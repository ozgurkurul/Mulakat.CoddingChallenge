# 🗣️ Senior .NET Developer — Mülakat ve Günlük Mühendislik İçin 205 Altın İngilizce Cümle (`English-Helper.md`)

> **Amaç:** Bu dosya, bir Senior .NET Backend geliştiricinin teknik mülakatlarda ve uluslararası (remote) ekiplerde günlük olarak en çok kurduğu **205 tam, doğal ve akıcı İngilizce cümleyi** içerir.
> **Nasıl Çalışmalısın?** Tek tek kelime ezberlemek yerine her gün 1-2 bölümü (20–40 cümleyi) **sesli olarak** oku. Kalın yazılmış anahtar kelimelerin cümle içinde nasıl kullanıldığına dikkat et.

---

## 📑 İçindekiler

1. [Bölüm 1: C# & .NET Backend Geliştirme (Cümle 1–20)](#1-bölüm-1-c--net-backend-geliştirme-cümle-120)
2. [Bölüm 2: Veritabanı, PostgreSQL, EF Core & Redis (Cümle 21–40)](#2-bölüm-2-veritabanı-postgresql-ef-core--redis-cümle-2140)
3. [Bölüm 3: Sistem Dizaynı, Event-Driven Mimari & Entegrasyon (Cümle 41–60)](#3-bölüm-3-sistem-dizaynı-event-driven-mimari--entegrasyon-cümle-4160)
4. [Bölüm 4: Design Patterns, SOLID, DDD & Clean Architecture (Cümle 61–80)](#4-bölüm-4-design-patterns-solid-ddd--clean-architecture-cümle-6180)
5. [Bölüm 5: DevOps, CI/CD, Docker, Observability & Test (Cümle 81–100)](#5-bölüm-5-devops-cicd-docker-observability--test-cümle-81100)
6. [Bölüm 6: Günlük Takım İletişimi, Standup, PR Review & Mülakat Kalıpları (Cümle 101–120)](#6-bölüm-6-günlük-takım-iletişimi-standup-pr-review--mülakat-kalıpları-cümle-101120)
7. [Bölüm 7: Temel C# & .NET Mülakat Soruları İçin Hazır Cevap Cümleleri (Cümle 121–150)](#7-bölüm-7-temel-c--net-mülakat-soruları-için-hazır-cevap-cümleleri-cümle-121150)
8. [Bölüm 8: Cache Storm/Stampede, Multi-Pod Invalidation, Rate Limiting, Gateway & Tracing (Cümle 151–177)](#8-bölüm-8-cache-stormstampede-multi-pod-invalidation-rate-limiting-gateway--tracing-cümle-151177)
9. [Bölüm 9: E-Ticaret Sipariş Süreçleri — Sepet, Ödeme, Stok Rezervasyonu, State Machine & İade (Cümle 178–205)](#9-bölüm-9-e-ticaret-sipariş-süreçleri--sepet-ödeme-stok-rezervasyonu-state-machine--iade-cümle-178205)

---

## 1. Bölüm 1: C# & .NET Backend Geliştirme (Cümle 1–20)

1. **"We use `async` and `await` all the way down so we never block threads during I/O operations."**
   *(Girdi/çıktı işlemleri sırasında thread'leri asla bloklamamak için en yukarıdan en aşağıya kadar `async` ve `await` kullanıyoruz.)*
2. **"Calling `.Result` or `.Wait()` on an async task can quickly cause thread pool starvation under high traffic."**
   *(Asenkron bir görevde `.Result` veya `.Wait()` çağırmak, yoğun trafik altında hızla thread havuzunun tükenmesine yol açabilir.)*
3. **"We pass a `CancellationToken` to all database and HTTP calls so aborted requests stop using server resources immediately."**
   *(İptal edilen isteklerin sunucu kaynaklarını kullanmayı hemen bırakması için tüm veritabanı ve HTTP çağrılarına `CancellationToken` iletiyoruz.)*
4. **"Since you cannot use `await` inside a standard C# `lock` block, we use `SemaphoreSlim` for asynchronous locking."**
   *(Standart bir C# `lock` bloğu içinde `await` kullanılamadığı için, asenkron kilitleme işlemlerinde `SemaphoreSlim` kullanıyoruz.)*
5. **"We register `DbContext` with a `Scoped` lifetime so each HTTP request gets its own isolated instance."**
   *(Her HTTP isteğinin kendi izole örneğini alması için `DbContext`'i `Scoped` yaşam döngüsüyle kaydediyoruz.)*
6. **"Injecting a `Scoped` service into a `Singleton` creates a captive dependency and leads to concurrency bugs."**
   *(`Scoped` bir servisi `Singleton` içine enjekte etmek 'captive dependency' oluşturur ve eşzamanlılık hatalarına yol açar.)*
7. **"In background workers, we inject `IServiceScopeFactory` and create a new scope for each message we process."**
   *(Arka plan işçilerinde `IServiceScopeFactory` enjekte ediyoruz ve işlediğimiz her mesaj için yeni bir scope oluşturuyoruz.)*
8. **"We always use `IHttpClientFactory` instead of creating `new HttpClient()` instances to avoid socket exhaustion."**
   *(Soket tükenmesini önlemek için `new HttpClient()` oluşturmak yerine her zaman `IHttpClientFactory` kullanıyoruz.)*
9. **"We handle global exceptions, logging, and correlation IDs in a custom ASP.NET Core middleware."**
   *(Global hataları, loglamayı ve korelasyon ID'lerini özel bir ASP.NET Core middleware katmanında yönetiyoruz.)*
10. **"We define our DTOs and Value Objects as immutable C# `record` types to make them thread-safe and predictable."**
    *(DTO ve Value Object'lerimizi thread-safe ve öngörülebilir olmaları için değiştirilemez C# `record` tipleri olarak tanımlıyoruz.)*
11. **"To reduce Garbage Collector (GC) pressure on hot paths, we avoid unnecessary object allocations inside loops."**
    *(Yoğun çalışan kod yollarında Garbage Collector üzerindeki baskıyı azaltmak için döngüler içinde gereksiz nesne üretiminden kaçınıyoruz.)*
12. **"Remember that `IQueryable` builds an SQL query for the database, while `IEnumerable` filters the data in memory."**
    *(`IQueryable`'ın veritabanı için bir SQL sorgusu oluşturduğunu, `IEnumerable`'ın ise veriyi bellekte filtrelediğini unutmamalıyız.)*
13. **"If you call `.ToList()` too early, you pull the entire table into memory before applying the `WHERE` filter."**
    *(Eğer `.ToList()` metodunu çok erken çağırırsanız, `WHERE` filtresini uygulamadan önce tüm tabloyu belleğe çekmiş olursunuz.)*
14. **"Instead of throwing exceptions for expected business errors, we return a `Result` object with a clear error code."**
    *(Beklenen iş kuralları hataları için exception fırlatmak yerine, net bir hata kodu içeren `Result` nesnesi dönüyoruz.)*
15. **"We use the `Options` pattern with validation at startup so the app fails fast if a configuration value is missing."**
    *(Bir konfigürasyon değeri eksikse uygulamanın başlangıçta hemen uyarı vermesi için doğrulamalı `Options` desenini kullanıyoruz.)*
16. **"We upgraded our microservices to modern .NET to benefit from better performance, minimal APIs, and lower memory usage."**
    *(Daha iyi performans, minimal API'ler ve daha düşük bellek kullanımından faydalanmak için mikroservislerimizi modern .NET'e yükselttik.)*
17. **"For in-process background queues inside a single service, we use `System.Threading.Channels` because it is fast and thread-safe."**
    *(Tek bir servis içindeki bellek içi arka plan kuyrukları için hızlı ve thread-safe olduğundan `System.Threading.Channels` kullanıyoruz.)*
18. **"We validate all incoming API requests at the boundary using `FluentValidation` before hitting the domain layer."**
    *(Gelen tüm API isteklerini, domain katmanına ulaşmadan önce sınırda `FluentValidation` kullanarak doğruluyoruz.)*
19. **"We use standardized `ProblemDetails` responses so frontend and third-party clients always get a consistent error format."**
    *(Frontend ve üçüncü parti istemcilerin her zaman tutarlı bir hata formatı alması için standart `ProblemDetails` yanıtları kullanıyoruz.)*
20. **"For recurring scheduled jobs and delayed tasks, we use Hangfire backed by PostgreSQL storage."**
    *(Tekrarlayan zamanlanmış işler ve gecikmeli görevler için PostgreSQL destekli Hangfire kullanıyoruz.)*

---

## 2. Bölüm 2: Veritabanı, PostgreSQL, EF Core & Redis (Cümle 21–40)

21. **"We use a unique database constraint on the `idempotency_key` column to prevent duplicate records."**
    *(Çift kayıtları önlemek için `idempotency_key` kolonunda veritabanı tekillik kısıtı kullanıyoruz.)*
22. **"For read-only queries in EF Core, we always add `.AsNoTracking()` to skip change tracking and speed up execution."**
    *(EF Core'daki salt okunur sorgularda değişiklik takibini atlamak ve sorguyu hızlandırmak için her zaman `.AsNoTracking()` ekliyoruz.)*
23. **"We avoid the N+1 query problem by projecting only the required columns with `.Select()` in a single query."**
    *(Tek bir sorguda `.Select()` ile sadece gereken kolonları çekerek N+1 sorgu probleminden kaçınıyoruz.)*
24. **"We use EF Core for domain writes where business rules matter, and Dapper for complex read-heavy reporting queries."**
    *(İş kurallarının önemli olduğu domain yazma işlemlerinde EF Core, okuma ağırlıklı karmaşık raporlama sorgularında ise Dapper kullanıyoruz.)*
25. **"To prevent lost updates when two users edit the same record, we use optimistic concurrency with a version column."**
    *(İki kullanıcı aynı kaydı düzenlerken kayıp güncellemeleri önlemek için versiyon kolonuyla iyimser eşzamanlılık kullanıyoruz.)*
26. **"If a concurrent transaction modifies the row first, EF Core throws a `DbUpdateConcurrencyException` and we safely retry or reject the request."**
    *(Eşzamanlı bir işlem satırı önce değiştirirse, EF Core `DbUpdateConcurrencyException` fırlatır ve isteği güvenle tekrar dener ya da reddederiz.)*
27. **"Instead of reading stock into C# memory and updating it later, we run an atomic SQL `UPDATE` with a `WHERE` condition."**
    *(Stoğu C# belleğine okuyup sonradan güncellemek yerine, `WHERE` koşulu içeren atomik bir SQL `UPDATE` komutu çalıştırıyoruz.)*
28. **"When we need to lock multiple rows in a transaction, we always lock them in the same ID order to prevent deadlocks."**
    *(Bir transaction içinde birden fazla satırı kilitlememiz gerektiğinde, deadlock oluşmasını önlemek için onları her zaman aynı ID sırasıyla kilitleriz.)*
29. **"We never make external HTTP calls inside an open database transaction because it holds locks and exhausts the connection pool."**
    *(Kilitleri tuttuğu ve bağlantı havuzunu tükettiği için açık bir veritabanı transaction'ı içinde asla dış HTTP çağrısı yapmıyoruz.)*
30. **"When diagnosing a slow PostgreSQL query, the first thing I do is run `EXPLAIN ANALYZE` to check the execution plan."**
    *(Yavaş bir PostgreSQL sorgusunu teşhis ederken yaptığım ilk şey, çalışma planını kontrol etmek için `EXPLAIN ANALYZE` çalıştırmaktır.)*
31. **"In a composite index, we put the equality filter columns first and the range or sorting columns last."**
    *(Birleşik bir indekste, eşitlik filtresi kolonlarını başa, aralık veya sıralama kolonlarını ise sona koyuyoruz.)*
32. **"We created a partial index on `WHERE status = 'PENDING'` so our outbox worker only scans unprocessed rows."**
    *(Outbox işçimizin sadece işlenmemiş satırları taraması için `WHERE status = 'PENDING'` koşulu üzerine parçalı bir indeks oluşturduk.)*
33. **"In production, we always create indexes with `CONCURRENTLY` in PostgreSQL so we don't lock the table for writes."**
    *(Canlı ortamda tabloyu yazma işlemlerine kilitlememek için PostgreSQL'de indeksleri her zaman `CONCURRENTLY` ile oluşturuyoruz.)*
34. **"For large lists, we use keyset (cursor) pagination instead of `OFFSET` because `OFFSET` gets slower as the page number grows."**
    *(Büyük listelerde `OFFSET` yerine keyset/cursor sayfalama kullanıyoruz çünkü sayfa numarası büyüdükçe `OFFSET` yavaşlar.)*
35. **"We always store money and quantities as `numeric` / `decimal` in PostgreSQL, never as floating-point numbers."**
    *(Para ve miktar verilerini PostgreSQL'de asla kayan noktalı sayı olarak değil, her zaman `numeric` / `decimal` olarak saklıyoruz.)*
36. **"We store all timestamps in UTC using `timestamptz` so time zone differences never break our logic."**
    *(Saat dilimi farklarının iş mantığımızı asla bozmaması için tüm zaman damgalarını `timestamptz` kullanarak UTC formatında saklıyoruz.)*
37. **"We use an atomic Redis `SET NX` command with a short TTL to block simultaneous duplicate requests at the edge."**
    *(Eşzamanlı mükerrer istekleri daha sınırda engellemek için kısa TTL süreli atomik bir Redis `SET NX` komutu kullanıyoruz.)*
38. **"We release our distributed Redis locks using a Lua script so an expired pod never deletes another pod's active lock."**
    *(Süresi dolmuş bir pod'un başka bir pod'un aktif kilidini asla silmemesi için dağıtık Redis kilitlerimizi Lua scripti ile açıyoruz.)*
39. **"We never treat Redis as our single source of truth; if Redis is unreachable, our service falls back to PostgreSQL."**
    *(Redis'i asla tek doğruluk kaynağımız olarak görmüyoruz; Redis'e ulaşılamazsa servisimiz doğrudan PostgreSQL'e düşüyor.)*
40. **"To deploy database schema changes with zero downtime, we follow the expand-and-contract migration pattern."**
    *(Veritabanı şema değişikliklerini sıfır kesintiyle canlıya almak için 'genişlet ve daralt' (expand-and-contract) migration desenini uyguluyoruz.)*

---

## 3. Bölüm 3: Sistem Dizaynı, Event-Driven Mimari & Entegrasyon (Cümle 41–60)

41. **"We decoupled the checkout flow from the ERP using asynchronous message queues so ERP downtime never blocks customer orders."**
    *(ERP kesintilerinin müşteri siparişlerini asla engellememesi için ödeme akışını asenkron mesaj kuyrukları kullanarak ERP'den ayırdık.)*
42. **"If we update the database and publish to Kafka directly in code, we risk the dual-write problem if the network fails in between."**
    *(Veritabanını güncelleyip kod içinde doğrudan Kafka'ya mesaj atarsak, arada ağ kopması durumunda çifte yazma (dual-write) problemi riskine gireriz.)*
43. **"We use the Transactional Outbox Pattern to guarantee that database changes and outgoing events are saved atomically."**
    *(Veritabanı değişiklikleri ile giden event'lerin atomik olarak kaydedilmesini garanti etmek için Transactional Outbox desenini kullanıyoruz.)*
44. **"We write both the business entity and the outbox message inside a single local PostgreSQL transaction."**
    *(Hem iş nesnesini hem de outbox mesajını tek bir yerel PostgreSQL transaction'ı içinde yazıyoruz.)*
45. **"Debezium reads PostgreSQL's Write-Ahead Log (WAL) and streams new outbox events into Kafka with near-zero latency."**
    *(Debezium, PostgreSQL'in WAL loglarını okur ve yeni outbox olaylarını sıfıra yakın gecikmeyle Kafka'ya aktarır.)*
46. **"Because Kafka and SQS provide at-least-once delivery, every consumer we build must be idempotent."**
    *(Kafka ve SQS en az bir kez teslimat garantisi sunduğu için, geliştirdiğimiz her consumer servisi idempotent olmak zorundadır.)*
47. **"To prevent duplicate processing on the consumer side, we record every processed `event_id` in an inbox table inside the same transaction."**
    *(Consumer tarafında mükerrer işlemeyi önlemek için, işlenen her `event_id` değerini aynı transaction içinde bir inbox tablosuna kaydediyoruz.)*
48. **"We disable auto-commit in Kafka and manually commit the offset only after the PostgreSQL transaction commits."**
    *(Kafka'da otomatik onaylamayı kapatıyoruz ve offset'i sadece PostgreSQL transaction'ı commit olduktan sonra manuel olarak onaylıyoruz.)*
49. **"We use `OrderId` or `Sku` as the Kafka partition key so all events for the same order or product are processed in strict order."**
    *(Aynı sipariş veya ürüne ait tüm olayların kesin sırayla işlenmesi için Kafka partition anahtarı olarak `OrderId` veya `Sku` kullanıyoruz.)*
50. **"When a `POST` request to an external API times out, we are in an unknown state because the order might have been created on their side."**
    *(Dış bir API'ye yapılan `POST` isteği zaman aşımına uğradığında belirsiz bir durumdayızdır; çünkü sipariş karşı tarafta oluşmuş olabilir.)*
51. **"Before retrying a timed-out `POST` request, our worker queries the external API by our reference ID to check if the order already exists."**
    *(Zaman aşımına uğramış bir `POST` isteğini tekrar denemeden önce, işçimiz siparişin zaten var olup olmadığını kontrol etmek için dış API'yi referans ID'mizle sorgular.)*
52. **"If the external system supports idempotency headers, we pass our internal `OrderId` so retries never create duplicate records."**
    *(Eğer dış sistem idempotency başlıklarını destekliyorsa, tekrar denemelerin asla çift kayıt oluşturmaması için kendi iç `OrderId` değerimizi göndeririz.)*
53. **"We distinguish between transient network errors, which we retry, and validation errors, which we send straight to the Dead-Letter Queue."**
    *(Tekrar denediğimiz geçici ağ hataları ile doğrudan Dead-Letter Queue'ya gönderdiğimiz doğrulama hatalarını birbirinden ayırıyoruz.)*
54. **"We use exponential backoff with random jitter on retries so we don't overwhelm a recovering third-party service."**
    *(Yeni ayağa kalkan bir üçüncü parti servisi boğmamak için tekrar denemelerde rastgele gecikmeli (jitter) üssel bekleme kullanıyoruz.)*
55. **"If an external ERP or payment provider is completely down, a circuit breaker trips and stops outgoing calls temporarily."**
    *(Bir dış ERP veya ödeme sağlayıcı tamamen çökmüşse, circuit breaker devreye girer ve giden çağrıları geçici olarak durdurur.)*
56. **"When an external API returns `429 Too Many Requests`, we respect the `Retry-After` header and throttle our queue consumer."**
    *(Bir dış API `429 Too Many Requests` döndüğünde, `Retry-After` başlığına uyarız ve kuyruk tüketicimizin hızını kısarız.)*
57. **"The ERP is the single source of truth for physical stock, while our integration layer tracks active reservations for open orders."**
    *(Fiziksel stok için tek doğruluk kaynağı ERP'dir; entegrasyon katmanımız ise açık siparişler için aktif rezervasyonları takip eder.)*
58. **"When publishing stock feeds to external marketplaces, we subtract a small safety buffer to prevent overselling during sync delays."**
    *(Dış pazaryerlerine stok beslemeleri gönderirken, senkronizasyon gecikmeleri sırasında fazla satışı önlemek için küçük bir güvenlik payı düşüyoruz.)*
59. **"We include a version number or timestamp in every stock event so an older delayed message never overwrites a newer stock count."**
    *(Gecikmiş eski bir mesajın daha yeni bir stok sayısını asla ezmemesi için her stok olayına bir versiyon numarası veya zaman damgası ekliyoruz.)*
60. **"We run scheduled reconciliation jobs to compare ERP balances with our local state and fix any silent data drift."**
    *(ERP bakiyelerini yerel durumumuzla karşılaştırmak ve sessiz veri kaymalarını düzeltmek için zamanlanmış mutabakat işleri çalıştırıyoruz.)*

---

## 4. Bölüm 4: Design Patterns, SOLID, DDD & Clean Architecture (Cümle 61–80)

61. **"We follow Clean Architecture so our core domain logic has zero dependency on databases, frameworks, or external APIs."**
    *(Çekirdek domain mantığımızın veritabanlarına, framework'lere veya dış API'lere sıfır bağımlılığı olması için Clean Architecture uyguluyoruz.)*
62. **"Using the Ports and Adapters pattern, our application layer defines an interface, and the third-party API client is just an infrastructure adapter."**
    *(Ports and Adapters desenini kullanarak uygulama katmanımız bir arayüz tanımlar, üçüncü parti API istemcisi ise sadece bir altyapı adaptörü olur.)*
63. **"We place an Anti-Corruption Layer (ACL) in front of the ERP and legacy systems so their external models don't pollute our domain."**
    *(Dış modellerinin kendi domain'imizi kirletmemesi için ERP ve eski sistemlerin önüne bir Anti-Corruption Layer (ACL) koyuyoruz.)*
64. **"If we ever replace the ERP or payment provider in the future, we only need to write a new adapter without touching the business logic."**
    *(Gelecekte ERP'yi veya ödeme sağlayıcısını değiştirirsek, iş mantığına hiç dokunmadan sadece yeni bir adaptör yazmamız yeterli olur.)*
65. **"We keep our aggregates small and enforce all business invariants inside the Aggregate Root."**
    *(Aggregate'lerimizi küçük tutuyoruz ve tüm iş kurallarını Aggregate Root içinde zorunlu kılıyoruz.)*
66. **"Different aggregates reference each other only by ID, and we use domain events to update other aggregates asynchronously."**
    *(Farklı aggregate'ler birbirine yalnızca ID ile referans verir ve diğer aggregate'leri asenkron olarak güncellemek için domain event'leri kullanırız.)*
67. **"A Domain Event stays inside our bounded context, while an Integration Event is a versioned contract published to other services."**
    *(Domain Event kendi bounded context'imiz içinde kalırken, Integration Event diğer servislere yayınlanan versiyonlu bir sözleşmedir.)*
68. **"Instead of giant `if-else` or `switch` blocks for different payment or shipping providers, we use the Strategy pattern."**
    *(Farklı ödeme veya kargo sağlayıcıları için devasa `if-else` veya `switch` blokları yerine Strategy desenini kullanıyoruz.)*
69. **"We resolve the right strategy at runtime using Dependency Injection based on the provider type."**
    *(Sağlayıcı tipine göre çalışma zamanında doğru stratejiyi Dependency Injection kullanarak çözümlüyoruz.)*
70. **"We use the Factory pattern to encapsulate complex object creation and keep our handlers clean."**
    *(Karmaşık nesne oluşturma süreçlerini kapsüllemek ve handler'larımızı temiz tutmak için Factory desenini kullanıyoruz.)*
71. **"We use the Decorator pattern (or MediatR pipeline behaviors) to add cross-cutting concerns like logging, validation, and caching."**
    *(Loglama, doğrulama ve önbellekleme gibi kesişen ilgileri eklemek için Decorator desenini veya MediatR pipeline davranışlarını kullanıyoruz.)*
72. **"Applying CQRS allows us to separate our transactional write model from our optimized read projections."**
    *(CQRS uygulamak, transactional yazma modelimizi optimize edilmiş okuma görünümlerimizden ayırmamıza olanak tanır.)*
73. **"For multi-step workflows across services, we use the Saga pattern with compensating actions to roll back on failure."**
    *(Servisler arası çok adımlı iş akışlarında, hata durumunda işlemleri geri almak için telafi edici aksiyonlara sahip Saga desenini kullanıyoruz.)*
74. **"When migrating a legacy monolith, I prefer the Strangler Fig pattern over a risky big-bang rewrite."**
    *(Eski bir monoliti taşırken, riskli bir 'her şeyi sıfırdan yazma' yaklaşımı yerine Strangler Fig desenini tercih ediyorum.)*
75. **"When deciding which module to extract into a microservice first, I look at business criticality and scaling needs."**
    *(Hangi modülü önce mikroservise ayıracağımıza karar verirken iş kritiklik seviyesine ve ölçeklenme ihtiyacına bakıyorum.)*
76. **"Single Responsibility Principle means a class should have only one reason to change and serve a single business actor."**
    *(Tek Sorumluluk Prensibi, bir sınıfın değişmek için yalnızca tek bir nedeni olması ve tek bir iş paydaşına hizmet etmesi demektir.)*
77. **"Dependency Inversion is what makes our backend testable because high-level policies depend on abstractions, not concrete implementations."**
    *(Yüksek seviyeli kurallar somut sınıflara değil soyutlamalara bağımlı olduğu için backend'imizi test edilebilir kılan şey Dependency Inversion prensibidir.)*
78. **"If a class requires seven or eight mocks in a unit test, that is a clear sign it violates Single Responsibility and needs refactoring."**
    *(Bir sınıf unit testte yedi-sekiz tane mock gerektiriyorsa, bu Tek Sorumluluk prensibini ihlal ettiğinin ve refactor edilmesi gerektiğinin net bir işaretidir.)*
79. **"I avoid over-engineering and follow YAGNI; I only introduce an abstraction when there is a real reason for change."**
    *(Aşırı mühendislikten kaçınır ve YAGNI prensibini izlerim; bir soyutlamayı yalnızca değişim için gerçek bir neden olduğunda eklerim.)*
80. **"We enforce our architectural boundaries automatically in CI using architecture tests so the domain layer never references infrastructure."**
    *(Domain katmanının altyapıya asla referans vermemesi için mimari sınırlarımızı CI sürecinde mimari testlerle otomatik olarak koruyoruz.)*

---

## 5. Bölüm 5: DevOps, CI/CD, Docker, Observability & Test (Cümle 81–100)

81. **"I work with a 'you build it, you run it' mindset—taking ownership from writing the C# code to monitoring it in production."**
    *('Sen yaz, sen işlet' zihniyetiyle çalışırım; C# kodunu yazmaktan canlı ortamda izlemeye kadar uçtan uca sorumluluk alırım.)*
82. **"All of our microservices are containerized with Docker so they run identically on local machines, staging, and production."**
    *(Tüm mikroservislerimiz yerel makinelerde, test ortamında ve canlıda birebir aynı şekilde çalışması için Docker ile konteynerize edilmiştir.)*
83. **"In our GitHub Actions CI/CD pipeline, every pull request must pass unit tests, integration tests, and SonarQube quality gates."**
    *(GitHub Actions CI/CD hattımızda her pull request; unit testlerden, entegrasyon testlerinden ve SonarQube kalite kapılarından geçmek zorundadır.)*
84. **"We write fast unit tests with xUnit for our domain rules and mapping logic without touching external dependencies."**
    *(Dış bağımlılıklara dokunmadan domain kurallarımız ve veri eşleme mantığımız için xUnit ile hızlı unit testler yazıyoruz.)*
85. **"For database and queue integration tests, we spin up real PostgreSQL and Kafka containers using Testcontainers."**
    *(Veritabanı ve kuyruk entegrasyon testleri için Testcontainers kullanarak gerçek PostgreSQL ve Kafka konteynerleri ayağa kaldırıyoruz.)*
86. **"Before takingover or refactoring legacy agency code, I first write integration tests around the critical flows as a safety net."**
    *(Eski veya ajans kodunu devralıp refactor etmeden önce, güvenlik ağı olarak kritik akışların etrafına entegrasyon testleri yazarım.)*
87. **"At Delivery Hero, I built a sandbox testing environment so external partners could test their API integrations end-to-end before going live."**
    *(Delivery Hero'da dış iş ortaklarının canlıya çıkmadan önce API entegrasyonlarını uçtan uca test edebilmeleri için bir sandbox test ortamı kurdum.)*
88. **"Before launching our quick-commerce platform, I ran load tests up to 500,000 requests using Locust to find bottlenecks early."**
    *(Hızlı ticaret platformumuzu yayına almadan önce darboğazları erken yakalamak için Locust kullanarak 500.000 isteğe kadar yük testleri yaptım.)*
89. **"You cannot fix what you cannot see, so structured logging and distributed tracing are always my first priority in distributed systems."**
    *(Göremediğiniz bir şeyi düzeltemezsiniz; bu yüzden dağıtık sistemlerde yapılandırılmış loglama ve dağıtık izleme her zaman ilk önceliğimdir.)*
90. **"We generate a `CorrelationId` at the API Gateway and propagate it through every HTTP header and message queue payload."**
    *(API Gateway'de bir `CorrelationId` üretiyor ve bunu her HTTP başlığı ile mesaj kuyruğu verisi boyunca taşıyoruz.)*
91. **"When an order fails across five different systems, I can search by `CorrelationId` in Datadog or Elastic APM and see the exact failure in seconds."**
    *(Bir sipariş beş farklı sistem arasında hata verdiğinde, Datadog veya Elastic APM'de `CorrelationId` ile arama yapıp hatayı saniyeler içinde görebiliyorum.)*
92. **"We expose `/health/live` and `/health/ready` endpoints in ASP.NET Core so the orchestrator knows if a container is healthy and ready for traffic."**
    *(Orkestratörün bir konteynerin sağlıklı ve trafiğe hazır olup olmadığını bilmesi için ASP.NET Core'da `/health/live` ve `/health/ready` uç noktalarını sunuyoruz.)*
93. **"We track key metrics like queue consumer lag, Dead-Letter Queue size, and p95 API latency, and trigger alerts before customers notice an issue."**
    *(Kuyruk tüketim gecikmesi, DLQ boyutu ve p95 API gecikmesi gibi temel metrikleri izliyor ve müşteriler fark etmeden önce alarmları tetikliyoruz.)*
94. **"We never hardcode secrets or connection strings; we inject them securely at runtime via environment variables or Azure Key Vault."**
    *(Şifreleri veya bağlantı cümlelerini asla koda gömmeyiz; bunları çalışma zamanında ortam değişkenleri veya Azure Key Vault üzerinden güvenle enjekte ederiz.)*
95. **"When releasing a risky change to an integration flow, we use feature flags so we can toggle it off instantly without redeploying."**
    *(Bir entegrasyon akışında riskli bir değişiklik yayınlarken, yeniden deploy etmeden anında kapatabilmek için feature flag'ler kullanıyoruz.)*
96. **"We automated the first pass of pull request reviews using GitHub Copilot Review and custom architecture rules, which saved us a full day of waiting."**
    *(GitHub Copilot Review ve özel mimari kuralları kullanarak PR incelemelerinin ilk aşamasını otomatikleştirdik; bu bize tam bir günlük bekleme süresi kazandırdı.)*
97. **"While AI tools help us write boilerplate code and tests faster, I personally review and verify every line before it goes to production."**
    *(Yapay zekâ araçları standart kodları ve testleri daha hızlı yazmamıza yardımcı olsa da, canlıya çıkmadan önce her satırı bizzat inceler ve doğrularım.)*
98. **"We keep our Docker images small and secure by using multi-stage builds and running containers as a non-root user."**
    *(Çok aşamalı derlemeler (multi-stage builds) kullanarak ve konteynerleri root olmayan kullanıcıyla çalıştırarak Docker imajlarımızı küçük ve güvenli tutuyoruz.)*
99. **"To ensure graceful shutdown during deployments, our background workers finish processing the current message before the container stops."**
    *(Canlıya alımlar sırasında düzgün kapanmayı sağlamak için, arka plan işçilerimiz konteyner durmadan önce eldeki mevcut mesajı işlemeyi tamamlar.)*
100. **"After any production incident, we hold a blameless post-mortem to fix the root cause and add automated tests so it never happens again."**
     *(Herhangi bir canlı ortam krizinden sonra, kök nedeni çözmek ve bir daha yaşanmaması için otomatik testler eklemek üzere suçlayıcı olmayan bir değerlendirme toplantısı yaparız.)*

---

## 6. Bölüm 6: Günlük Takım İletişimi, Standup, PR Review & Mülakat Kalıpları (Cümle 101–120)

101. **"In a remote team, I believe it is always better to over-communicate than to leave teammates guessing."**
     *(Uzaktan çalışan bir ekipte, ekip arkadaşlarını tahminde bırakmaktansa fazla iletişim kurmanın her zaman daha iyi olduğuna inanıyorum.)*
102. **"Yesterday I finished the NetSuite order sync worker, today I'm writing integration tests for the retry flow, and I have no blockers."**
     *(Dün NetSuite sipariş senkronizasyon işçisini bitirdim, bugün retry akışı için entegrasyon testleri yazıyorum ve önümde bir engel yok.)*
103. **"I noticed an edge case in the stock reservation requirement; could we clarify what should happen if the ERP rejects a partial shipment?"**
     *(Stok rezervasyonu gereksiniminde bir uç durum fark ettim; ERP kısmi sevkiyatı reddederse ne olması gerektiğini netleştirebilir miyiz?)*
104. **"Instead of waiting for tomorrow's standup, I wanted to flag this API rate-limit issue early so we can align on a solution."**
     *(Yarınki standup'ı beklemek yerine, bir çözüm üzerinde hizalanabilmemiz için bu API hız sınırı sorununu erkenden bildirmek istedim.)*
105. **"I documented the architectural trade-offs in a short ADR (Architecture Decision Record) so the whole team can review the options."**
     *(Tüm ekibin seçenekleri inceleyebilmesi için mimari ödünleşimleri kısa bir ADR dokümanında kayıt altına aldım.)*
106. **"When we had a disagreement about moving from the monolith to Kafka, I built a small prototype to prove the performance difference with real metrics."**
     *(Monolitten Kafka'ya geçiş konusunda bir fikir ayrılığı yaşadığımızda, performans farkını gerçek metriklerle kanıtlamak için küçük bir prototip geliştirdim.)*
107. **"In code reviews, I focus on architectural boundaries, edge cases, and readability rather than nitpicking formatting rules that linters can check."**
     *(Kod incelemelerinde, linter araçlarının kontrol edebileceği biçimlendirme detaylarına takılmak yerine mimari sınırlara, uç durumlara ve okunabilirliğe odaklanırım.)*
108. **"That's a great question—let me walk you through the problem, how we solved it, why we chose that approach, and the final result."**
     *(Bu harika bir soru; size problemi, bunu nasıl çözdüğümüzü, neden bu yaklaşımı seçtiğimizi ve nihai sonucu adım adım anlatayım.)*
109. **"Every architectural choice is a trade-off; here we traded immediate consistency for higher availability during checkout."**
     *(Her mimari seçim bir ödünleşimdir; burada ödeme adımı sırasında daha yüksek erişilebilirlik elde etmek için anlık tutarlılıktan ödün verdik.)*
110. **"For a small traffic volume, a simple database polling job is enough, but at high scale, CDC with Debezium is much more efficient."**
     *(Küçük trafik hacimleri için basit bir veritabanı sorgulama işi yeterlidir; ancak yüksek ölçekte Debezium ile CDC çok daha verimlidir.)*
111. **"To be completely transparent about team boundaries, another team owned the physical ERP, while I personally owned the order integration layer."**
     *(Ekip sınırları konusunda tamamen şeffaf olmak gerekirse, fiziksel ERP'nin sahibi başka bir ekipti; ben ise bizzat sipariş entegrasyon katmanının sorumlusuydum.)*
112. **"If I joined your team, my first priority in the first month would be understanding the critical data flows and improving observability."**
     *(Ekibinize katılırsam ilk aydaki önceliğim, kritik veri akışlarını anlamak ve gözlemlenebilirliği iyileştirmek olur.)*
113. **"Even though I haven't used that exact tool by name, I have integrated very similar ERP and e-commerce platforms using the exact same patterns."**
     *(İsim olarak tam o aracı kullanmamış olsam da, birebir aynı mimari desenleri kullanarak çok benzer ERP ve e-ticaret platformlarını entegre ettim.)*
114. **"Although I am Primarily a backend engineer in .NET, my experience with React and TypeScript helps me collaborate smoothly with frontend developers."**
     *(Öncelikli olarak bir .NET backend mühendisi olsam da, React ve TypeScript deneyimim frontend geliştiricilerle sorunsuz çalışmama yardımcı oluyor.)*
115. **"I don't just write code and throw it over the wall; I monitor how it behaves in production and make sure it delivers business value."**
     *(Sadece kodu yazıp kenara çekilmem; canlı ortamda nasıl davrandığını izler ve iş değeri ürettiğinden emin olurum.)*
116. **"Let me make sure I understood your question correctly: are you asking about how we prevent duplicates at the API gateway or inside the queue consumer?"**
     *(Sorunuzu doğru anladığımdan emin olmak istiyorum: çift kayıtları API gateway seviyesinde mi yoksa kuyruk tüketicisi içinde mi nasıl önlediğimizi soruyorsunuz?)*
117. **"If we had more time, I would also add a scheduled reconciliation job as a second safety net."**
     *(Daha fazla zamanımız olsaydı, ikinci bir güvenlik ağı olarak zamanlanmış bir mutabakat işi de eklerdim.)*
118. **"What is the biggest technical pain point in your current integration layer right now?"**
     *(Şu an mevcut entegrasyon katmanınızdaki en büyük teknik acı noktası nedir?)*
119. **"How do you currently handle local development and end-to-end testing when multiple third-party systems like NetSuite and BigCommerce are involved?"**
     *(İşin içinde NetSuite ve BigCommerce gibi birden fazla üçüncü parti sistem varken yerel geliştirmeyi ve uçtan uca testleri şu an nasıl yürütüyorsunuz?)*
120. **"Does that explanation cover what you were looking for, or would you like me to dive deeper into the database or queue side?"**
     *(Bu açıklama aradığınız noktayı karşılıyor mu, yoksa veritabanı veya kuyruk tarafında daha derine inmemi ister misiniz?)*

---

## 7. Bölüm 7: Temel C# & .NET Mülakat Soruları İçin Hazır Cevap Cümleleri (Cümle 121–150)

> 💡 **Bu bölüm**, mülakatlarda en sık sorulan **klasik C# / .NET temel sorularına** tek cümlede net ve kıdemli yanıt vermeni sağlar. Her cümlenin başında hangi sorunun cevabı olduğu parantez içinde yazılmıştır.

### Bellek Yönetimi, Value/Reference Types & Garbage Collector (121–130)

121. **`[Struct vs Class]` "A `class` is a reference type allocated on the heap, whereas a `struct` is a value type that holds its data directly and avoids GC overhead for small, short-lived data."**
     *(`class` heap'te ayrılan bir referans tipidir; `struct` ise verisini doğrudan tutan ve küçük, kısa ömürlü veriler için GC yükünden kaçınan bir değer tipidir.)*
122. **`[Stack vs Heap Tuzak Sorusu]` "A `struct` lives on the stack when declared as a local variable, but if it is a field inside a `class` or inside a `List<T>`, it is stored inline on the heap."**
     *(Bir `struct` yerel değişken olarak tanımlandığında stack'te yaşar; ancak bir `class` içinde alan ise veya `List<T>` içindeyse heap üzerinde bitişik olarak saklanır.)*
123. **`[Boxing & Unboxing]` "Boxing happens when a value type is cast to `object` or an interface, which allocates a new wrapper object on the heap and hurts performance."**
     *(Boxing, bir değer tipinin `object` veya bir arayüze dönüştürülmesiyle oluşur; bu işlem heap'te yeni bir sarmalayıcı nesne üretir ve performansı düşürür.)*
124. **`[in / ref readonly]` "When passing a large `struct` to a method, we use the `in` keyword to pass it by read-only reference instead of copying all its bytes."**
     *(Büyük bir `struct` yapısını bir metoda geçirirken tüm byte'larını kopyalamak yerine salt okunur referansla geçirmek için `in` anahtar kelimesini kullanırız.)*
125. **`[Class vs Record]` "While classes compare equality by memory reference, `record` types compare equality by value and are immutable by default, making them ideal for DTOs and Value Objects."**
     *(Sınıflar eşitliği bellek referansına göre karşılaştırırken, `record` tipleri eşitliği değere göre karşılaştırır ve varsayılan olarak değiştirilemezdir; bu da onları DTO ve Value Object'ler için ideal yapar.)*
126. **`[GC Generations]` "The .NET Garbage Collector uses three generations: Gen 0 for short-lived objects, Gen 1 as a buffer, and Gen 2 for long-lived objects like singletons or static caches."**
     *(.NET Garbage Collector üç nesil kullanır: kısa ömürlü nesneler için Gen 0, tampon olarak Gen 1 ve singleton veya statik önbellekler gibi uzun ömürlü nesneler için Gen 2.)*
127. **`[Large Object Heap - LOH]` "Any object larger than 85 kilobytes, such as a huge byte array or JSON string, goes directly to the Large Object Heap (LOH), which is expensive to collect."**
     *(Büyük bir byte dizisi veya JSON metni gibi 85 kilobayttan büyük her nesne doğrudan Large Object Heap'e (LOH) gider ve buranın temizlenmesi maliyetlidir.)*
128. **`[IDisposable & using]` "The Garbage Collector only frees managed memory; for unmanaged resources like DB connections, streams, or sockets, we implement `IDisposable` and wrap them in a `using` statement."**
     *(Garbage Collector yalnızca yönetilen belleği temizler; DB bağlantıları, stream'ler veya soketler gibi yönetilmeyen kaynaklar için `IDisposable` uygular ve bunları `using` bloğuna alırız.)*
129. **`[String vs StringBuilder]` "Because strings in C# are immutable, concatenating strings inside a loop allocates a new string in memory every time; we use `StringBuilder` instead."**
     *(C#'ta string'ler değiştirilemez olduğu için döngü içinde string birleştirmek her seferinde bellekte yeni bir string oluşturur; bunun yerine `StringBuilder` kullanırız.)*
130. **`[Span<T> & ReadOnlySpan<T>]` "When parsing large strings or byte buffers, we use `ReadOnlySpan<char>` to slice the memory without allocating new substrings on the heap."**
     *(Büyük metinleri veya byte dizilerini ayrıştırırken heap'te yeni alt string'ler oluşturmadan belleği dilimlemek için `ReadOnlySpan<char>` kullanırız.)*

### OOP, Dil Özellikleri & Koleksiyonlar (131–140)

131. **`[Interface vs Abstract Class]` "An `abstract class` shares common base code and state among closely related classes, while an `interface` defines a behavior contract that any class can implement."**
     *(Bir `abstract class` birbiriyle yakından ilişkili sınıflar arasında ortak temel kodu ve durumu paylaşır; `interface` ise herhangi bir sınıfın uygulayabileceği bir davranış sözleşmesi tanımlar.)*
132. **`[const vs readonly]` "A `const` value is baked into the assembly at compile time, while a `readonly` field is assigned at runtime inside the constructor, such as an injected service."**
     *(Bir `const` değeri derleme zamanında koda gömülürken, `readonly` alan çalışma zamanında constructor içinde—örneğin enjekte edilen bir servis gibi—atanır.)*
133. **`[sealed Class]` "We mark classes as `sealed` when they are not meant to be inherited, which also helps the JIT compiler optimize method calls."**
     *(Miras alınması planlanmayan sınıfları `sealed` olarak işaretleriz; bu aynı zamanda JIT derleyicisinin metot çağrılarını optimize etmesine yardımcı olur.)*
134. **`[throw vs throw ex]` "Inside a `catch` block, we always write `throw;` instead of `throw ex;` because `throw ex;` resets the stack trace and hides the original line where the error happened."**
     *(Bir `catch` bloğu içinde her zaman `throw ex;` yerine `throw;` yazarız; çünkü `throw ex;` stack trace'i sıfırlar ve hatanın gerçekleştiği asıl satırı gizler.)*
135. **`[Equals & GetHashCode]` "If you override `Equals()` on a custom type, you must also override `GetHashCode()` so the object works properly as a key in a `Dictionary` or `HashSet`."**
     *(Özel bir tipte `Equals()` metodunu ezerseniz, nesnenin `Dictionary` veya `HashSet` içinde anahtar olarak düzgün çalışması için `GetHashCode()` metodunu da ezmeniz gerekir.)*
136. **`[List vs Dictionary vs HashSet]` "Checking `.Contains()` on a `List<T>` is an $O(N)$ linear scan, whereas looking up an item in a `HashSet<T>` or `Dictionary<K,V>` is an $O(1)$ constant-time operation."**
     *(Bir `List<T>` üzerinde `.Contains()` kontrolü yapmak $O(N)$ doğrusal taramadır; oysa `HashSet<T>` veya `Dictionary<K,V>` içinde eleman aramak $O(1)$ sabit zamanlı bir işlemdir.)*
137. **`[Thread-Safe Collections]` "Standard collections like `List` and `Dictionary` are not thread-safe; when multiple threads access a shared cache in memory, we use `ConcurrentDictionary`."**
     *(`List` ve `Dictionary` gibi standart koleksiyonlar thread-safe değildir; birden fazla thread bellekteki ortak bir önbelleğe eriştiğinde `ConcurrentDictionary` kullanırız.)*
138. **`[LINQ Deferred Execution]` "LINQ queries like `.Where()` and `.Select()` use deferred execution—they only execute when you iterate over them with `foreach` or call `.ToList()` or `.FirstOrDefault()`."**
     *(`.Where()` ve `.Select()` gibi LINQ sorguları ertelenmiş çalıştırma (deferred execution) kullanır; yalnızca `foreach` ile dönüldüğünde veya `.ToList()` / `.FirstOrDefault()` çağrıldığında çalışırlar.)*
139. **`[yield return]` "Using `yield return` allows a method to return items lazily one by one without loading the entire collection into memory at once."**
     *(`yield return` kullanmak, bir metodun tüm koleksiyonu tek seferde belleğe yüklemeden elemanları tek tek ve tembel (lazy) olarak döndürmesini sağlar.)*
140. **`[Generics vs Reflection]` "Generics give us compile-time type safety and avoid boxing, while Reflection inspects types dynamically at runtime and is much slower unless cached."**
     *(Generics bize derleme zamanı tip güvenliği sağlar ve boxing'i önler; Reflection ise tipleri çalışma zamanında dinamik olarak inceler ve önbelleğe alınmadıkça çok daha yavaştır.)*

### Async İç Yapısı, ASP.NET Core & EF Core Temelleri (141–150)

141. **`[How async/await works]` "Under the hood, `async/await` does not create new threads; the compiler turns the method into a state machine and releases the thread back to the pool while waiting for I/O."**
     *(Kaputun altında `async/await` yeni thread oluşturmaz; derleyici metodu bir durum makinesine (state machine) dönüştürür ve I/O beklerken thread'i havuza geri bırakır.)*
142. **`[Task vs ValueTask]` "`Task` is a reference type that allocates on the heap, while `ValueTask` is a struct that avoids heap allocation when the method frequently completes synchronously."**
     *(`Task` heap'te yer ayıran bir referans tipidir; `ValueTask` ise metodun sık sık senkron olarak tamamlandığı durumlarda heap tahsisini önleyen bir struct'tır.)*
143. **`[Task.WhenAll vs await in loop]` "When we have independent async calls that can run in parallel, we start them together and use `Task.WhenAll` instead of awaiting them one by one in a loop."**
     *(Paralel çalışabilecek bağımsız asenkron çağrılarımız olduğunda, döngü içinde tek tek `await` etmek yerine hepsini başlatıp `Task.WhenAll` kullanırız.)*
144. **`[ConfigureAwait(false)]` "In ASP.NET Core, there is no `SynchronizationContext`, so `ConfigureAwait(false)` is mostly needed only when writing shared NuGet libraries."**
     *(ASP.NET Core'da `SynchronizationContext` yoktur; bu yüzden `ConfigureAwait(false)` çoğunlukla yalnızca ortak NuGet kütüphaneleri yazarken gereklidir.)*
145. **`[Middleware vs Filter]` "Middleware runs for every HTTP request in the ASP.NET Core pipeline, while Action Filters run inside the MVC context and have access to controller and action metadata."**
     *(Middleware, ASP.NET Core hattındaki her HTTP isteği için çalışır; Action Filter'lar ise MVC bağlamı içinde çalışır ve controller/action bilgilerine erişebilir.)*
146. **`[FirstOrDefault vs SingleOrDefault]` "`FirstOrDefault` returns the first matching row (`LIMIT 1`), while `SingleOrDefault` fetches up to two rows and throws an exception if there is more than one match."**
     *(`FirstOrDefault` eşleşen ilk satırı (`LIMIT 1`) döner; `SingleOrDefault` ise en fazla iki satır çeker ve birden fazla eşleşme varsa exception fırlatır.)*
147. **`[IMemoryCache vs IDistributedCache]` "`IMemoryCache` stores data in a single server's RAM, whereas `IDistributedCache` (backed by Redis) shares cached data consistently across all running pods."**
     *(`IMemoryCache` veriyi tek bir sunucunun RAM'inde tutarken, Redis destekli `IDistributedCache` önbellek verisini çalışan tüm pod'lar arasında tutarlı şekilde paylaşır.)*
148. **`[Options: IOptions vs IOptionsSnapshot]` "`IOptions<T>` is a singleton read once at startup, whereas `IOptionsSnapshot<T>` is scoped and reloads configuration changes per request without restarting the app."**
     *(`IOptions<T>` başlangıçta bir kez okunan bir singleton'dır; `IOptionsSnapshot<T>` ise scoped'dur ve uygulamayı yeniden başlatmadan istek başına konfigürasyon değişikliklerini yeniden yükler.)*
149. **`[Authentication vs Authorization]` "Authentication verifies *who* the user is (for example via a JWT token), while Authorization checks *what* that user is allowed to do via roles or policies."**
     *(Authentication kullanıcının *kim* olduğunu (örneğin JWT token ile) doğrular; Authorization ise o kullanıcının roller veya politikalar üzerinden *neyi* yapmaya yetkili olduğunu kontrol eder.)*
150. **`[Garbage Collector vs Memory Leak in .NET]` "Even with a Garbage Collector, memory leaks still happen in .NET if you keep references in static lists, forget to unsubscribe from events, or fail to dispose unmanaged resources."**
     *(Garbage Collector olsa bile, statik listelerde referans tutarsanız, event aboneliklerini kaldırmayı unutursanız veya yönetilmeyen kaynakları dispose etmezseniz .NET'te yine bellek sızıntısı (memory leak) yaşanır.)*

---

## 8. Bölüm 8: Cache Storm/Stampede, Multi-Pod Invalidation, Rate Limiting, Gateway & Tracing (Cümle 151–177)

### A. Redis Cache Expiration, Cache Storm (Stampede) & Jitter (151–160)

151. **`[Cache Storm / Stampede Nedir?]` "When a popular Redis key expires under heavy traffic, thousands of concurrent requests miss the cache at the exact same millisecond and hit PostgreSQL at once—this is called a cache stampede (or cache storm)."**
     *(Yoğun trafik altında popüler bir Redis anahtarının süresi dolduğunda, binlerce eşzamanlı istek aynı milisaniyede önbelleği ıskalar ve aynı anda PostgreSQL'e yüklenir; buna 'cache stampede' veya 'cache storm' denir.)*
152. **`[TTL Jitter Ekleme]` "If we preload 10,000 products into Redis with the exact same 1-hour TTL, they will all expire at the same second and cause a cache avalanche; to prevent this, we add random TTL jitter (for example, 60 minutes plus a random 1 to 10 minutes)."**
     *(10.000 ürünü Redis'e birebir aynı 1 saatlik TTL ile yüklersek hepsi aynı saniyede düşer ve önbellek çığına yol açar; bunu önlemek için rastgele TTL jitter ekleriz—örneğin 60 dakika artı rastgele 1 ila 10 dakika.)*
153. **`[Distributed Lock / Mutex ile Önleme]` "To stop a cache stampede on a hot key, only the first request acquires a short lock to query PostgreSQL and repopulate Redis, while the other requests wait briefly or read the newly cached value."**
     *(Çok okunan bir anahtarda cache izdihamını durdurmak için yalnızca ilk istek kısa bir kilit alıp PostgreSQL'i sorgular ve Redis'i doldurur; diğer istekler ise kısa süre bekler ve yeni önbelleğe alınan değeri okur.)*
154. **`[Soft TTL / Stale-While-Revalidate]` "For ultra-hot data like homepage campaigns, we use a soft TTL and a hard TTL: when the soft TTL expires, we immediately return the slightly stale data to the user and trigger a background worker to refresh the cache asynchronously."**
     *(Anasayfa kampanyaları gibi çok yoğun okunan verilerde 'soft TTL' ve 'hard TTL' kullanırız: soft TTL dolduğunda kullanıcıya hemen mevcut veriyi döneriz ve önbelleği asenkron olarak yenilemesi için arka planda bir iş tetikleriz.)*
155. **`[Modern .NET HybridCache]` "In modern .NET (.NET 9 and .NET 10), `HybridCache` combines in-memory L1 cache and Redis L2 cache and provides built-in stampede protection so concurrent callers share a single database fetch."**
     *(Modern .NET'te (.NET 9 ve .NET 10) `HybridCache`, bellek içi L1 önbellek ile Redis L2 önbelleği birleştirir ve yerleşik izdiham koruması sunarak eşzamanlı çağrıların tek bir veritabanı sorgusunu paylaşmasını sağlar.)*
156. **`[Cache Penetration / Olmayan Veri]` "If someone repeatedly requests a product ID that doesn't exist in the database, it bypasses Redis every time; to prevent this, we cache a `null` placeholder with a short 1-minute TTL."**
     *(Birisi veritabanında olmayan bir ürün ID'sini sürekli sorgularsa her seferinde Redis'i geçip DB'ye iner; bunu önlemek için `null` sonucu 1 dakikalık kısa bir TTL ile önbelleğe alırız.)*

### B. Aynı Anda Cache Güncelleme & Diğer Pod'lardan Cache Düşürme (161–167)

157. **`[Aynı Anda Cache Güncelleme Yarışı]` "If two threads update the database and then overwrite Redis directly, a slower thread might overwrite Redis with older data and leave the cache stale."**
     *(İki thread veritabanını güncelleyip ardından doğrudan Redis'in üzerine yazarsa, daha yavaş olan thread eski veriyi Redis'e yazıp önbelleği bayat bırakabilir.)*
158. **`[Write-Update Yerine Cache Invalidation]` "To avoid race conditions when updating cache, we prefer the Cache-Aside pattern: after updating PostgreSQL, we delete (invalidate) the Redis key instead of overwriting it, so the next read fetches fresh data."**
     *(Önbelleği güncellerken yarış durumlarından kaçınmak için Cache-Aside desenini tercih ederiz: PostgreSQL'i güncelledikten sonra Redis anahtarının üzerine yazmak yerine onu sileriz (invalidate); böylece sonraki okuma en güncel veriyi çeker.)*
159. **`[Lua Script & Versiyon ile Güncelleme]` "When we must update Redis directly from events, we use a Lua script or check the version number so Redis only accepts the update if the incoming version is newer than the cached version."**
     *(Redis'i event'ler üzerinden doğrudan güncellememiz gerektiğinde bir Lua scripti kullanırız veya versiyon numarasını kontrol ederiz; böylece Redis yalnızca gelen versiyon önbellektekinden daha yeniyse güncellemeyi kabul eder.)*
160. **`[Diğer Örneklerden/Pod'lardan L1 Cache Düşürme - PROBLEM]` "When we run 10 Kubernetes pods with local in-memory cache (`IMemoryCache`) in front of Redis, updating a product on Pod 1 leaves stale data in the local RAM of the other 9 pods."**
     *(Redis'in önünde yerel bellek önbelleği (`IMemoryCache`) kullanan 10 Kubernetes pod'u çalıştırdığımızda, Pod 1'de bir ürünü güncellemek diğer 9 pod'un yerel RAM'inde eski veri kalmasına neden olur.)*
161. **`[Redis Pub/Sub Backplane ile Çözüm]` "To invalidate local in-memory caches across all running pods, we use Redis Pub/Sub as a backplane: when Pod 1 invalidates a key, it publishes an invalidation message, and all other pods immediately evict that key from their local `IMemoryCache`."**
     *(Çalışan tüm pod'lardaki yerel bellek önbelleklerini temizlemek için arka kanal (backplane) olarak Redis Pub/Sub kullanırız: Pod 1 bir anahtarı sildiğinde bir geçersiz kılma mesajı yayınlar ve diğer tüm pod'lar o anahtarı kendi yerel `IMemoryCache` belleklerinden anında siler.)*
162. **`[Kafka/Event ile Cache Senkronizasyonu]` "Alternatively, all pods can subscribe to a Kafka or RabbitMQ `ProductUpdated` topic to evict stale entries from their local memory cache in real time."**
     *(Alternatif olarak, tüm pod'lar kendi yerel bellek önbelleklerindeki eski kayıtları gerçek zamanlı olarak silmek için bir Kafka veya RabbitMQ `ProductUpdated` başlığına abone olabilir.)*

### C. Rate Limiting & API Gateway (168–176)

163. **`[API Gateway Rolü]` "We put an API Gateway (like Kong, YARP, or Azure API Management) in front of our microservices to handle authentication, SSL termination, routing, and rate limiting in one central place."**
     *(Kimlik doğrulama, SSL sonlandırma, yönlendirme ve hız sınırlandırma işlemlerini tek bir merkezi noktada yönetmek için mikroservislerimizin önüne Kong, YARP veya Azure API Management gibi bir API Gateway koyuyoruz.)*
164. **`[Inbound Rate Limiting]` "We configure rate limiting at the API Gateway and in ASP.NET Core middleware per IP address or client token to protect our backend from traffic spikes and brute-force attacks."**
     *(Backend'imizi ani trafik patlamalarından ve kaba kuvvet saldırılarından korumak için API Gateway'de ve ASP.NET Core middleware katmanında IP adresi veya istemci token'ı bazında hız sınırı yapılandırıyoruz.)*
165. **`[Sliding Window vs Token Bucket]` "We use a Sliding Window or Token Bucket algorithm backed by Redis so our rate limits are enforced accurately across all backend instances."**
     *(Hız sınırlarımızın tüm backend sunucuları genelinde doğru şekilde uygulanması için Redis destekli bir Sliding Window (Kayan Pencere) veya Token Bucket (Jeton Kovası) algoritması kullanıyoruz.)*
166. **`[429 Too Many Requests & Retry-After]` "When a client exceeds the rate limit, we reject the request at the gateway with HTTP `429 Too Many Requests` and include a `Retry-After` header."**
     *(Bir istemci hız sınırını aştığında, isteği daha gateway seviyesinde HTTP `429 Too Many Requests` ile reddediyor ve yanıt içine bir `Retry-After` başlığı ekliyoruz.)*
167. **`[Outbound Rate Limiting / ERP Koruma]` "Rate limiting is also critical for outgoing calls: since ERPs like NetSuite have strict API concurrency limits, we throttle our queue workers so we never flood the ERP."**
     *(Hız sınırlandırma giden çağrılar için de kritiktir: NetSuite gibi ERP'lerin katı API eşzamanlılık sınırları olduğundan, ERP'yi asla boğmamak için kuyruk işçilerimizin hızını dizginliyoruz.)*
168. **`[API Gateway & Strangler Fig]` "When migrating from a legacy system or external agency code, the API Gateway lets us route migrated endpoints to our new .NET services while forwarding the rest to the old system."**
     *(Eski bir sistemden veya ajans kodundan geçiş yaparken API Gateway, taşınmış uç noktaları yeni .NET servislerimize yönlendirmemizi, geri kalanları ise eski sisteme iletmemizi sağlar.)*
169. **`[BFF - Backend for Frontend]` "We use the Backend-for-Frontend (BFF) pattern at the gateway layer to aggregate data from multiple services into a single response tailored for the React frontend or mobile app."**
     *(Birden fazla servisten gelen veriyi React arayüzüne veya mobil uygulamaya özel tek bir yanıtta birleştirmek için gateway katmanında Backend-for-Frontend (BFF) desenini kullanıyoruz.)*

### D. Structured Logging, Distributed Tracing & Observability (177–185)

170. **`[Structured Logging vs Plain Text]` "Instead of writing plain-text logs that are hard to search, we use Serilog to write structured JSON logs with properties like `OrderId`, `Sku`, and `CustomerId`."**
     *(Araması zor olan düz metin loglar yazmak yerine, `OrderId`, `Sku` ve `CustomerId` gibi alanlar içeren yapılandırılmış JSON logları yazmak için Serilog kullanıyoruz.)*
171. **`[CorrelationId & W3C TraceContext]` "Every incoming request gets a `CorrelationId` (or OpenTelemetry W3C `traceparent` header) at the API Gateway, which is automatically attached to every log entry."**
     *(Gelen her istek API Gateway'de bir `CorrelationId` (veya OpenTelemetry W3C `traceparent` başlığı) alır ve bu kimlik her log kaydına otomatik olarak eklenir.)*
172. **`[HTTP & Queue Boyunca Trace Taşıma]` "When Service A calls Service B via HTTP or publishes a message to Kafka or SQS, we pass the `TraceId` in the headers so the distributed trace stays connected end-to-end."**
     *(A Servisi B Servisini HTTP ile çağırdığında veya Kafka/SQS'e mesaj bıraktığında, dağıtık izlemenin uçtan uca bağlı kalması için `TraceId` değerini başlıklarda iletiyoruz.)*
173. **`[Datadog / Elastic APM Waterfall]` "In Datadog or Elastic APM, distributed tracing gives us a waterfall view of the entire request, showing exactly how many milliseconds were spent in .NET, PostgreSQL, Redis, or an external API."**
     *(Datadog veya Elastic APM'de dağıtık izleme bize tüm isteğin şelale (waterfall) görünümünü verir ve .NET, PostgreSQL, Redis veya bir dış API'de tam olarak kaç milisaniye harcandığını gösterir.)*
174. **`[Log Levels & Gürültüyü Önleme]` "In production, we keep our default log level at `Information` or `Warning` and avoid logging full payloads on every request so we don't pay huge storage costs or slow down I/O."**
     *(Canlı ortamda varsayılan log seviyemizi `Information` veya `Warning` düzeyinde tutarız ve yüksek depolama maliyetleri ödememek veya I/O'yu yavaşlatmamak için her istekte tüm veri gövdesini loglamaktan kaçınırız.)*
175. **`[GDPR & PII Masking]` "To comply with GDPR and security standards, we mask sensitive PII data—like emails, phone numbers, tokens, and payment details—before writing logs to Elasticsearch or Datadog."**
     *(GDPR ve güvenlik standartlarına uymak için e-posta, telefon numarası, token ve ödeme bilgileri gibi hassas kişisel verileri (PII) Elasticsearch veya Datadog'a yazmadan önce maskeliyoruz.)*
176. **`[Sampling / Örnekleme]` "Under very high traffic (like 25 million packets a day at Drivee), we trace 100% of failed and slow requests, and use sampling for normal fast `200 OK` requests."**
     *(Drivee'deki günde 25 milyon paket gibi çok yüksek trafik altında, hatalı ve yavaş isteklerin %100'ünü izleriz; normal ve hızlı `200 OK` istekleri içinse örnekleme (sampling) kullanırız.)*
177. **`[Alerting on Symptoms]` "We set up proactive alerts on real symptoms—like a spike in `5xx` errors, Dead-Letter Queue growth, or ERP sync lag—so we can react before customers report a problem."**
     *(Müşteriler bir sorun bildirmeden önce müdahale edebilmek için `5xx` hatalarında artış, Dead-Letter Queue büyümesi veya ERP senkronizasyon gecikmesi gibi gerçek belirtiler üzerine proaktif alarmlar kuruyoruz.)*

---

## 9. Bölüm 9: E-Ticaret Sipariş Süreçleri — Sepet, Ödeme, Stok Rezervasyonu, State Machine & İade (Cümle 178–205)

### A. Sepet (Basket) vs. Sipariş (Order), Fiyat Snapshot & Stok Rezervasyon TTL (178–185)

178. **`[Basket vs Order Ayrımı]` "A basket is a mutable, temporary shopping session where prices can still change, whereas an order is an immutable financial and legal record created at checkout."**
     *(Sepet, fiyatların hâlâ değişebildiği geçici ve düzenlenebilir bir alışveriş oturumudur; sipariş ise ödeme anında oluşturulan değiştirilemez bir finansal ve yasal kayıttır.)*
179. **`[Fiyat & Vergi Snapshot'ı]` "When an order is placed, we take an immutable snapshot of the product name, unit price, applied discount, currency, and VAT rate so future catalog changes never alter historical orders."**
     *(Sipariş verildiğinde ürün adının, birim fiyatın, uygulanan indirimin, para biriminin ve KDV oranının değiştirilemez bir kopyasını (snapshot) alırız; böylece gelecekteki katalog değişiklikleri geçmiş siparişleri asla bozmaz.)*
180. **`[Stok Ne Zaman Rezerve Edilir?]` "We don't reserve stock when an item is just added to the cart because that would allow bots or window-shoppers to lock up all our inventory."**
     *(Ürün sadece sepete eklendiğinde stok rezerve etmeyiz; çünkü bu durum botların veya sadece gezinen kullanıcıların tüm stoğumuzu kilitlemesine yol açar.)*
181. **`[Checkout Anında TTL'li Rezervasyon]` "Instead, we create a temporary stock reservation with a 15-minute TTL when the customer starts checkout and is redirected to the payment provider."**
     *(Bunun yerine, müşteri ödeme adımını başlatıp ödeme sağlayıcıya yönlendirildiğinde 15 dakikalık süresi (TTL) olan geçici bir stok rezervasyonu oluştururuz.)*
182. **`[Terk Edilen Ödeme / Expired Reservation]` "If the customer abandons the payment page and we don't receive a payment webhook within 15 minutes, a background job automatically expires the reservation and returns the stock to the sellable pool."**
     *(Müşteri ödeme sayfasını terk ederse ve 15 dakika içinde ödeme bildirimi alamazsak, arka planda çalışan bir iş rezervasyonu otomatik olarak sonlandırır ve stoğu tekrar satılabilir havuza iade eder.)*
183. **`[Kupon Kullanım Limiti & Concurrency]` "To prevent a single-use coupon from being used twice at the same millisecond, we increment the coupon usage count atomically in the database with `WHERE used_count < max_uses`."**
     *(Tek kullanımlık bir kuponun aynı milisaniyede iki kez kullanılmasını önlemek için kupon kullanım sayacını veritabanında `WHERE used_count < max_uses` koşuluyla atomik olarak artırırız.)*
184. **`[Set / Bundle Ürün Stoğu - DIY Hardware]` "For bundle or kit products—like a pergola kit made of brackets and screws—the available stock of the bundle is calculated dynamically from the lowest available component in stock."**
     *(Pergola bağlantı demirleri ve vidalardan oluşan bir set gibi paket ürünlerde, paketin satılabilir stoğu bileşenlerden stokta en az bulunanına göre dinamik olarak hesaplanır.)*
185. **`[12 Ülke KDV & Çoklu Dil]` "When selling across 12 European countries, our order payload explicitly stores the destination country's VAT rate and local currency so NetSuite ERP can generate compliant invoices."**
     *(12 Avrupa ülkesinde satış yaparken, NetSuite ERP'nin mevzuata uygun fatura kesebilmesi için sipariş verimizde teslimat ülkesinin KDV oranını ve yerel para birimini açıkça saklarız.)*

### B. Ödeme (PSP) Entegrasyonu, Webhook Güvenliği & Uyumsuzluk Çözümü (186–192)

186. **`[Tarayıcı Yönlendirmesine Güvenmeme]` "In payment integrations, we never rely on the user's browser redirect to confirm payment because the user might close the browser tab right after paying."**
     *(Ödeme entegrasyonlarında ödemeyi onaylamak için asla kullanıcının tarayıcı yönlendirmesine güvenmeyiz; çünkü kullanıcı ödeme yaptıktan hemen sonra tarayıcı sekmesini kapatabilir.)*
187. **`[PSP Webhook = Doğruluk Kaynağı]` "The asynchronous webhook from the Payment Service Provider (PSP) is our reliable trigger to mark the order as `Paid` and release it to the ERP."**
     *(Ödeme Hizmet Sağlayıcısından (PSP) gelen asenkron webhook, siparişi `Ödendi` olarak işaretlemek ve ERP'ye göndermek için kullandığımız güvenilir tetikleyicidir.)*
188. **`[Webhook İmza Doğrulaması]` "Before processing any payment webhook, we verify the cryptographic HMAC signature in the header to make sure the request genuinely came from the PSP."**
     *(Herhangi bir ödeme webhook'unu işlemeden önce, isteğin gerçekten ödeme sağlayıcıdan geldiğinden emin olmak için başlıktaki kriptografik HMAC imzasını doğrularız.)*
189. **`[Idempotent Webhook İşleme]` "Payment providers often send the same `PaymentSucceeded` webhook multiple times, so we deduplicate webhooks by `PaymentTransactionId` in PostgreSQL."**
     *(Ödeme sağlayıcılar aynı `PaymentSucceeded` webhook'unu sık sık birden fazla kez gönderir; bu yüzden webhook'ları PostgreSQL'de `PaymentTransactionId` üzerinden tekilleştiririz.)*
190. **`[Authorize vs Capture]` "Depending on the payment method, we either capture the payment immediately at checkout or authorize the amount first and capture it when the warehouse ships the order."**
     *(Ödeme yöntemine bağlı olarak, tutarı ya ödeme anında hemen tahsil ederiz (capture) ya da önce provizyon (authorize) alıp depo siparişi kargoladığında tahsilatı gerçekleştiririz.)*
191. **`[Ödeme Alındı Ama Stok Bitti - Geç Webhook]` "In the rare edge case where a late payment webhook arrives after the stock reservation expired and the last item was sold, we automatically trigger a refund or flag the order for priority backorder fulfillment."**
     *(Stok rezervasyon süresi dolup son ürün satıldıktan sonra gecikmiş bir ödeme webhook'unun geldiği nadir uç durumda, otomatik olarak iade (refund) başlatırız veya siparişi öncelikli tedarik için işaretleriz.)*
192. **`[PSP Mutabakatı / Settlement Reconciliation]` "Every night, we reconcile captured PSP transactions against our `Paid` orders in PostgreSQL and NetSuite so no payment ever gets stuck silently."**
     *(Her gece, tahsil edilen PSP işlemlerini PostgreSQL ve NetSuite'teki `Ödendi` siparişlerimizle karşılaştırıp mutabakat yaparız; böylece hiçbir ödeme sessizce askıda kalmaz.)*

### C. Sipariş Durum Makinesi (Order State Machine), Kargo & İade Lojistiği (193–205)

193. **`[Order State Machine]` "We model the order lifecycle as a strict state machine (`PendingPayment` → `Paid` → `SentToErp` → `Picking` → `Shipped` → `Delivered`) so invalid transitions are rejected in the domain layer."**
     *(Sipariş yaşam döngüsünü katı bir durum makinesi olarak modelleriz; böylece geçersiz durum geçişleri daha domain katmanında reddedilir.)*
194. **`[İptal Kuralı / Invariant]` "For example, if a customer requests a cancellation while the order is already in `Picking` or `Shipped` state in the warehouse, our state machine blocks direct cancellation and requires a return flow instead."**
     *(Örneğin müşteri, sipariş depoda `Toplanıyor` veya `Kargolandı` durumundayken iptal talep ederse durum makinemiz doğrudan iptali engeller ve bunun yerine iade akışı başlatılmasını zorunlu kılar.)*
195. **`[Canonical Order Model - Webshop & Pazaryerleri]` "Whether an order comes from BigCommerce or an external marketplace via Channable, our Anti-Corruption Layer maps it into a single Canonical Order Model before sending it to the core pipeline."**
     *(Bir sipariş ister BigCommerce'den ister Channable üzerinden bir dış pazaryerinden gelsin, Anti-Corruption Layer katmanımız onu ana boru hattına göndermeden önce tek bir Ortak Sipariş Modeline dönüştürür.)*
196. **`[Pazaryeri Sipariş Farkı]` "Marketplace orders arrive already paid on the marketplace side, so our state machine skips the payment capture step and moves directly to stock reservation and ERP synchronization."**
     *(Pazaryeri siparişleri pazaryeri tarafında ödemesi alınmış olarak gelir; bu nedenle durum makinemiz ödeme tahsilat adımını atlar ve doğrudan stok rezervasyonu ile ERP senkronizasyonuna geçer.)*
197. **`[Split Shipment / Kısmi Sevkiyat]` "If a customer orders five items and they are shipped in two separate parcels, our data model tracks shipment status per order line rather than just a single status on the order header."**
     *(Müşteri beş ürün sipariş eder ve bunlar iki ayrı paket halinde kargolanırsa, veri modelimiz kargo durumunu sadece sipariş başlığında tek bir durum olarak değil, sipariş satırı (order line) bazında takip eder.)*
198. **`[Short Pick / Depoda Hasarlı Ürün]` "If the warehouse worker finds during picking that the last item on the shelf is damaged, the ERP sends a short-pick event to our integration layer to re-allocate that line or trigger a partial refund."**
     *(Depo çalışanı ürün toplama sırasında raftaki son ürünün hasarlı olduğunu fark ederse, ERP entegrasyon katmanımıza o satırı başka depoya atamak veya kısmi para iadesi başlatmak için bir 'eksik toplama' (short-pick) olayı gönderir.)*
199. **`[Kuponlu Siparişte Kısmi İade / Discount Proration]` "When a coupon discount is applied to a basket, we prorate (distribute) the discount amount across each order line proportionally; that way, if the customer returns just one item later, we know the exact net amount to refund."**
     *(Bir sepete kupon indirimi uygulandığında indirim tutarını her bir sipariş satırına orantılı olarak dağıtırız (proration); böylece müşteri daha sonra tek bir ürünü iade ederse iade edeceğimiz net tutarı kuruşu kuruşuna biliriz.)*
200. **`[RMA - İade Süreci & Stok Güncellemesi]` "When a customer initiates a return (RMA), we don't add the item back to sellable stock immediately; we wait until the warehouse inspects the returned parcel and confirms the item is undamaged in the ERP."**
     *(Müşteri bir iade (RMA) başlattığında ürünü hemen satılabilir stoğa geri eklemeyiz; deponun iade paketi inceleyip ürünün sağlam olduğunu ERP'de onaylamasını bekleriz.)*
201. **`[İade Onayı → PSP Refund & Fatura]` "Once the warehouse approves the return in NetSuite, our integration layer receives an event, triggers an idempotent refund call to the PSP, and generates a credit note."**
     *(Depo NetSuite üzerinde iadeyi onayladığında, entegrasyon katmanımız bir event alır, ödeme sağlayıcıya (PSP) idempotent bir para iadesi çağrısı tetikler ve iade faturası (credit note) oluşturur.)*
202. **`[Kargo Takip & Klaviyo Bildirimleri]` "When the warehouse generates a shipping label, our .NET layer syncs the tracking number back to BigCommerce andChannable, and publishes an event to Klaviyo so the customer receives their tracking email."**
     *(Depo kargo barkodunu oluşturduğunda, .NET katmanımız kargo takip numarasını BigCommerce ve Channable'a geri bildirir ve müşterinin takip e-postasını alması için Klaviyo'ya bir event yayınlar.)*
203. **`[Sipariş Sıralaması / Out-of-Order Events]` "Sometimes an `OrderShipped` webhook from the warehouse can arrive milliseconds before the `OrderPacked` event; our consumer handles out-of-order events gracefully by checking state priority."**
     *(Bazen depodan gelen `Sipariş Kargolandı` bildirimi, `Sipariş Paketlendi` olayından milisaniyeler önce gelebilir; consumer servisimiz durum önceliğini kontrol ederek sırası karışmış olayları sorunsuz yönetir.)*
204. **`[PIM -> Webshop Ürün Senkronizasyonu]` "When product specifications or translations are updated in Akeneo PIM, our integration layer validates the required fields before syncing them to BigCommerce so incomplete products never go live."**
     *(Akeneo PIM'de ürün özellikleri veya çeviriler güncellendiğinde, eksik ürünlerin asla yayına çıkmaması için entegrasyon katmanımız bunları BigCommerce'e senkronize etmeden önce zorunlu alanları doğrular.)*
205. **`[Audit Trail / Sipariş Tarihçesi]` "For customer support visibility, we keep an append-only `order_status_history` table in PostgreSQL that logs every state change, timestamp, and system trigger for every order."**
     *(Müşteri hizmetlerinin süreci net görebilmesi için PostgreSQL'de her siparişin tüm durum değişikliklerini, zamanını ve hangi sistem tarafından tetiklendiğini kaydeden 'sadece eklenir' (append-only) bir `order_status_history` tablosu tutarız.)*




