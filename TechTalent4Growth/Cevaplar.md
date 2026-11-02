# 📩 Kees'in Teknik Netleştirme Sorularına Yanıtlar (Yemeksepeti, DeFacto, Drivee)

> **Amaç:** Kees'in e-postasındaki 4 soruya **uzatmadan, net, somut üretim (production) örnekleriyle** ve **senin bizzat yaptığın kısımları dürüstçe belirterek** yanıt vermek.
> **Kees'in Notu:** *"There's no need for lengthy answers. Clear explanations with concrete examples are more valuable. Where something is theoretical rather than something you personally built, please indicate that."*

---

## 🇬🇧 BÖLÜM 1: Kees'e Gönderilecek Hazır İngilizce E-Posta Metni

*(Aşağıdaki metni kopyalayıp doğrudan Kees'e gönderebilirsin. Çok net, samimi ve bir Senior Engineer'ın kendi kaleminden çıkmış doğallıktadır.)*

---

Hi Kees,

Thank you for your feedback and for giving me the opportunity to clarify these points in writing. Sometimes it is much easier to explain distributed system mechanics in structured text than on a first call, so I really appreciate this.

Below are the concrete details from my production experience for each of your four questions, including what I personally built.

---

### 1. Checkout Idempotency — Yemeksepeti (Yemeksepeti Mahalle)

* **How we guaranteed only one order is created across 10 simultaneous requests:**
  We used a **two-layer defense** (Redis as a fast distributed lock + PostgreSQL as the final source of truth):
  1. **Layer 1 — Redis Atomic Operation (`SET NX`):** When the mobile app initiated checkout, it sent a deterministic `Idempotency-Key` (tied to the `BasketId` / checkout attempt). Before running any checkout logic, the .NET service executed an atomic Redis command:
     `SET idempotency:checkout:{key} "IN_PROGRESS" NX EX 60`
     Because Redis executes commands single-threadedly, if 10 identical requests hit 10 different Kubernetes pods at the exact same millisecond, **only the first request receives `OK`** and proceeds. The other 9 requests receive `false/null` and immediately return `409 Conflict (Checkout already in progress)` or the cached result once completed.
  2. **Layer 2 — PostgreSQL Unique Constraint:** We never relied on Redis alone for order creation. In our PostgreSQL `orders` table, we had a **`UNIQUE` index on `basket_id` / `idempotency_key`**. Even if two requests ever bypassed Redis, PostgreSQL's ACID transaction guaranteed that only one `INSERT` succeeded, while the second threw a unique constraint violation (`23505`), which we caught to return the existing order.

* **What happens if Redis becomes unavailable or the key expires?**
  * **If Redis is unavailable:** Our code failed open to the database (logged a warning and proceeded to PostgreSQL).
  * **If the Redis key expires (or Redis is down):** PostgreSQL acts as the permanent guard. Once an order is created, the `idempotency_key` / `basket_id` exists permanently in the `orders` table (and the basket status is marked as `Completed`). Any late retry hits the PostgreSQL unique check and returns the already created order instead of creating a duplicate.

* **My personal involvement:** I personally developed the .NET Order and Campaign microservices with PostgreSQL and Redis at Yemeksepeti Mahalle and implemented this idempotency flow.

---

### 2. Inventory Synchronization — DeFacto

* **My personal involvement (Team Boundaries):**
  To be transparent about responsibilities in a large organization like DeFacto: a separate ERP/Warehouse team owned the core inventory master and physical store stock counts. **My personal responsibility** was on the **Omnichannel Order Management (OMS), Marketplace, and Cargo/Return integration side**—consuming stock availability, allocating orders across warehouses/stores, reserving stock during order creation, and syncing order/return statuses back to the ERP and marketplaces.

* **How we prevented overselling when two customers ordered the last item simultaneously:**
  1. **Atomic Database Reservation:** When reserving stock in the order allocation database, we never did "read in memory, then update." We used atomic conditional SQL updates (`UPDATE Stock SET ReservedQty = ReservedQty + @qty WHERE Sku = @sku AND (PhysicalQty - ReservedQty) >= @qty`) combined with optimistic concurrency (`RowVersion`). If only 1 item was left, only the first transaction affected 1 row; the second affected 0 rows.
  2. **Omnichannel Store Re-allocation:** Because DeFacto fulfilled orders from both central warehouses and hundreds of retail stores, if the last item in Warehouse A was taken by another customer at that millisecond (or turned out to be damaged during picking), our allocation engine automatically re-routed the order to another store that had the item in stock, preventing order cancellation.
  3. **Marketplace Safety Buffer:** For external marketplaces, we published `Sellable Stock = Available Stock - Safety Buffer` (holding back a small buffer on fast-moving items) to prevent overselling during marketplace sync intervals.

* **What happened when the ERP was temporarily unavailable & Reconciliation:**
  * Order placement was decoupled from the ERP using asynchronous message queues. If the ERP was temporarily down, the OMS continued accepting orders against its local stock/reservation state and queued the ERP sync messages.
  * When the ERP came back online, queued order and stock events were processed asynchronously. In addition, scheduled **reconciliation jobs** periodically compared ERP physical stock snapshots with OMS reservations to fix any drift.

---

### 3. External API Failures — Order Processing (Timeouts & Retries)

* **How to determine if the external system already created the order after a timeout, and how to retry safely:**
  When an HTTP `POST /orders` call times out, we don't know if the request failed before reaching the server or if the order was created and only the response was lost. I handle this in two ways depending on what the external API supports:
  1. **If the external API supports Idempotency / Client Reference IDs:** Before calling the external API, we generate and save our own deterministic `ExternalReferenceId` (e.g., our internal `OrderId` GUID) in our database with status `PENDING_SYNC`. We pass this ID in the `Idempotency-Key` header or `client_reference_id` field. When our background worker retries with exponential backoff, the external system sees the same ID and returns the existing order without duplicating it.
  2. **If the external API does NOT support Idempotency headers (Read-Before-Retry):** Before sending a retry `POST`, our recovery worker first queries the external API (`GET /orders?externalReference={ourOrderId}`).
     * If the order exists on their side → we save their remote Order ID, mark our record as `SYNCED`, and skip the `POST`.
     * If it returns `404 Not Found` → we safely retry the `POST`.

* **My personal production experience:**
  Yes, I personally implemented both in production:
  * At **Delivery Hero (Integration Hub)**, our .NET and Go workers pushed orders from AWS SQS queues to hundreds of external partner POS/ERP APIs where timeouts were frequent. For modern partners, we enforced `external_order_id` idempotency in our API contract; for legacy partners, I implemented the **Check-Status-Before-Retry** flow and routed permanent failures to a Dead-Letter Queue (DLQ).
  * Earlier at **Novartz**, I built this exact "query by reference ID before retrying" mechanism when integrating bank payment gateways (PSPs) and LOGO ERP invoice creation.

---

### 4. Transactional Outbox and Kafka — Drivee

* **How we ensured database changes and outgoing events remained consistent:**
  To avoid the **Dual-Write problem** (where the DB commit succeeds but publishing to Kafka fails), our .NET services never publish directly to Kafka during a database transaction.
  Instead, inside a **single PostgreSQL ACID transaction (`BEGIN ... COMMIT`)**, we:
  1. Insert/update the business entity (e.g., vehicle state or alarm record), and
  2. Insert the event payload (with a unique `event_id` UUID, `aggregate_id`, `event_type`, and JSON payload) into an `outbox_messages` table.
  Because both writes happen in the same local transaction, they either both commit or both roll back. Then, **Debezium (CDC)** reads PostgreSQL’s Write-Ahead Log (WAL) and streams new rows from `outbox_messages` into the appropriate **Kafka** topic.

* **How we prevented duplicate processing when a consumer crashed before acknowledging (offset commit):**
  Debezium + Kafka provides **at-least-once** delivery, meaning if a consumer updates its database and crashes before committing the Kafka offset, Kafka will redeliver that message on restart.
  To make our .NET consumers **idempotent**, we implemented an **Inbox (`processed_events`) table** in PostgreSQL with a `UNIQUE` primary key on `event_id`:
  * When a consumer processes a message, it writes the business update **and** `INSERT INTO processed_events (event_id, processed_at)` inside **one single database transaction**, and only then manually commits the Kafka offset (`EnableAutoCommit = false`).
  * If the consumer crashes right before committing the Kafka offset, it receives the message again on restart, checks/inserts `event_id` into `processed_events`, sees it was already processed in the previous transaction, skips the business logic, and commits the offset safely.

* **Which parts I personally designed and implemented:**
  At Drivee, **I personally designed and implemented this architecture end-to-end**. I designed the PostgreSQL schemas (`outbox_messages` and `processed_events`), wrote the .NET 10 / EF Core unit-of-work and Outbox interceptor, configured the Dockerized Kafka and Debezium PostgreSQL connector, and wrote the idempotent .NET consumer workers (which power our real-time SignalR alerts and cold-chain notifications).

---

Please let me know if any of these points need further detail. I'm happy to elaborate!

Best regards,
**Özgür Kurul**

---
---

## 🇹🇷 BÖLÜM 2: Cevapların Türkçe Teknik Özeti (Senin İçin Rehber)

*(Yukarıdaki İngilizce metinde tam olarak ne anlattığımızın ve bir sonraki teknik mülakatta CTO / Engineering Lead sorarsa nasıl savunacağının Türkçe özeti)*

### 1. Yemeksepeti — Checkout Idempotency (Çift Siparişi Önleme)
- **Neden tek başına Redis yetmez?** Kees çok güzel bir tuzak soru sormuş: *"Redis çökerse veya TTL süresi dolarsa ne olur?"*
- **Bizim Cevabımız (Çift Katmanlı Savunma):**
  1. **1. Katman (Redis `SET key "IN_PROGRESS" NX EX 60`):** `NX` (Not Exists) parametresi Redis'te atomiktir ve Redis tek thread çalıştığı için aynı milisaniyede 10 farklı sunucuya (pod'a) gelen 10 istekten **sadece ilki** `OK` alır. Diğer 9 istek anında reddedilir (`409 Conflict`).
  2. **2. Katman (PostgreSQL `UNIQUE` Constraint):** Sipariş tablosunda `basket_id` / `idempotency_key` üzerinde **Unique Index** vardır.
  3. **Redis çökerse veya TTL biterse ne olur?** Uygulama Redis'e ulaşamazsa veritabanına düşer (fail-open). Kalıcı doğruluk kaynağı PostgreSQL olduğu için, Redis ayakta olmasa bile veya Redis'teki key silinmiş olsa bile PostgreSQL'deki `UNIQUE` constraint aynı sepetin/anahtarın ikinci kez siparişe dönüşmesini **%100 engeller**.

### 2. DeFacto — Stok Senkronizasyonu ve Overselling
- **Dürüst Sınır Çizimi (Kees'in en çok puan vereceği yer):** DeFacto gibi dev bir şirkette mağaza içi fiziksel ERP stok yazılımını tek başına bir kişinin yazmadığını dürüstçe söyledik: *"Fiziksel mağaza/ERP stok motorunu ERP ekibi yönetiyordu; benim kişisel sorumluluğum Omnichannel Sipariş Yönetimi (OMS), Mağaza/Depo Atama (Allocation), Pazaryeri ve Kargo/İade entegrasyonlarıydı."*
- **Son 1 Ürünü İki Kişi Aynı Anda Alırsa (Overselling Önleme):**
  1. **Atomik SQL Güncellemesi + Optimistic Locking (`RowVersion`):** Belleğe çekip kontrol etmek yerine veritabanında `WHERE (PhysicalQty - ReservedQty) >= @qty` koşuluyla atomik `UPDATE` yapılır. Sadece 1 kişi güncelleyebilir.
  2. **Omnichannel Mağaza Yönlendirmesi (Re-allocation):** DeFacto'da yüzlerce mağaza aynı zamanda depo gibi çalıştığı için, A deposundaki son ürün o milisaniyede biterse veya defolu çıkarsa sipariş iptal edilmez; algoritma siparişi otomatik olarak stoğu olan B mağazasına yönlendirir.
  3. **Pazaryeri Güvenlik Stoğu (Safety Buffer):** Pazaryerlerine stok gönderirken son 1-2 adet güvenlik payı olarak düşülür.
- **ERP Çökerse:** Siparişler mesaj kuyruğuna (Queue) alınır, OMS kendi lokal rezervasyon tablosuyla sipariş almaya devam eder. ERP gelince kuyruk boşaltılır ve periyodik **Reconciliation (Mutabakat)** job'ları ile ERP-OMS stok farkları eşitlenir.

### 3. Dış API Zaman Aşımı (Timeout) ve Güvenli Retry
- **Problem:** Dış e-ticaret sistemine `POST /orders` attık ama `Timeout` aldık. Sipariş karşıda oluştu mu oluşmadı mı bilmiyoruz.
- **Çözüm (2 Senaryo):**
  1. **Karşı API `Idempotency-Key` veya `ExternalReferenceId` destekliyorsa:** İsteği atmadan önce kendi sipariş GUID'imizi kaydedip karşıya göndeririz. Retry yaptığımızda karşı sistem aynı ID'yi görüp mükerrer kayıt açmaz.
  2. **Karşı API desteklemiyorsa (Read-Before-Retry):** Retry worker'ımız tekrar `POST` atmadan önce karşı sisteme `GET /orders?externalReference={bizimId}` diye sorar. Kayıt varsa ID'sini alıp `SYNCED` işaretler; `404` dönerse güvenle tekrar `POST` atar.
- **Senin Canlı Deneyimin:** Hem **Delivery Hero Integration Hub**'da (POS/ERP entegrasyonlarında) hem de **Novartz**'ta (LOGO ERP ve Banka POS entegrasyonlarında) bizzat uyguladın.

### 4. Drivee — Transactional Outbox, Debezium ve Kafka
- **DB ve Event Tutarlılığı:** `.NET` servisi doğrudan Kafka'ya yazmaz. Aynı PostgreSQL transaction'ı (`BEGIN ... COMMIT`) içinde hem asıl tabloyu günceller hem de `outbox_messages` tablosuna INSERT yapar. **Debezium (CDC)** PostgreSQL WAL loglarını okuyup mesajı Kafka'ya basar.
- **Consumer DB'yi Güncelledi Ama Kafka'ya Ack Göndermeden Çöktü (Kritik Soru!):**
  - Consumer tekrar ayağa kalktığında Kafka aynı mesajı tekrar gönderir.
  - Çift işlenmeyi önlemek için Consumer tarafında PostgreSQL'de **`processed_events` (Inbox) tablosu** vardır (`event_id` Primary Key / Unique).
  - Consumer iş mantığını (business update) ve `INSERT INTO processed_events (event_id)` işlemini **tek bir DB transaction'ı içinde** yapar, commit olduktan sonra Kafka offset'ini manuel commit eder (`EnableAutoCommit = false`).
  - Eğer offset commit'ten hemen önce çökerse, mesaj tekrar geldiğinde `event_id` zaten `processed_events` tablosunda olduğu için işlemi atlar ve sadece offset'i commit eder.
- **Senin Rolün:** Drivee'de bu mimariyi (PostgreSQL şeması, .NET 10 EF Core Outbox yapısı, Docker Kafka + Debezium kurulumu ve idempotent consumer'lar) **uçtan uca bizzat sen tasarladın ve kodladın**.

