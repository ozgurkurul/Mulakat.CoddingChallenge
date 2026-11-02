# 📩 Kees'in Teknik Sorularına Yanıtlar (Doğal, İnsan Diline Yakın Senior Backend Dili)

> **Neler Değişti?**
> - Yapay zekâ kokan akademik/ağır kelimeler (*"deterministic"*, *"periodic reconciliation to fix drift"*, *"facilitate"*, uzun tireler `—`) tamamen temizlendi.
> - Bir Türk Senior Backend geliştiricinin gerçekten kendi klavyesinden yazacağı **sade, net, samimi ama teknik olarak %100 dolu (B2/C1)** bir mühendis İngilizcesiyle yeniden yazıldı.
> - Kees'in istediği bütün alt sorulara (Redis çökerse ne olur, Lua/Unique constraint, DeFacto'da senin kişisel rolün, API timeout sonrası kontrol, Kafka consumer çökmesi) doğrudan ve net cevap verildi.

---

## 🇬🇧 BÖLÜM 1: Kees'e Gönderilecek Doğal İngilizce E-Posta Metni

Hi Kees,

Thank you for your email and for giving me the chance to explain these points in writing. During the call, it was a bit hard for me to express all the low-level flow details on the spot, so writing them down helps a lot.

Here are the answers based on what we actually ran in production and what I personally built:

---

**1. Checkout idempotency - Yemeksepeti**

In the Yemeksepeti Mahalle project, I personally built the Order and Campaign microservices using .NET, PostgreSQL, and Redis. To make sure only one order is created, we used **both Redis atomic operations and a PostgreSQL unique constraint** together:

* **10 simultaneous requests & Redis:** When the user clicked checkout on the mobile app, the request included an `Idempotency-Key` (linked to the `BasketId`). Before running any checkout code, our .NET service ran an atomic Redis command: `SET key "processing" NX EX 60` (and we used a small Lua script to safely release the lock only if the current pod owned it). Because Redis is single-threaded, even if 10 identical requests hit 10 different pods at the exact same millisecond, only the first one gets `OK` and continues. The other 9 requests get `false` and we immediately return `409 Conflict` (or return the created order if it already finished).
* **Database Unique Constraint (Final Guard):** We never trusted Redis alone for order creation. In our PostgreSQL `orders` table, we also had a `UNIQUE` index on `basket_id` (and `idempotency_key`). Even if two requests somehow passed Redis, PostgreSQL only allows the first `INSERT` to succeed. The second one throws a unique constraint error (`23505`), which we catch in C# and return the existing order.
* **What if Redis is unavailable or the key expires?** If Redis was down, our service logged a warning and fell back directly to PostgreSQL. And if the Redis key expired later, it wasn't a problem because the order was already saved permanently in PostgreSQL with that unique `basket_id` (and the basket status was changed to `Completed`). So any late retry was still blocked by the database.

---

**2. Inventory synchronization - DeFacto**

* **My personal role:** At DeFacto, different teams owned different parts of the system. A separate ERP/Warehouse team managed the physical store-to-ERP stock sync engine. My personal responsibility was on the **Omnichannel Order Management (OMS), Marketplace, and Cargo/Return integration team**. I worked on consuming stock availability, allocating orders to stores or warehouses, reserving stock when an order came in, and syncing order/return statuses back to the ERP and marketplaces.
* **Preventing overselling on the last item:**
  1. **Atomic SQL update & RowVersion:** When reserving stock for an order, we didn't read the stock into memory and update it later. We updated the reserved stock directly in SQL with a condition (`UPDATE Stock SET ReservedQty = ReservedQty + @qty WHERE Sku = @sku AND (PhysicalQty - ReservedQty) >= @qty`) together with optimistic locking (`RowVersion`). If only 1 item was left, only the first transaction updated the row (`1 row affected`), and the second one got `0 rows affected`.
  2. **Store re-allocation (Omnichannel fallback):** Since DeFacto shipped orders from both central warehouses and hundreds of retail stores, if the last item in Store A was just taken by another customer at that millisecond (or was found damaged during picking), our allocation service automatically reassigned the order to Store B that still had stock, instead of cancelling the customer's order.
  3. **Safety buffer for marketplaces:** When sending stock numbers to external marketplaces, we kept a small safety buffer (for example, holding back 1-2 items on fast-selling products) so sync delays wouldn't cause overselling.
* **When the ERP was down & Reconciliation:** Order creation worked asynchronously over message queues. If the ERP was temporarily down, our OMS continued taking orders using its local stock/reservation tables and queued the ERP updates. When the ERP came back online, the queued messages were processed. We also had scheduled background jobs that compared the ERP's physical stock snapshots with our OMS reservations and fixed any differences.

---

**3. External API failures - order processing**

When our backend sends a `POST /orders` request to an external platform and gets a timeout, we don't know if the request failed on the way there, or if the external system created the order and the response timed out on the way back.

* **How to check and retry safely:** I handle this in two ways depending on what the external API supports:
  1. **Using an Idempotency Key / External Reference ID:** Before calling the external API, we save our own `OrderId` (GUID) in our database with status `PendingSync`, and send it in the `Idempotency-Key` header or `externalOrderId` field. When our background worker retries with exponential backoff, the external platform sees the same ID and returns/updates the existing order instead of creating a second one.
  2. **Querying before retrying (Read-Before-Retry):** If the external API doesn't support idempotency headers, our retry worker does **not** blindly send `POST` again. First, it calls `GET /orders?externalOrderId={ourOrderId}` to check if the order is already there. If it exists, we save their remote ID, mark our DB record as `Synced`, and stop. If it returns `404 Not Found`, we safely send the `POST` request again.
* **My production experience:** Yes, I personally implemented this in production:
  * A concrete example is transferring **Yemeksepeti orders to Delivery Hero's central Datafridge system** (and partner POS/ERP APIs). When sending high-volume order payloads to Datafridge, network timeouts happened regularly, and creating duplicate order records was unacceptable because Datafridge is used for financial and order reporting. We sent our unique Yemeksepeti `OrderId` as the reference key and used queue-based retries (AWS SQS) with status checks so that timed-out requests could be safely retried without ever duplicating an order in Datafridge. If an order still failed after max retries, it went to a Dead-Letter Queue (DLQ) for inspection.
  * Earlier at **Novartz**, I also built this same "check by our reference number before retrying" logic when integrating bank payment gateways and LOGO ERP invoice creation.

---

**4. Transactional outbox and Kafka - Drivee**

* **Keeping DB changes and events consistent:** If a service updates PostgreSQL and then tries to publish to Kafka directly in C# code, the app might crash or Kafka might fail right after the DB commit (dual-write problem). To solve this at Drivee, we save both the business data (like an alarm or vehicle update) and the outgoing event (`outbox_messages` table with a unique `event_id` UUID and JSON payload) inside **one single PostgreSQL transaction (`BEGIN ... COMMIT`)**. Either both are saved, or both roll back. Then, **Debezium (CDC)** reads PostgreSQL's WAL logs and pushes new rows from `outbox_messages` to the Kafka topic.
* **What if a consumer updates its DB and crashes before acknowledging Kafka?** Since Kafka + Debezium works as *at-least-once* delivery, Kafka will send the same message again when the consumer restarts. To prevent processing it twice, we created a `processed_events` (Inbox) table in the consumer's PostgreSQL database with a `UNIQUE` primary key on `event_id`.
  When our .NET consumer processes a message, it updates the business table **and** inserts the `event_id` into `processed_events` in **the same DB transaction**. Only after `COMMIT` succeeds do we manually commit the Kafka offset (`EnableAutoCommit = false`). If the app crashes right before committing the Kafka offset, it gets the message again on restart, sees that `event_id` is already in `processed_events`, skips the business logic, and just commits the offset.
* **What I personally built:** At Drivee, I designed and coded this entire pipeline myself from scratch. I created the PostgreSQL `outbox_messages` and `processed_events` tables, wrote the .NET 10 / EF Core logic to save events in the same transaction, set up Kafka and the Debezium PostgreSQL connector on Docker, and wrote the idempotent .NET consumer services.

---

Please let me know if everything is clear now or if you'd like me to explain any part in more detail.

Best regards,
Özgür

---
---

## 🇹🇷 BÖLÜM 2: Cevapların Türkçe Karşılığı (Neyi Nasıl Söyledik?)

### Giriş
> *"Merhaba Kees, e-postan ve bu noktaları yazılı olarak açıklama fırsatı verdiğin için teşekkürler. Görüşme sırasında tüm alt seviye akış detaylarını anlık olarak ifade etmek benim için biraz zordu, bu yüzden yazılı anlatmak çok yardımcı oluyor."*
*(Bu cümle çok insani ve samimi; görüşmedeki dil heyecanını çok doğal açıklıyor).*

### 1. Yemeksepeti — Checkout Idempotency
- **Ne dedik?** Yemeksepeti Mahalle'de Sipariş ve Kampanya mikroservislerini .NET, PostgreSQL ve Redis ile bizzat kurdum. Tek sipariş oluşmasını garantilemek için **hem Redis atomik komutunu hem de PostgreSQL Unique Index'i** birlikte kullandık:
  1. **10 eşzamanlı istek ve Redis:** Kullanıcı checkout'a bastığında istek `BasketId`'ye bağlı bir `Idempotency-Key` ile geliyordu. .NET servisimiz önce Redis'te `SET key "processing" NX EX 60` çalıştırıyordu (kilidi güvenli bırakmak için de küçük bir Lua scripti kullandık). Redis tek thread çalıştığı için aynı milisaniyede 10 farklı pod'a 10 istek gelse bile sadece ilki `OK` alır ve devam eder. Diğer 9'u `false` alır ve hemen `409 Conflict` döneriz.
  2. **Veritabanı Unique Constraint (Son Güvence):** Sipariş için asla tek başına Redis'e güvenmedik. PostgreSQL `orders` tablosunda `basket_id` üzerinde `UNIQUE` index vardı. İki istek bir şekilde Redis'i geçse bile PostgreSQL sadece ilk `INSERT`e izin verir, ikincisi `23505` unique hatası fırlatır; bunu C#'ta yakalayıp mevcut siparişi döneriz.
  3. **Redis çökerse veya key süresi (TTL) dolarsa?** Redis'e ulaşılamazsa servisimiz log basıp doğrudan PostgreSQL'e devam ediyordu. Key süresi dolsa bile sipariş PostgreSQL'de o unique `basket_id` ile kalıcı olarak kayıtlı olduğu (ve sepet durumu `Completed` olduğu) için sonradan gelen tekrar istekleri yine veritabanı tarafından engelleniyordu.

### 2. DeFacto — Stok Senkronizasyonu
- **Ne dedik?**
  - **Benim kişisel rolüm:** DeFacto'da farklı alanlara farklı ekipler bakıyordu. Fiziksel mağaza-ERP stok senkronizasyon motorunu ayrı bir ERP/Depo ekibi yönetiyordu. Benim kişisel sorumluluğum **Omnichannel Sipariş Yönetimi (OMS), Pazaryeri ve Kargo/İade entegrasyonu** ekibindeydi. Stok uygunluğunu çekmek, siparişleri mağaza veya depolara atamak, sipariş geldiğinde stok rezerve etmek ve sipariş/iade durumlarını ERP ile pazaryerlerine geri bildirmek üzerinde çalıştım.
  - **Son 1 üründe overselling'i önleme:**
    1. **Atomik SQL Update & RowVersion:** Stoğu belleğe okuyup sonra güncellemedik; doğrudan SQL'de koşulla (`WHERE (PhysicalQty - ReservedQty) >= @qty`) ve `RowVersion` (optimistic locking) ile güncelledik. Son 1 ürün kaldıysa sadece ilk işlem `1 satır` günceller, ikincisi `0 satır` alır.
    2. **Mağaza yeniden atama (Omnichannel fallback):** DeFacto hem merkez depodan hem yüzlerce mağazadan kargo çıkabildiği için, A mağazasındaki son ürün o salisede başkasına satıldıysa (veya rafta hasarlı çıktıysa) sipariş iptal edilmez, algoritmamız siparişi otomatik olarak stoğu olan B mağazasına yönlendirirdi.
    3. **Pazaryeri güvenlik stoğu (Safety buffer):** Pazaryerlerine stok gönderirken çok satan ürünlerde 1-2 adet güvenlik payı düşerek gönderirdik.
  - **ERP çökerse ve Mutabakat (Reconciliation):** Siparişler mesaj kuyruklarıyla asenkron çalışıyordu. ERP geçici olarak çökerse OMS kendi lokal stok/rezervasyon tablolarıyla sipariş almaya devam eder, ERP mesajlarını kuyrukta bekletirdi. ERP gelince kuyruk işlenirdi. Ayrıca zamanlanmış background job'lar periyodik olarak ERP fiziksel stoğu ile OMS rezervasyonlarını karşılaştırıp farkları düzeltirdi.

### 3. Dış API Hataları — Sipariş İşleme (Timeout & Retry)
- **Ne dedik?** Dış bir platforma `POST /orders` attığımızda timeout alırsak, istek giderken mi koptu yoksa sipariş oluştu da cevabı dönerken mi koptu bilemeyiz. Karşı API'nin desteğine göre iki yöntem uyguladım:
  1. **Idempotency Key / External Reference ID kullanımı:** İsteği atmadan önce kendi `OrderId` (GUID) değerimizi veritabanımıza `PendingSync` olarak kaydeder ve karşıya `Idempotency-Key` header'ı veya `externalOrderId` alanı olarak göndeririz. Worker'ımız tekrar denediğinde (retry) karşı sistem aynı ID'yi görür ve ikinci bir sipariş açmak yerine mevcut siparişi döner/günceller.
  2. **Retry öncesi sorgulama (Read-Before-Retry):** Karşı API idempotency header desteklemiyorsa, worker'ımız körü körüne tekrar `POST` atmaz. Önce `GET /orders?externalOrderId={bizimOrderId}` ile sipariş oluşmuş mu diye sorar. Oluşmuşsa karşı tarafın ID'sini alıp bizdeki kaydı `Synced` yapar ve durur. `404 Not Found` dönerse güvenle tekrar `POST` atar.
  - **Canlı ortam deneyimim (Datafridge Örneği):** Bunu canlıda bizzat uyguladım: En somut örneği **Yemeksepeti siparişlerinin Delivery Hero'nun merkezi Datafridge sistemine** (ve partner POS/ERP API'lerine) aktarılmasıydı. Yüksek hacimli siparişleri Datafridge'e gönderirken sık sık network timeout yaşanabiliyordu ve Datafridge finansal/sipariş raporlaması için kullanıldığından mükerrer sipariş oluşması kesinlikle kabul edilemezdi. Yemeksepeti `OrderId`'mizi referans anahtarı olarak gönderip AWS SQS tabanlı retry ve durum kontrolü (status check) kullandık; böylece timeout alan istekler Datafridge'de çift kayıt yaratmadan güvenle tekrar denendi. Maksimum denemeden sonra hâlâ hata alanlar inceleme için DLQ'ya (Dead-Letter Queue) düştü. Daha önce **Novartz**'ta da banka ödeme sistemleri ve LOGO ERP fatura entegrasyonlarında aynı mantığı yazmıştım.

### 4. Transactional Outbox ve Kafka — Drivee
- **Ne dedik?**
  - **DB ve event tutarlılığı:** Servis PostgreSQL'i güncelleyip ardından C# kodunda doğrudan Kafka'ya mesaj atmaya çalışırsa, DB commit'ten hemen sonra uygulama çökebilir veya Kafka hata verebilir (dual-write problemi). Bunu çözmek için Drivee'de hem iş verisini (alarm veya araç durumu) hem de gidecek event'i (`outbox_messages` tablosuna unique `event_id` ve JSON ile) **tek bir PostgreSQL transaction'ı (`BEGIN ... COMMIT`)** içinde kaydediyoruz. Ya ikisi birden yazılır ya ikisi birden geri alınır. Sonra **Debezium (CDC)** PostgreSQL WAL loglarını okuyup yeni kayıtları Kafka'ya basar.
  - **Consumer kendi DB'sini güncelleyip Kafka'ya Ack dönmeden çökerse?** Kafka + Debezium *at-least-once* çalıştığı için consumer yeniden başladığında Kafka aynı mesajı tekrar gönderir. İki kez işlenmesini önlemek için consumer'ın PostgreSQL veritabanında `event_id` alanı `UNIQUE` primary key olan bir `processed_events` (Inbox) tablosu açtık. .NET consumer mesajı işlerken iş tablosu güncellemesini **ve** `processed_events` tablosuna `event_id` ekleme işlemini **aynı DB transaction'ı içinde** yapar. Sadece `COMMIT` başarılı olduktan sonra Kafka offset'ini manuel olarak commit ederiz (`EnableAutoCommit = false`). Offset commit'ten hemen önce çökerse, yeniden başladığında mesaj tekrar gelir, `event_id`'nin `processed_events` içinde zaten var olduğunu görür, iş mantığını atlar ve sadece offset'i commit eder.
  - **Benim bizzat yaptığım kısım:** Drivee'de bu yapının tamamını sıfırdan ben tasarladım ve kodladım (PostgreSQL tabloları, .NET 10 / EF Core transaction yapısı, Docker üzerinde Kafka ve Debezium kurulumu ve idempotent .NET consumer servisleri).
