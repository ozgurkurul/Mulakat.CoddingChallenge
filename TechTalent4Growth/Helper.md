# 🛠️ Wovar (TechTalent4Growth) — Teknik İngilizce & Mimari Konuşma Rehberi (`Helper.md`)

> **Harika Haber:** Kees mesajında müşterinin adını (**Wovar**) paylaştı! İlk mesajında *"After the 1st interview you get the name (when we present you)"* demişti. Bu, gönderdiğin yazılı teknik cevapların kabul edildiği ve profilinin **Wovar**'ın Engineering Lead ve CTO'suna sunulduğu anlamına geliyor.
>
> **Bu Dosyanın Amacı:** Wovar'ın teknik lideriyle (Engineering Lead) yapacağın görüşmede, Kees'in özellikle belirttiği **5 ana teknik alanı**:
> 1. **Checkout and Idempotency** (Ödeme/Sipariş ve Tekillik)
> 2. **Concurrency and Database Transactions** (Eşzamanlılık ve Veritabanı İşlemleri)
> 3. **Inventory Synchronization** (Stok Senkronizasyonu)
> 4. **Kafka and Event-Driven Architecture** (Kafka ve Olay Güdümlü Mimari)
> 5. **Failure Recovery** (Hata Kurtarma ve Dayanıklılık)
>
> tek tek kelimelerle değil, **tam ve doğal İngilizce cümlelerle** ve **`Problem → Solution → Reason → Result`** yapısıyla rahatça anlatabilmeni sağlamak.

---

## 📑 İçindekiler

0. [Müşteri Analizi (Wovar) ve "Problem → Solution → Reason → Result" Formülü](#0-müşteri-analizi-wovar-ve-problem--solution--reason--result-formülü)
1. [Alan 1: Checkout and Idempotency (Sipariş & Çift İşlem Önleme)](#1-alan-1-checkout-and-idempotency-sipariş--çift-işlem-önleme)
2. [Alan 2: Concurrency and Database Transactions (Eşzamanlılık & Veritabanı İşlemleri)](#2-alan-2-concurrency-and-database-transactions-eşzamanlılık--veritabanı-işlemleri)
3. [Alan 3: Inventory Synchronization (Stok Senkronizasyonu & Overselling)](#3-alan-3-inventory-synchronization-stok-senkronizasyonu--overselling)
4. [Alan 4: Kafka and Event-Driven Architecture (Kafka, Outbox & Inbox)](#4-alan-4-kafka-and-event-driven-architecture-kafka-outbox--inbox)
5. [Alan 5: Failure Recovery & Resilience (Hata Kurtarma, Timeout, DLQ & Mutabakat)](#5-alan-5-failure-recovery--resilience-hata-kurtarma-timeout-dlq--mutabakat)
6. [Engineering Lead'in Sorabileceği 8 "Neden?" (Trade-Off) Sorusu](#6-engineering-leadin-sorabileceği-8-neden-trade-off-sorusu)
7. [Her Gün Sesli Okunacak "30 Altın Tam Cümle" (Ezber Bozan Özet Liste)](#7-her-gün-sesli-okunacak-30-altın-tam-cümle-ezber-bozan-özet-liste)

---

## 0. Müşteri Analizi (Wovar) ve `Problem → Solution → Reason → Result` Formülü

### 🏢 Müşteri Kimdir? — Wovar (`wovar.com`)
- **Ne Yaparlar?** Hollanda merkezli, Avrupa'nın en hızlı büyüyen DIY (Do-It-Yourself) yapı market / hırdavat / bahçe donanımı e-ticaret markasıdır (vidalar, cıvatalar, pergola bağlantı demirleri, kapı/pencere donanımları vb. 8.000+ ürün).
- **Senin Büyük Avantajın:** Kariyerinin başında uçtan uca kurduğun **MetalAVM** ile birebir aynı ürün grubu (metal/hırdavat/yapı market)! Bunu görüşmede *"I actually built an e-commerce platform called MetalAVM for hardware and metal products in the past, so Wovar's product domain feels like home to me"* diyerek mutlaka kullan.
- **Wovar'ın Mimarisi:**
  - **Webshop:** BigCommerce + React/Redux/Tailwind
  - **Merkez:** **Custom C# / .NET Integration Layer + PostgreSQL + Message Queues** (Senin çalışacağın yer!)
  - **Bağlı Sistemler:** NetSuite (ERP), Akeneo (PIM), Prismic (Headless CMS), Channable (Pazaryeri/Feed), Klaviyo (Marketing), PSPs (Ödeme).

---

### 🧩 Kees'in Önerdiği Konuşma Kalıbı: `Problem → Solution → Reason → Result`

Engineering Lead sana bir soru sorduğunda uzun ve dağınık paragraflar kurmak yerine **her cevabını şu 4 kısa cümleyle** ver:

| Adım | Ne Söyleyeceksin? | Cümleye Başlama Kalıbı (İngilizce) |
|---|---|---|
| **1. Problem** | Hangi teknik riski veya hatayı çözmeye çalışıyorduk? | *"The problem was that..."* / *"When [X happens], there is a risk of..."* |
| **2. Solution** | Bunu hangi somut mimari/kod ile çözdük? | *"To solve this, we implemented..."* / *"We used [X] together with [Y]..."* |
| **3. Reason** | Neden başka bir yöntemi değil de bunu seçtik? | *"The reason we chose this approach is that..."* / *"Because..."* |
| **4. Result** | Canlı ortamda (production) sonucu ne oldu? | *"As a result, we prevented [X] and guaranteed..."* |

---

## 1. Alan 1: Checkout and Idempotency (Sipariş & Çift İşlem Önleme)

### 1.1. Derin Yazılım Bilgisi (Neyi Bilmelisin?)
- **Idempotency Nedir?** Bir işlemin 1 kez yapılması ile 10 kez yapılması arasında sistem durumu açısından fark olmamasıdır ($f(x) = f(f(x))$). E-ticarette kullanıcı "Öde" butonuna üst üste bastığında, mobil uygulama ağ koptuğu için isteği tekrarladığında veya ödeme sağlayıcı (PSP) aynı webhook'u 3 kez gönderdiğinde **sadece 1 sipariş ve 1 ödeme** oluşmalıdır.
- **Neden İki Katman (Redis + PostgreSQL) Kullanılır?**
  - **Redis (`SET key value NX EX 60`):** Hız ve ilk filtreleme katmanıdır. Aynı milisaniyede gelen 10 paralel isteğin 9'unu veritabanını hiç yormadan kapıda (`409 Conflict` ile) geri çevirir.
  - **PostgreSQL (`UNIQUE` Constraint):** Kalıcı doğruluk kaynağıdır (Source of Truth). Redis geçici bir önbellektir; çökebilir, restart olabilir veya TTL süresi (60 sn) dolabilir. PostgreSQL'deki `orders` tablosunda `basket_id` veya `idempotency_key` üzerindeki `UNIQUE` index, çift siparişi fiziksel olarak **imkansız** kılar.

### 1.2. Terimler ve "Tam Cümle" Sözlüğü (Turkish ↔ Complete English Sentences)

| Türkçe Kavram / Düşünce | Doğrudan Söyleyeceğin Tam İngilizce Cümle |
|---|---|
| Çift siparişleri önlemek için veritabanında tekillik kısıtı (unique constraint) kullanıyoruz. | **"We use a unique database constraint to prevent duplicate orders."** |
| İstemci her ödeme denemesi için benzersiz bir idempotency anahtarı gönderir. | **"The client sends a unique idempotency key for each checkout attempt."** |
| Aynı anda gelen eşzamanlı istekleri engellemek için Redis'te atomik `SET NX` komutu çalıştırıyoruz. | **"We run an atomic `SET NX` command in Redis to block simultaneous duplicate requests."** |
| Eğer Redis'te anahtar zaten varsa, hemen `409 Conflict` hatası dönüyoruz. | **"If the key already exists in Redis, we immediately return a `409 Conflict` response."** |
| Başka bir sunucunun kilidini yanlışlıkla silmemek için kilidi Lua scripti ile açıyoruz. | **"We release the Redis lock using a Lua script so a pod never deletes another pod's lock."** |
| Sipariş verisi için asla tek başına Redis'e güvenmiyoruz; son karar verici her zaman PostgreSQL'dir. | **"We never rely on Redis alone for order data; PostgreSQL is always our final source of truth."** |
| Eğer Redis çökerse, sistemimiz doğrudan PostgreSQL'deki unique index kontrolüne düşer. | **"If Redis becomes unavailable, our service falls back directly to the unique index in PostgreSQL."** |
| Veritabanı unique hatası fırlattığında bunu yakalayıp mevcut siparişi döneriz. | **"When the database throws a unique constraint violation, we catch it and return the existing order."** |

### 1.3. `Problem → Solution → Reason → Result` Konuşma Kartları

#### 🎯 Senaryo 1A: 10 Eşzamanlı Checkout İsteği (Yemeksepeti Mahalle)
- 🔴 **Problem:**
  - **EN:** *"During checkout, if a user double-clicks or the mobile app retries on a slow network, multiple identical requests can hit different backend pods at the exact same millisecond and create duplicate orders."*
  - *(TR: Ödeme sırasında kullanıcı çift tıklarsa veya mobil uygulama yavaş ağda tekrar denerse, aynı milisaniyede farklı sunuculara birden fazla aynı istek gelebilir ve çift sipariş oluşabilir.)*
- 🛠️ **Solution:**
  - **EN:** *"To solve this at Yemeksepeti, I implemented a two-layer check: first an atomic Redis `SET NX` lock with a 60-second TTL, and second a `UNIQUE` index on the `basket_id` and `idempotency_key` columns in our PostgreSQL `orders` table."*
  - *(TR: Bunu Yemeksepeti'nde çözmek için iki katmanlı bir kontrol kurdum: birincisi 60 saniye süreli atomik Redis `SET NX` kilidi, ikincisi ise PostgreSQL `orders` tablomuzda `basket_id` ve `idempotency_key` kolonları üzerinde bir `UNIQUE` index.)*
- 🧠 **Reason:**
  - **EN:** *"The reason we used both is that Redis is single-threaded and fast, so it blocks 9 out of 10 simultaneous requests in memory without hitting the database, while the PostgreSQL unique constraint protects us permanently if Redis goes down or the TTL expires."*
  - *(TR: İkisini birlikte kullanmamızın nedeni şu: Redis tek thread'li ve hızlı olduğu için 10 eşzamanlı istekten 9'unu veritabanına gitmeden bellekte durdurur; PostgreSQL unique kısıtı ise Redis çökerse veya TTL süresi dolarsa bizi kalıcı olarak korur.)*
- ✅ **Result:**
  - **EN:** *"As a result, even under heavy traffic with tens of thousands of daily orders, we guaranteed that each basket created exactly one order."*
  - *(TR: Sonuç olarak, günde on binlerce siparişlik yoğun trafik altında bile her sepetin tam olarak tek bir sipariş oluşturmasını garantiledik.)*

---

## 2. Alan 2: Concurrency and Database Transactions (Eşzamanlılık & Veritabanı İşlemleri)

### 2.1. Derin Yazılım Bilgisi (Neyi Bilmelisin?)
- **Lost Update (Kayıp Güncelleme) Problemi:** İki thread aynı anda veritabanından `Stock = 5` okur. İkisi de bellekte `5 - 1 = 4` hesaplar ve veritabanına `UPDATE Stock = 4` yazar. 2 ürün satılmıştır ama stok 3 yerine 4 görünür!
- **3 Çözüm Yöntemi ve Ne Zaman Hangisi Seçilir?**
  1. **Atomic Conditional Update (Atomik Koşullu Güncelleme — En Hızlı & En Temiz):**
     - Veriyi C# belleğine çekip hesaplamak yerine tek bir SQL komutu gönderilir:
       `UPDATE stock SET reserved_qty = reserved_qty + @qty WHERE sku = @sku AND (physical_qty - reserved_qty) >= @qty;`
     - Veritabanı bu satırı güncellerken kendi içinde anlık satır kilidi (row-level lock) alır. Koşul sağlanmazsa `0 rows affected` döner, eksi stoğa düşmek imkansızdır.
  2. **Optimistic Concurrency (İyimser Kilitleme — `RowVersion` / `xmin`):**
     - Tabloda bir `version` kolonu vardır. Okurken `version = 1` okunur, yazarken `WHERE id = @id AND version = 1` şartı konur ve `SET version = 2` yapılır. Başka biri araya girdiyse güncelleme başarısız olur (`DbUpdateConcurrencyException`). Çakışmanın (contention) düşük/orta olduğu yerlerde performanslıdır çünkü satırı okurken kilitlemez.
  3. **Pessimistic Locking (Kötümser Kilitleme — `SELECT ... FOR UPDATE`):**
     - Satır okunurken kilitlenir (`SELECT * FROM stock WHERE sku = @sku FOR UPDATE`). Transaction bitene kadar diğer tüm istekler bekler. Çok yüksek çakışma olan ve karmaşık çok adımlı kontrollerde kullanılır ama veritabanı bağlantılarını bekletir ve **Deadlock** riski yaratır (birden fazla satır kilitlenecekse her zaman `ORDER BY sku ASC` ile aynı sırada kilitlenmelidir!).

### 2.2. Terimler ve "Tam Cümle" Sözlüğü (Turkish ↔ Complete English Sentences)

| Türkçe Kavram / Düşünce | Doğrudan Söyleyeceğin Tam İngilizce Cümle |
|---|---|
| Kayıp güncellemeleri (lost updates) önlemek için veriyi bellekte okuyup sonradan güncellemiyoruz. | **"To prevent lost updates, we never read stock into memory and update it later."** |
| Stok miktarını doğrudan SQL içinde atomik bir koşulla güncelliyoruz. | **"We update the stock quantity directly in SQL using an atomic `WHERE` condition."** |
| Eğer etkilenen satır sayısı sıfırsa, bu başka bir işlemin son ürünü aldığı anlamına gelir. | **"If the number of affected rows is zero, it means another transaction just took the last item."** |
| Eşzamanlı değişiklikleri tespit etmek için versiyon kolonuyla iyimser kilitleme (optimistic locking) kullanıyoruz. | **"We use optimistic locking with a version column to detect concurrent modifications."** |
| Karmaşık ve çok adımlı işlemlerde satırı işlem bitene kadar kilitlemek için `SELECT FOR UPDATE` kullanıyoruz. | **"For complex multi-step operations, we use `SELECT FOR UPDATE` to lock the row until the transaction commits."** |
| Deadlock oluşmasını önlemek için birden fazla ürünü her zaman ID sırasına göre kilitliyoruz. | **"To avoid deadlocks, we always lock multiple items in a consistent order by their ID."** |
| Veritabanı kilitlerini dar tutmak için dış API çağrılarını asla açık bir DB transaction'ı içinde yapmıyoruz. | **"We never make external HTTP calls inside an open database transaction so we don't hold locks for a long time."** |

### 2.3. `Problem → Solution → Reason → Result` Konuşma Kartları

#### 🎯 Senaryo 2A: İki Müşterinin Son 1 Ürünü Aynı Anda Alması (Race Condition)
- 🔴 **Problem:**
  - **EN:** *"If two customers try to buy the last item in stock at the exact same time, reading the stock into C# memory first causes a race condition and leads to overselling."*
  - *(TR: İki müşteri stoktaki son ürünü aynı anda almaya çalışırsa, stoğu önce C# belleğine okumak yarış durumuna (race condition) yol açar ve olmayan ürünün satılmasına neden olur.)*
- 🛠️ **Solution:**
  - **EN:** *"To solve this, we execute a single atomic SQL update with a condition: `UPDATE stock SET reserved = reserved + @qty WHERE sku = @sku AND (physical - reserved) >= @qty`, combined with optimistic row versioning."*
  - *(TR: Bunu çözmek için koşula bağlı tek bir atomik SQL güncellemesi çalıştırıyoruz ve bunu iyimser satır versiyonlama ile birleştiriyoruz.)*
- 🧠 **Reason:**
  - **EN:** *"The reason we do this at the database level is that PostgreSQL serializes writes to the same row automatically, without blocking read queries or holding long locks."*
  - *(TR: Bunu veritabanı seviyesinde yapmamızın nedeni, PostgreSQL'in aynı satıra yapılan yazma işlemlerini okuma sorgularını bloklamadan ve uzun kilitler tutmadan otomatik olarak sıraya sokmasıdır.)*
- ✅ **Result:**
  - **EN:** *"As a result, only the first transaction updates the row and gets `1 row affected`, while the second gets `0 rows affected` and we prevent negative stock 100% of the time."*
  - *(TR: Sonuç olarak sadece ilk işlem satırı günceller ve `1 satır etkilendi` alır; ikincisi `0 satır` alır ve eksi stoğu %100 oranında engelleriz.)*

---

## 3. Alan 3: Inventory Synchronization (Stok Senkronizasyonu & Overselling)

### 3.1. Derin Yazılım Bilgisi (Neyi Bilmelisin?)
- **Wovar'ın Ortamı:** Fiziksel stok **NetSuite (ERP)** içindedir. Satış ise **BigCommerce (Webshop)** ve **Channable (Pazaryerleri - Amazon, Bol.com vb.)** üzerinden yapılır.
- **3 Temel Stok Kavramı:**
  1. **`Physical Stock` (Fiziksel Stok):** Depodaki gerçek adet (Sahibi: ERP / NetSuite).
  2. **`Reserved Stock` (Rezerve Stok):** Webshop veya pazaryerinde siparişi alınmış ama henüz ERP'de faturası/irsaliyesi kesilip depodan düşülmemiş adet (Sahibi: .NET Integration Layer).
  3. **`Available-to-Sell (ATS)` (Satılabilir Stok):** `Physical Stock - Reserved Stock - Safety Buffer`. Dış dünyaya (BigCommerce ve Channable'a) bildirilen rakam budur!
- **Event-Driven Delta vs. Full Reconciliation:**
  - Gün içinde stok değişimleri **event-driven (anlık olaylar)** olarak akar. Ancak mesaj sırası kayması veya ERP'de manuel stok düzeltmesi ihtimaline karşı her mesajda **versiyon/zaman damgası (timestamp)** kontrol edilir ve geceleri/periyodik olarak **Reconciliation (Mutabakat)** job'ı ile tam stok eşitlenir.

### 3.2. Terimler ve "Tam Cümle" Sözlüğü (Turkish ↔ Complete English Sentences)

| Türkçe Kavram / Düşünce | Doğrudan Söyleyeceğin Tam İngilizce Cümle |
|---|---|
| ERP fiziksel stok için tek doğruluk kaynağıdır, entegrasyon katmanı ise aktif rezervasyonları takip eder. | **"The ERP is the single source of truth for physical stock, while our integration layer tracks active reservations."** |
| Eğer ERP geçici olarak ulaşılamaz durumdaysa, güncellemeleri kuyruğa alırız ve sonrasında stok mutabakatı yaparız. | **"If the ERP is unavailable, we queue the updates and reconcile the inventory afterward."** |
| Pazaryerlerine stok gönderirken senkronizasyon gecikmelerini tolere etmek için küçük bir güvenlik payı (safety buffer) bırakıyoruz. | **"When pushing stock to marketplaces, we keep a small safety buffer to absorb synchronization delays."** |
| Eski bir stok mesajının yeni stok verisini ezmesini önlemek için zaman damgası veya versiyon numarası kontrolü yapıyoruz. | **"We check timestamps or version numbers so an older stock message never overwrites a newer stock update."** |
| Eğer seçilen depoda son ürün hasarlı çıkarsa, siparişi otomatik olarak stoğu olan başka bir mağazaya yönlendiriyoruz. | **"If the last item is damaged during picking, we automatically re-allocate the order to another store that has stock."** |
| ERP ile e-ticaret platformu arasındaki stok farklarını yakalamak için zamanlanmış mutabakat işleri (reconciliation jobs) çalıştırıyoruz. | **"We run scheduled reconciliation jobs to detect and fix any stock discrepancies between the ERP and the webshop."** |

### 3.3. `Problem → Solution → Reason → Result` Konuşma Kartları

#### 🎯 Senaryo 3A: Çoklu Kanalda (ERP, Webshop, Pazaryeri) Eşzamanlı Stok Değişimi (DeFacto)
- 🔴 **Problem:**
  - **EN:** *"When selling across a webshop and multiple external marketplaces simultaneously, there is always a small sync delay between the ERP and sales channels, which can cause two channels to sell the same last item."*
  - *(TR: Bir webshop ve birden fazla dış pazaryerinde aynı anda satış yaparken, ERP ile satış kanalları arasında her zaman küçük bir senkronizasyon gecikmesi olur; bu da iki kanalın aynı son ürünü satmasına yol açabilir.)*
- 🛠️ **Solution:**
  - **EN:** *"To solve this at DeFacto, our integration layer tracked real-time order reservations locally, calculated `Available-to-Sell` stock as `Physical Stock minus Reserved Stock minus a Safety Buffer`, and pushed updates asynchronously."*
  - *(TR: Bunu DeFacto'da çözmek için entegrasyon katmanımız anlık sipariş rezervasyonlarını lokalde takip etti, Satılabilir Stoğu `Fiziksel Stok - Rezerve Stok - Güvenlik Payı` olarak hesapladı ve güncellemeleri asenkron olarak gönderdi.)*
- 🧠 **Reason:**
  - **EN:** *"The reason is that waiting for the ERP to confirm every single reservation synchronously would slow down checkout and fail whenever the ERP is under maintenance."*
  - *(TR: Bunun nedeni, her bir rezervasyonu senkron olarak ERP'nin onaylamasını beklemenin ödeme adımını yavaşlatacak ve ERP bakımda olduğunda sistemi durduracak olmasıdır.)*
- ✅ **Result:**
  - **EN:** *"As a result, we could accept orders 24/7 even during ERP downtimes, prevent marketplace overselling, and reconcile everything cleanly once the ERP processed the queue."*
  - *(TR: Sonuç olarak ERP kesintilerinde bile 7/24 sipariş alabildik, pazaryerlerinde fazla satışı önledik ve ERP kuyruğu işlediğinde her şeyi temizce eşitledik.)*

---

## 4. Alan 4: Kafka and Event-Driven Architecture (Kafka, Outbox & Inbox)

### 4.1. Derin Yazılım Bilgisi (Neyi Bilmelisin?)
- **Dual-Write (Çifte Yazma) Problemi:** Bir servisin aynı metodun içinde önce PostgreSQL'e `SaveChanges()` yapıp hemen alt satırda `kafkaProducer.ProduceAsync()` çağırması **hatalı bir mimaridir**. Çünkü DB commit olduktan 1 milisaniye sonra elektrik kesilirse veya Kafka ağ hatası verirse, veri DB'de kalır ama event asla yayınlanmaz (sistemler arası tutarsızlık oluşur).
- **Çözüm 1 — Transactional Outbox Pattern + Debezium (CDC):**
  - İş verisi (ör. `orders` tablosu) ve gönderilecek event (`outbox_messages` tablosu) **tek bir PostgreSQL ACID transaction'ı (`BEGIN ... COMMIT`)** içinde kaydedilir.
  - **Debezium**, PostgreSQL'in **WAL (Write-Ahead Log)** dosyasını dinler ve `outbox_messages` tablosuna düşen her yeni satırı milisaniyeler içinde Kafka'ya iletir.
- **Çözüm 2 — Idempotent Consumer (Inbox Pattern / `processed_events`):**
  - Kafka *at-least-once* (en az bir kere) teslimat yapar. Consumer mesajı işleyip kendi DB'sini güncelledikten sonra, Kafka'ya "ben bunu işledim" (Offset Commit) diyemeden çökerse, ayağa kalktığında aynı mesaj tekrar gelir.
  - Consumer kendi veritabanında bir `processed_events` (`event_id` UNIQUE PK) tablosu tutar. İş güncellemesini ve `INSERT INTO processed_events` işlemini **tek bir DB transaction'ı** içinde yapar; commit başarılı olduktan sonra Kafka offset'ini manuel commit eder (`EnableAutoCommit = false`).

### 4.2. Terimler ve "Tam Cümle" Sözlüğü (Turkish ↔ Complete English Sentences)

| Türkçe Kavram / Düşünce | Doğrudan Söyleyeceğin Tam İngilizce Cümle |
|---|---|
| Veritabanı değişiklikleri ile yayınlanan event'ler arasındaki tutarsızlıkları önlemek için Transactional Outbox desenini kullanıyoruz. | **"We use the transactional outbox pattern to avoid inconsistencies between database changes and published events."** |
| İş verisini ve outbox mesajını tek bir yerel PostgreSQL transaction'ı içinde kaydediyoruz. | **"We save both the business data and the outbox event inside a single local PostgreSQL transaction."** |
| Debezium, PostgreSQL'in WAL loglarını okur ve yeni outbox kayıtlarını Kafka'ya aktarır. | **"Debezium reads PostgreSQL's Write-Ahead Log and streams new outbox records into Kafka."** |
| Aynı siparişe veya ürüne ait mesajların sırayla işlenmesi için Kafka'da Partition Key olarak `OrderId` veya `Sku` kullanıyoruz. | **"We use `OrderId` or `Sku` as the Kafka partition key so events for the same entity are always processed in order."** |
| Kafka en az bir kez teslimat garantisi verdiği için tüm consumer servislerimizi idempotent olarak tasarlıyoruz. | **"Because Kafka provides at-least-once delivery, we design all of our consumers to be idempotent."** |
| Mesajların iki kez işlenmesini önlemek için consumer veritabanında `processed_events` (Inbox) tablosu tutuyoruz. | **"We keep a `processed_events` inbox table in the consumer database to prevent duplicate message processing."** |
| Otomatik offset commit özelliğini kapatıyoruz ve Kafka offset'ini sadece veritabanı transaction'ı commit olduktan sonra onaylıyoruz. | **"We disable auto-commit and manually commit the Kafka offset only after the database transaction succeeds."** |

### 4.3. `Problem → Solution → Reason → Result` Konuşma Kartları

#### 🎯 Senaryo 4A: Dual-Write Problemi ve Consumer Çökmesi (Drivee)
- 🔴 **Problem:**
  - **EN:** *"If a backend service updates PostgreSQL and publishes to Kafka directly in code, a network failure or crash between those two steps leaves the database and other systems out of sync. Also, if a consumer crashes right before committing the Kafka offset, it will process the same message twice on restart."*
  - *(TR: Bir backend servisi PostgreSQL'i güncelleyip kod içinde doğrudan Kafka'ya mesaj atarsa, bu iki adım arasındaki bir ağ hatası veya çökme veritabanı ile diğer sistemleri tutarsız bırakır. Ayrıca bir consumer Kafka offset'ini onaylamadan hemen önce çökerse, yeniden başladığında aynı mesajı iki kez işler.)*
- 🛠️ **Solution:**
  - **EN:** *"At Drivee, I solved the producer side by writing both the business update and an `outbox_messages` row in a single PostgreSQL transaction, streamed to Kafka via Debezium CDC. On the consumer side, I disabled auto-commit and inserted the `event_id` into a `processed_events` table in the same transaction as the business update."*
  - *(TR: Drivee'de üretici tarafını hem iş güncellemesini hem de `outbox_messages` satırını tek bir PostgreSQL transaction'ında yazıp Debezium CDC ile Kafka'ya aktararak çözdüm. Tüketici tarafında ise auto-commit'i kapattım ve `event_id`'yi iş güncellemesiyle aynı transaction'da `processed_events` tablosuna kaydettim.)*
- 🧠 **Reason:**
  - **EN:** *"The reason is that relational databases guarantee ACID atomicity within a single database, turning a risky distributed dual-write into a safe local transaction on both ends."*
  - *(TR: Bunun nedeni, ilişkisel veritabanlarının tek bir veritabanı içinde ACID atomikliğini garanti etmesi ve böylece riskli bir dağıtık çifte yazmayı her iki uçta da güvenli bir yerel transaction'a dönüştürmesidir.)*
- ✅ **Result:**
  - **EN:** *"As a result, across 25 million daily events at Drivee, we achieved zero lost events and zero duplicate side effects even when containers restarted."*
  - *(TR: Sonuç olarak Drivee'de günlük 25 milyon olayda, konteynerler yeniden başlasa bile sıfır veri kaybı ve sıfır mükerrer yan etki elde ettik.)*

---

## 5. Alan 5: Failure Recovery & Resilience (Hata Kurtarma, Timeout, DLQ & Mutabakat)

### 5.1. Derin Yazılım Bilgisi (Neyi Bilmelisin?)
- **1. Dış API Timeout (Belirsiz Durum / Unknown State):**
  - Dış sisteme (ör. NetSuite ERP veya Delivery Hero Datafridge) `POST /orders` attık ve 15 saniye sonunda `TimeoutException` aldık. Sipariş karşıda oluştu mu, oluşmadı mı?
  - **Çözüm A (Idempotent API):** İsteği atmadan önce kendi `OrderId` GUID değerimizi `PendingSync` olarak kaydedip karşıya `Idempotency-Key` / `externalOrderId` olarak göndeririz. Retry yaptığımızda karşı sistem aynı ID'yi görüp mükerrer kayıt açmaz.
  - **Çözüm B (Read-Before-Retry):** Karşı sistem idempotency desteklemiyorsa, tekrar `POST` atmadan önce `GET /orders?externalOrderId={ourId}` ile sorarız. Varsa ID'sini alıp `Synced` işaretleriz; `404` dönerse tekrar `POST` atarız.
- **2. Geçici Hata (Transient) vs. Kalıcı/İş Hatası (Permanent/Business Error):**
  - **Transient (`408 Timeout`, `429 Rate Limit`, `502/503/504`):** Otomatik olarak **Exponential Backoff + Jitter** (ör. 2sn, 4sn, 8sn + rastgele milisaniye) ile tekrar denenir.
  - **Permanent (`400 Bad Request`, `422 Validation Error` — ör. geçersiz vergi numarası veya eksik SKU):** Tekrar denemek anlamsızdır! Doğrudan **Dead-Letter Queue (DLQ)**'ya atılır ve Slack/Teams üzerinden ekibe alarm üretilir.
- **3. Observability (Gözlemlenebilirlik):**
  - Her sipariş ve mesaja başlangıçta bir **`CorrelationId` (`TraceId`)** verilir. BigCommerce → .NET Integration Layer → Kafka/Queue → NetSuite boyunca tüm loglara bu ID basılır (Datadog / Elastic APM).

### 5.2. Terimler ve "Tam Cümle" Sözlüğü (Turkish ↔ Complete English Sentences)

| Türkçe Kavram / Düşünce | Doğrudan Söyleyeceğin Tam İngilizce Cümle |
|---|---|
| Bir dış API zaman aşımına uğradığında, tekrar `POST` göndermeden önce siparişin oluşup oluşmadığını sorgularız. | **"When an external API times out, we query the external system to check if the order was created before retrying the `POST` request."** |
| Karşı sistemde çift kayıt oluşmasını önlemek için kendi sipariş ID'mizi dış referans anahtarı olarak gönderiyoruz. | **"We send our internal Order ID as an external reference key to prevent duplicates on the third-party system."** |
| Geçici ağ hatalarını üssel bekleme (exponential backoff) ile tekrar denerken, doğrulama hatalarını doğrudan Dead-Letter Queue'ya gönderiyoruz. | **"We retry transient network errors with exponential backoff, but we route validation errors directly to the Dead-Letter Queue."** |
| Başarısız mesajlar Dead-Letter Queue'ya düştüğünde ekibe otomatik bir bildirim gönderilir. | **"When failed messages land in the Dead-Letter Queue, an automated alert is sent to the engineering team."** |
| Dış sistemdeki sorun çözüldükten sonra Dead-Letter Queue'daki mesajları güvenle yeniden oynatabiliyoruz (replay). | **"Once the external issue is resolved, we can safely replay the messages from the Dead-Letter Queue."** |
| Bir siparişin tüm sistemlerdeki yolculuğunu saniyeler içinde takip edebilmek için her isteğe bir `CorrelationId` ekliyoruz. | **"We attach a `CorrelationId` to every request so we can trace an order's journey across all systems in seconds."** |

### 5.3. `Problem → Solution → Reason → Result` Konuşma Kartları

#### 🎯 Senaryo 5A: Dış API Timeout ve Mükerrer Sipariş Riski (Yemeksepeti → Delivery Hero Datafridge)
- 🔴 **Problem:**
  - **EN:** *"When sending Yemeksepeti orders to Delivery Hero's central Datafridge system (or external partner POS APIs), HTTP requests frequently timed out. If we blindly retried the `POST` request, we risked creating duplicate financial order records."*
  - *(TR: Yemeksepeti siparişlerini Delivery Hero'nun merkezi Datafridge sistemine gönderirken HTTP istekleri sık sık zaman aşımına uğruyordu. Körü körüne tekrar `POST` atsaydık çift finansal sipariş kaydı oluşturma riskimiz vardı.)*
- 🛠️ **Solution:**
  - **EN:** *"To solve this, we persisted our internal `OrderId` as `PendingSync` before calling the API, passed it as the external reference key, and for legacy APIs without idempotency support, our SQS retry worker executed a `GET` lookup by `OrderId` before re-sending the `POST`. Persistent failures after max retries were routed to a Dead-Letter Queue (DLQ)."*
  - *(TR: Bunu çözmek için API'yi çağırmadan önce kendi `OrderId`'mizi `PendingSync` olarak kaydettik, dış referans anahtarı olarak gönderdik ve idempotency desteği olmayan eski API'lerde SQS retry worker'ımız tekrar `POST` atmadan önce `OrderId` ile `GET` sorgusu yaptı. Maksimum denemeden sonra süren hatalar DLQ'ya yönlendirildi.)*
- 🧠 **Reason:**
  - **EN:** *"The reason is that a timeout leaves the caller in an unknown state—you don't know if the request failed on the way there or on the way back—so verifying the state or using an idempotency key is the only safe way to recover."*
  - *(TR: Bunun nedeni, zaman aşımının çağıran tarafı belirsiz bir durumda bırakmasıdır—isteğin giderken mi yoksa dönerken mi koptuğunu bilemezsiniz—bu yüzden durumu doğrulamak veya idempotency anahtarı kullanmak kurtarmanın tek güvenli yoludur.)*
- ✅ **Result:**
  - **EN:** *"As a result, our workers recovered from daily network timeouts automatically without human intervention and without creating a single duplicate order in Datafridge."*
  - *(TR: Sonuç olarak worker'larımız günlük ağ zaman aşımlarından insan müdahalesi olmadan ve Datafridge'de tek bir çift sipariş bile yaratmadan otomatik olarak kurtuldu.)*

---

## 6. Engineering Lead'in Sorabileceği 8 "Neden?" (Trade-Off) Sorusu

Wovar'ın Engineering Lead'i senin yazdığın cevapları okuduktan sonra görüşmede şu derinleştirme sorularını sorabilir. Hepsini yine **kısa ve net** yanıtla:

1. **❓ "In Yemeksepeti, if PostgreSQL already has a `UNIQUE` index on `basket_id`, why did you also use Redis in front of it?"**
   *(PostgreSQL'de zaten UNIQUE index varsa neden önünde ayrıca Redis kullandınız?)*
   - **🎤 Cevap:** *"To protect the database under high traffic. If a user or buggy mobile client sends 10 requests at once, hitting PostgreSQL 10 times wastes DB connections and transactions. Redis filters out the 9 duplicates in memory in less than a millisecond, and PostgreSQL acts as our final safety net."*

2. **❓ "Why did you choose Debezium CDC instead of a simple background polling job (`SELECT * FROM outbox WHERE processed = false`)?"**
   *(Neden basit bir polling worker yerine Debezium CDC seçtiniz?)*
   - **🎤 Cevap:** *"For lower volume, a polling job with `FOR UPDATE SKIP LOCKED` is great and simpler to run. However, at Drivee we process 25 million packets a day. Constant polling at that scale puts heavy read/write load on PostgreSQL. Debezium reads the WAL log directly with near-zero latency and much less load on the database."* *(Not: Bu cevap senin "her yere ezbere Debezium koymadığını, ölçeğe göre karar verdiğini" gösterir!)*

3. **❓ "What happens if a message in the Dead-Letter Queue (DLQ) is fixed and replayed, but a newer update for the same order or stock was already processed?"**
   *(DLQ'daki bir mesaj düzeltilip tekrar oynatıldığında, aynı sipariş veya stok için daha yeni bir güncelleme çoktan işlendiyse ne olur?)*
   - **🎤 Cevap:** *"Every event payload includes a timestamp or version number. Before applying an update, the consumer checks if the incoming event's timestamp/version is newer than the current record in PostgreSQL. If an older DLQ message is replayed later, it is safely ignored so it doesn't overwrite newer data."*

4. **❓ "How do you maintain message ordering in Kafka when you have multiple consumer instances?"**
   *(Birden fazla consumer instance çalışırken Kafka'da mesaj sırasını nasıl korursun?)*
   - **🎤 Cevap:** *"Kafka guarantees ordering within a single partition. So we use the `OrderId`, `Sku`, or `DeviceId` as the partition key. That way, all events for the same order or product always go to the exact same partition and are processed sequentially by a single consumer instance in the group."*

5. **❓ "When calling an external ERP like NetSuite, what happens if NetSuite returns `429 Too Many Requests` (Rate Limit)?"**
   *(NetSuite `429 Rate Limit` dönerse ne yaparsın?)*
   - **🎤 Cevap:** *"First, we check if the response includes a `Retry-After` header and pause retries for that duration. Second, we limit concurrency on our queue consumer so we don't flood the ERP, and where supported, we batch multiple updates into a single API call."*

6. **❓ "Why shouldn't we call an external API (like a Payment Provider or ERP) inside a database transaction?"**
   *(Neden bir veritabanı transaction'ı açıkken dış API çağırmamalıyız?)*
   - **🎤 Cevap:** *"Because external APIs can take seconds or time out. Holding a PostgreSQL transaction open during an HTTP call locks rows and holds database connections, which quickly causes connection pool exhaustion and blocks the whole system."*

7. **❓ "What is the difference between a Domain Event and an Integration Event in your architecture?"**
   *(Mimarinde Domain Event ile Integration Event arasındaki fark nedir?)*
   - **🎤 Cevap:** *"A Domain Event happens inside a single service or bounded context in memory before saving. An Integration Event is a versioned contract saved to the Outbox and published to a message broker so other services or external systems can react asynchronously."*

8. **❓ "If you join Wovar, how will you approach taking over the .NET integration layer from the external agency?"**
   *(Wovar'a katılırsan dış ajanstan .NET entegrasyon katmanını devralmaya nasıl yaklaşacaksın?)*
   - **🎤 Cevap:** *"First, I will map out the critical data flows between BigCommerce, NetSuite, Akeneo, and Channable, and check our observability—logs, correlation IDs, and alerts. Second, I will write integration tests around the riskiest flows (like orders and inventory sync) before refactoring anything. That way, we can take ownership safely without breaking daily sales."*

---

## 7. Her Gün Sesli Okunacak "30 Altın Tam Cümle" (Ezber Bozan Özet Liste)

> 🗣️ **Kees'in Tavsiyesi:** *"Don't just practise individual technical words; practise explaining them in complete English sentences."*
> Mülakata kadar bu 30 cümleyi günde 2 kez **sesli** oku. Hepsi sade, net ve doğrudan Wovar mülakatında kullanacağın cümlelerdir:

### Checkout & Idempotency (1–6)
1. **"We use a unique database constraint to prevent duplicate orders."**
   *(Çift siparişleri önlemek için veritabanında unique constraint kullanıyoruz.)*
2. **"The client sends a unique idempotency key with every checkout request."**
   *(İstemci her ödeme isteğiyle birlikte benzersiz bir idempotency anahtarı gönderir.)*
3. **"We run an atomic `SET NX` command in Redis to block simultaneous duplicate requests."**
   *(Eşzamanlı mükerrer istekleri engellemek için Redis'te atomik `SET NX` komutu çalıştırıyoruz.)*
4. **"Redis filters out duplicate requests in memory before they reach the database."**
   *(Redis, mükerrer istekleri daha veritabanına ulaşmadan bellekte filtreler.)*
5. **"We never trust Redis alone; PostgreSQL is always our final source of truth."**
   *(Asla tek başına Redis'e güvenmeyiz; son doğruluk kaynağımız her zaman PostgreSQL'dir.)*
6. **"If Redis goes down or the key expires, the unique index in PostgreSQL blocks any duplicate insert."**
   *(Redis çökerse veya anahtarın süresi dolarsa, PostgreSQL'deki unique index çift kayıt eklenmesini engeller.)*

### Concurrency & Database Transactions (7–12)
7. **"We never read stock into memory and update it later because that causes a race condition."**
   *(Stoğu belleğe okuyup sonradan güncellemeyiz çünkü bu yarış durumuna yol açar.)*
8. **"We update reserved stock directly in SQL with an atomic `WHERE` condition."**
   *(Rezerve stoğu doğrudan SQL içinde atomik bir `WHERE` koşuluyla güncelleriz.)*
9. **"We use optimistic locking with a version column to detect concurrent updates."**
   *(Eşzamanlı güncellemeleri tespit etmek için versiyon kolonuyla iyimser kilitleme kullanıyoruz.)*
10. **"If two transactions try to buy the last item, only the first one updates the row and the second one gets zero rows affected."**
    *(İki işlem son ürünü almaya çalışırsa sadece ilki satırı günceller, ikincisi sıfır satır etkilendi sonucu alır.)*
11. **"We never call external APIs inside an open database transaction."**
    *(Açık bir veritabanı transaction'ı içinde asla dış API çağrısı yapmayız.)*
12. **"To prevent deadlocks, we always lock multiple rows in the same ID order."**
    *(Deadlock'ları önlemek için birden fazla satırı her zaman aynı ID sırasıyla kilitleriz.)*

### Inventory Synchronization (13–18)
13. **"NetSuite ERP is the single source of truth for physical warehouse stock."**
    *(NetSuite ERP, fiziksel depo stoğu için tek doğruluk kaynağıdır.)*
14. **"Our integration layer tracks active reservations for orders that are not yet synced to the ERP."**
    *(Entegrasyon katmanımız, henüz ERP'ye senkronize edilmemiş siparişler için aktif rezervasyonları takip eder.)*
15. **"When pushing stock to marketplaces, we subtract reserved stock and a small safety buffer."**
    *(Pazaryerlerine stok gönderirken rezerve stoğu ve küçük bir güvenlik payını düşeriz.)*
16. **"If the ERP is unavailable, we queue the updates and reconcile the inventory afterward."**
    *(Eğer ERP ulaşılamaz durumdaysa, güncellemeleri kuyruğa alırız ve sonrasında stok mutabakatı yaparız.)*
17. **"We check version numbers or timestamps so an older message never overwrites a newer stock level."**
    *(Eski bir mesajın daha yeni bir stok seviyesini ezmemesi için versiyon numaralarını veya zaman damgalarını kontrol ederiz.)*
18. **"We run scheduled background jobs to compare ERP stock with our local reservations and fix any differences."**
    *(ERP stoğunu yerel rezervasyonlarımızla karşılaştırmak ve farkları düzeltmek için zamanlanmış arka plan işleri çalıştırıyoruz.)*

### Kafka & Event-Driven Architecture (19–24)
19. **"We use the transactional outbox pattern to avoid inconsistencies between database changes and published events."**
    *(Veritabanı değişiklikleri ile yayınlanan event'ler arasındaki tutarsızlıkları önlemek için transactional outbox desenini kullanıyoruz.)*
20. **"We save the business entity and the outbox message inside a single PostgreSQL transaction."**
    *(İş verisini ve outbox mesajını tek bir PostgreSQL transaction'ı içinde kaydediyoruz.)*
21. **"Debezium reads PostgreSQL's WAL logs and streams new outbox events to Kafka."**
    *(Debezium, PostgreSQL'in WAL loglarını okur ve yeni outbox olaylarını Kafka'ya aktarır.)*
22. **"We use the `OrderId` or `Sku` as the Kafka partition key to guarantee message ordering."**
    *(Mesaj sırasını garanti altına almak için Kafka partition anahtarı olarak `OrderId` veya `Sku` kullanıyoruz.)*
23. **"To make our consumers idempotent, we store processed event IDs in an inbox table inside the same database transaction."**
    *(Consumer'larımızı idempotent yapmak için, işlenen event ID'lerini aynı veritabanı transaction'ı içinde bir inbox tablosuna kaydediyoruz.)*
24. **"We disable auto-commit and manually commit the Kafka offset only after the database transaction commits."**
    *(Auto-commit'i kapatıyoruz ve Kafka offset'ini sadece veritabanı transaction'ı commit olduktan sonra manuel olarak onaylıyoruz.)*

### Failure Recovery & Observability (25–30)
25. **"When a `POST` request to an external API times out, we don't know if the order was created or not."**
    *(Dış bir API'ye yapılan `POST` isteği zaman aşımına uğradığında, siparişin oluşup oluşmadığını bilemeyiz.)*
26. **"Before retrying a timed-out `POST` request, we query the external API by our reference ID to check if the order already exists."**
    *(Zaman aşımına uğrayan bir `POST` isteğini tekrar denemeden önce, siparişin zaten var olup olmadığını kontrol etmek için dış API'yi referans ID'mizle sorgularız.)*
27. **"We retry transient network errors with exponential backoff, and send validation errors straight to the Dead-Letter Queue."**
    *(Geçici ağ hatalarını üssel bekleme ile tekrar deneriz, doğrulama hatalarını ise doğrudan Dead-Letter Queue'ya göndeririz.)*
28. **"When a message lands in the Dead-Letter Queue, it triggers an alert so we can inspect and replay it safely."**
    *(Bir mesaj Dead-Letter Queue'ya düştüğünde, onu inceleyip güvenle yeniden oynatabilmemiz için bir alarm tetikler.)*
29. **"We pass a `CorrelationId` across all services and queues so we can trace any failed order in seconds."**
    *(Hata veren herhangi bir siparişi saniyeler içinde takip edebilmek için tüm servisler ve kuyruklar boyunca bir `CorrelationId` taşırız.)*
30. **"Checkout should never fail just because a downstream system like the ERP is temporarily down."**
    *(Ödeme adımı, sırf ERP gibi alt bir sistem geçici olarak çöktü diye asla başarısız olmamalıdır.)*

