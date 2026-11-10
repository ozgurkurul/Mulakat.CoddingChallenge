# 🗣️ Senior .NET Developer — Mülakat ve Günlük Mühendislik İçin 120 Altın İngilizce Cümle (`English-Helper.md`)

> **Amaç:** Bu dosya, bir Senior .NET Backend geliştiricinin teknik mülakatlarda ve uluslararası (remote) ekiplerde günlük olarak en çok kurduğu **120 tam, doğal ve akıcı İngilizce cümleyi** içerir.
> **Nasıl Çalışmalısın?** Tek tek kelime ezberlemek yerine her gün 1-2 bölümü (20–40 cümleyi) **sesli olarak** oku. Kalın yazılmış anahtar kelimelerin cümle içinde nasıl kullanıldığına dikkat et.

---

## 📑 İçindekiler

1. [Bölüm 1: C# & .NET Backend Geliştirme (Cümle 1–20)](#1-bölüm-1-c--net-backend-geliştirme-cümle-120)
2. [Bölüm 2: Veritabanı, PostgreSQL, EF Core & Redis (Cümle 21–40)](#2-bölüm-2-veritabanı-postgresql-ef-core--redis-cümle-2140)
3. [Bölüm 3: Sistem Dizaynı, Event-Driven Mimari & Entegrasyon (Cümle 41–60)](#3-bölüm-3-sistem-dizaynı-event-driven-mimari--entegrasyon-cümle-4160)
4. [Bölüm 4: Design Patterns, SOLID, DDD & Clean Architecture (Cümle 61–80)](#4-bölüm-4-design-patterns-solid-ddd--clean-architecture-cümle-6180)
5. [Bölüm 5: DevOps, CI/CD, Docker, Observability & Test (Cümle 81–100)](#5-bölüm-5-devops-cicd-docker-observability--test-cümle-81100)
6. [Bölüm 6: Günlük Takım İletişimi, Standup, PR Review & Mülakat Kalıpları (Cümle 101–120)](#6-bölüm-6-günlük-takım-iletişimi-standup-pr-review--mülakat-kalıpları-cümle-101120)

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

