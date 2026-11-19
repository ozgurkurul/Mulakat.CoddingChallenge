# 🏗️ E-Ticaret, DDD ve API Entegrasyonlarında Tasarım Desenleri (`design-patterns.md`)

> **Amaç:** Bir Senior .NET Backend geliştiricinin e-ticaret, DDD (Domain-Driven Design) ve çoklu sistem entegrasyonlarında (ERP, PIM, PSP, Pazaryeri) kullandığı tasarım desenlerini **ezbere kitaptan değil; "Hangi problemi çözer? Neden seçtik? Nasıl kodlanır? Mülakatta İngilizce nasıl anlatılır?"** çerçevesiyle sunmak.

---

## 📑 İçindekiler

0. [Hızlı Karar Matrisi: Hangi Sorunda Hangi Pattern Kullanılır?](#0-hızlı-karar-matrisi-hangi-sorunda-hangi-pattern-kullanılır)
1. [Katman A: Klasik (GoF) Desenlerin E-Ticaret Senaryoları](#1-katman-a-klasik-gof-desenlerin-e-ticaret-senaryoları)
   - [1. Strategy Pattern (Ödeme, Kargo ve İndirim Hesaplama)](#11-strategy-pattern-ödeme-kargo-ve-indirim-hesaplama)
   - [2. Factory Pattern & DI Keyed Services (Nesne ve Strateji Üretimi)](#12-factory-pattern--di-keyed-services-nesne-ve-strateji-üretimi)
   - [3. Adapter Pattern & Anti-Corruption Layer - ACL (ERP, PIM ve Pazaryeri Entegrasyonu)](#13-adapter-pattern--anti-corruption-layer---acl-erp-pim-ve-pazaryeri-entegrasyonu)
   - [4. Decorator Pattern & MediatR Pipeline Behaviors (Caching, Logging, Validation)](#14-decorator-pattern--mediatr-pipeline-behaviors-caching-logging-validation)
   - [5. State Pattern / Order State Machine (Sipariş Durum Geçişleri)](#15-state-pattern--order-state-machine-sipariş-durum-geçişleri)
   - [6. Specification & Chain of Responsibility (Kampanya ve Sipariş Kuralları)](#16-specification--chain-of-responsibility-kampanya-ve-sipariş-kuralları)
2. [Katman B: DDD (Domain-Driven Design) ve Uygulama Mimarisi Desenleri](#2-katman-b-ddd-domain-driven-design-ve-uygulama-mimarisi-desenleri)
   - [7. Aggregate Root & Value Object Pattern (Sipariş Bütünlüğü ve Para/SKU)](#21-aggregate-root--value-object-pattern-sipariş-bütünlüğü-ve-parasku)
   - [8. Domain Events vs. Integration Events (Servis İçi ve Servisler Arası Olaylar)](#22-domain-events-vs-integration-events-servis-içi-ve-servisler-arası-olaylar)
   - [9. CQRS & Mediator Pattern (Okuma ve Yazma Sorumluluğunu Ayırma)](#23-cqrs--mediator-pattern-okuma-ve-yazma-sorumluluğunu-ayırma)
   - [10. Repository & Unit of Work Pattern (Atomik Kayıt)](#24-repository--unit-of-work-pattern-atomik-kayıt)
3. [Katman C: Dağıtık Sistem, API ve Entegrasyon Desenleri (Wovar'ın Kalbi)](#3-katman-c-dağıtık-sistem-api-ve-entegrasyon-desenleri-wovarın-kalbi)
   - [11. Transactional Outbox & Inbox (Idempotent Consumer) Pattern](#31-transactional-outbox--inbox-idempotent-consumer-pattern)
   - [12. Saga Pattern & Compensating Actions (Dağıtık İşlem Geri Alma)](#32-saga-pattern--compensating-actions-dağıtık-işlem-geri-alma)
   - [13. Resilience Patterns: Retry, Circuit Breaker & Bulkhead](#33-resilience-patterns-retry-circuit-breaker--bulkhead)
   - [14. Strangler Fig Pattern (Ajans/Legacy Kodunu Kademeli Devralma)](#34-strangler-fig-pattern-ajanslegacy-kodunu-kademeli-devralma)
4. [Mülakatta Altın Değerinde 3 Prensip (Over-Engineering Tuzağından Kaçınma)](#4-mülakatta-altın-değerinde-3-prensip-over-engineering-tuzağından-kaçınma)

---

## 0. Hızlı Karar Matrisi: Hangi Sorunda Hangi Pattern Kullanılır?

| E-Ticaret / API Problemi | Kullanılan Pattern | Neden ve Amacı (Tek Cümle) |
|---|---|---|
| Farklı ödeme (Adyen, Mollie, Stripe) veya kargo (DHL, PostNL) firmalarına göre değişen iş mantığı | **Strategy Pattern** | Devasa `if-else / switch` bloklarını kaldırır; yeni firma eklendiğinde mevcut kodu değiştirmeden yeni sınıf eklenir (**OCP**). |
| Gelen siparişteki ülke veya kanal bilgisine göre doğru stratejiyi seçip üretmek | **Factory / Keyed DI** | "Hangi sınıf üretilecek?" kararını merkezi bir noktaya alır, iş kodunu `new` bağımlılığından kurtarır. |
| NetSuite ERP, Akeneo PIM veya Channable'ın karmaşık JSON modellerinin bizim domain'imizi kirletmesi | **Adapter / Anti-Corruption Layer (ACL)** | Dış sistemin veri modelini bizim temiz C# domain modelimize çevirir; dış sistem değişirse sadece Adapter değişir. |
| İş koduna dokunmadan tüm sorgulara Redis Cache, Loglama, Validation veya Retry eklemek | **Decorator / Pipeline Behavior** | İş sınıfı sadece işini yapar (**SRP**); önbellekleme ve loglama dışarıdan sarmalayıcı (wrapper) olarak eklenir. |
| Kargolanmış siparişin yanlışlıkla iptal edilmesi gibi geçersiz durum geçişleri | **State Machine Pattern** | Siparişin hangi durumdan hangi duruma geçebileceğini katı kurallarla korur. |
| Karmaşık kampanya uygunluk kuralları (Sepet > €50 VE kategori = Vida VE ilk sipariş) | **Specification Pattern** | İş kurallarını tek tek test edilebilir ve `AND/OR` ile birleştirilebilir küçük sınıflara böler. |
| Sipariş satırlarının (`OrderLine`) dışarıdan doğrudan değiştirilip sipariş toplamının bozulması | **Aggregate Root & Value Object** | Tüm değişiklikleri `Order` kök nesnesi üzerinden zorunlu kılar ve `Money` gibi değerleri değiştirilemez (`immutable`) yapar. |
| Okuma (ürün/sipariş listeleme) ve yazma (sipariş oluşturma) ihtiyaçlarının çok farklı olması | **CQRS** | Yazma tarafında EF Core ile iş kurallarını korur, okuma tarafında Dapper/projeksiyon ile maksimum hız sağlar. |
| Veritabanına sipariş yazılıp Kafka'ya mesaj atılırken ağın kopması (Dual-Write) | **Transactional Outbox & Inbox** | İş verisi ve event'i aynı DB transaction'ında kaydeder; consumer tarafında çift işlemeyi engeller. |
| Ödeme başarısız olduğunda önceki adımda ayrılan stoğun geri bırakılması | **Saga (Compensating Action)** | Çoklu sistemde bir adım patladığında önceki adımları ters işlemle geri alır (telafi eder). |
| Çökmüş bir ERP'ye sürekli istek atıp kendi sunucumuzun thread/bağlantı havuzunu tüketmek | **Circuit Breaker & Retry** | Geçici hataları tekrar dener; sistem tamamen çökmüşse devreyi kesip kuyrukta bekletir. |
| Dış ajansın yazdığı veya eski monolit kodu canlıyı durdurmadan yenilemek | **Strangler Fig Pattern** | Eski sistemi tek seferde yıkmak yerine modül modül yeni .NET servislerine yönlendirir. |

---

## 1. Katman A: Klasik (GoF) Desenlerin E-Ticaret Senaryoları

### 1.1. Strategy Pattern (Ödeme, Kargo ve İndirim Hesaplama)

- **🎯 E-Ticaret Senaryosu:** Wovar 12 Avrupa ülkesinde satış yapıyor. Hollanda'da müşteriler **iDEAL / Mollie**, Almanya'da **Klarna / Adyen**, Fransa'da **Kredi Kartı / Stripe** kullanıyor. Veya kargo ücreti hesaplanırken ağır metal/vida kolileri için **DHL Freight**, küçük paketler için **PostNL** kuralları çalışıyor.
- **❓ Neden ve Amaç:** Eğer `PaymentService` içinde `if (method == "Mollie") ... else if (method == "Adyen")` yazarsak, her yeni ödeme yöntemi eklendiğinde çalışan kodu değiştirmek zorunda kalırız ve başka bir ülkenin ödemesini bozma riskimiz doğar (**Open/Closed Principle ihlali**). Strategy deseni ile her ödeme/kargo sağlayıcısını kendi izole sınıfına ayırırız.
- **💻 C# (.NET) Örneği:**
  ```csharp
  public interface IPaymentProviderStrategy
  {
      string ProviderName { get; }
      Task<PaymentResult> ProcessPaymentAsync(Order order, CancellationToken ct);
  }

  public sealed class MolliePaymentStrategy : IPaymentProviderStrategy
  {
      public string ProviderName => "Mollie";
      public async Task<PaymentResult> ProcessPaymentAsync(Order order, CancellationToken ct)
      {
          // Mollie API çağrısı
          return PaymentResult.Success("mollie_tx_123");
      }
  }

  public sealed class AdyenPaymentStrategy : IPaymentProviderStrategy
  {
      public string ProviderName => "Adyen";
      public async Task<PaymentResult> ProcessPaymentAsync(Order order, CancellationToken ct)
      {
          // Adyen API çağrısı
          return PaymentResult.Success("adyen_tx_456");
      }
  }
  ```
- **🗣️ Mülakatta İngilizce Anlatımı (`Problem → Solution → Reason → Result`):**
  > *"When supporting multiple payment providers or shipping carriers across 12 countries, putting `if-else` blocks inside the order service quickly becomes unmaintainable. To solve this, we use the **Strategy pattern** by defining an `IPaymentProviderStrategy` interface and implementing a separate class for each provider. The reason is to follow the **Open/Closed Principle**—when we add a new payment provider, we just add a new class without touching existing payment code. As a result, each provider can be unit-tested in isolation with zero risk of breaking other checkout flows."*

---

### 1.2. Factory Pattern & DI Keyed Services (Nesne ve Strateji Üretimi)

- **🎯 E-Ticaret Senaryosu:**
  1. Yukarıdaki Strategy deseninde gelen siparişte `order.PaymentMethod == "Mollie"` yazıyor. Çalışma zamanında (runtime) doğru stratejiyi kim seçecek? **Factory (veya .NET 8/10 Keyed DI)**!
  2. İkinci kullanım: Bir sepetten (`Basket`) yeni bir sipariş (`Order`) oluştururken fiyat snapshot'ı almak, KDV hesaplamak ve kuponu satırlara dağıtmak karmaşık bir süreçtir. Bunu dağınık controller kodlarında yapmak yerine `Order.CreateFromBasket(...)` **Static Factory Method** içinde yaparız.
- **❓ Neden ve Amaç:** **Strategy** *"iş nasıl yapılacak?"* sorusunu çözerken, **Factory** *"hangi nesne/strateji nasıl yaratılacak?"* sorusunu çözer. Modern .NET'te (`IServiceProvider` / `IEnumerable<IStrategy>` veya `[FromKeyedServices]`) DI container zaten Factory görevi görür.
- **💻 C# (.NET) Örneği:**
  ```csharp
  public sealed class PaymentStrategyFactory(IEnumerable<IPaymentProviderStrategy> strategies)
  {
      public IPaymentProviderStrategy GetStrategy(string providerName) =>
          strategies.FirstOrDefault(s => s.ProviderName.Equals(providerName, StringComparison.OrdinalIgnoreCase))
          ?? throw new InvalidOperationException($"Payment provider '{providerName}' is not supported.");
  }
  ```
- **🗣️ Mülakatta İngilizce Anlatımı:**
  > *"Strategy and Factory work hand in hand. While the Strategy pattern defines how each payment or marketplace integration behaves, the **Factory pattern** (resolved via .NET Dependency Injection) is responsible for selecting and instantiating the right strategy at runtime based on the order's country or channel."*

---

### 1.3. Adapter Pattern & Anti-Corruption Layer - ACL (ERP, PIM ve Pazaryeri Entegrasyonu)

- **🎯 E-Ticaret Senaryosu (Wovar İçin En Kritik Pattern!):** Wovar'ın .NET entegrasyon katmanı ortada duruyor ve **NetSuite (ERP)**, **BigCommerce (Webshop)**, **Akeneo (PIM)** ve **Channable (Pazaryeri)** ile konuşuyor. NetSuite sipariş alanlarına `tranId`, `entity_internal_id`, `custbody_vat_code` gibi garip isimler veriyor. Channable ise bambaşka bir JSON gönderiyor.
- **❓ Neden ve Amaç:** Eğer NetSuite'in veya Channable'ın DTO sınıflarını kendi C# Domain katmanımızın içinde doğrudan kullanırsak, dış sistemdeki en ufak bir API değişikliği tüm iç kodumuzu kırar. **Adapter Pattern** (DDD'deki adıyla **Anti-Corruption Layer - ACL**), dış sistemin dilini bizim kendi temiz domain dilimize (`Canonical Model`) tercüme eden koruyucu bir çevirmen katmanıdır.
- **💻 C# (.NET) Örneği:**
  ```csharp
  // 1. Bizim temiz Domain Port'umuz (Application/Domain katmanında)
  public interface IErpOrderGateway
  {
      Task<string> ExportOrderAsync(Order order, CancellationToken ct);
  }

  // 2. NetSuite Adapter / ACL (Infrastructure katmanında)
  public sealed class NetSuiteOrderAdapter(HttpClient httpClient) : IErpOrderGateway
  {
      public async Task<string> ExportOrderAsync(Order order, CancellationToken ct)
      {
          // ACL: Kendi temiz 'Order' modelimizi NetSuite'in beklediği karmaşık DTO'ya çeviriyoruz
          var netSuitePayload = new NetSuiteSalesOrderDto
          {
              ExternalId = order.Id.ToString(),
              TranDate = order.CreatedAt.ToString("yyyy-MM-dd"),
              Lines = order.Lines.Select(l => new NetSuiteLineDto(l.Sku.Value, l.Quantity, l.UnitPrice.Amount)).ToList()
          };

          var response = await httpClient.PostAsJsonAsync("/services/rest/record/v1/salesOrder", netSuitePayload, ct);
          response.EnsureSuccessStatusCode();
          return await response.Content.ReadAsStringAsync(ct);
      }
  }
  ```
- **🗣️ Mülakatta İngilizce Anlatımı:**
  > *"In an integration-heavy platform connecting BigCommerce, NetSuite, Akeneo, and Channable, each third-party system has its own naming conventions and data structures. To keep our core .NET domain clean, we use the **Adapter pattern** as an **Anti-Corruption Layer (ACL)**. Our core application defines a clean interface like `IErpOrderGateway`, and the `NetSuiteOrderAdapter` translates our internal `Order` model into NetSuite's API format. That way, external API quirks never leak into our business logic."*

---

### 1.4. Decorator Pattern & MediatR Pipeline Behaviors (Caching, Logging, Validation)

- **🎯 E-Ticaret Senaryosu:**
  1. Ürün detaylarını veya kampanya kurallarını getiren `ICatalogService.GetProductBySkuAsync(sku)` metodumuz var. Bu metoda **Redis önbellekleme (Cache-Aside)** ve **süre ölçümü (Logging)** eklemek istiyoruz ama veritabanı kodunu kirletmek istemiyoruz.
  2. Veya her gelen `Command` çalışmadan önce **FluentValidation** ile doğrulanmasını, loglanmasını ve hata olursa yakalanmasını istiyoruz.
- **❓ Neden ve Amaç:** İş mantığı sınıfının içine `redis.Get()`, `logger.Log()`, `validator.Validate()` kodlarını doldurursak sınıf çorba olur (**Single Responsibility ihlali**). **Decorator Pattern** (veya MediatR `IPipelineBehavior`), aynı arayüzü uygulayarak asıl servisi bir soğan kabuğu gibi dışarıdan sarmalar.
- **💻 C# (.NET) Örneği:**
  ```csharp
  // Asıl servis sadece PostgreSQL'den çeker
  public sealed class PostgresCatalogService(AppDbContext db) : ICatalogService
  {
      public Task<ProductDto?> GetBySkuAsync(string sku, CancellationToken ct) =>
          db.Products.AsNoTracking().Where(p => p.Sku == sku).Select(p => p.ToDto()).FirstOrDefaultAsync(ct);
  }

  // Decorator: Asıl servisi değiştirmeden dışarıdan Redis Cache ekler!
  public sealed class CachedCatalogService(ICatalogService inner, IDistributedCache cache) : ICatalogService
  {
      public async Task<ProductDto?> GetBySkuAsync(string sku, CancellationToken ct)
      {
          var cached = await cache.GetStringAsync($"product:{sku}", ct);
          if (cached is not null) return JsonSerializer.Deserialize<ProductDto>(cached);

          var product = await inner.GetBySkuAsync(sku, ct); // İçteki asıl servisi çağırır
          if (product is not null)
              await cache.SetStringAsync($"product:{sku}", JsonSerializer.Serialize(product), ct);

          return product;
      }
  }
  ```
- **🗣️ Mülakatta İngilizce Anlatımı:**
  > *"To keep our business handlers clean of cross-cutting concerns like caching, logging, and validation, we use the **Decorator pattern** and **MediatR Pipeline Behaviors**. For example, a `CachedCatalogService` wraps the database service using the same interface. It checks Redis first and only calls the inner database service on a cache miss, keeping both classes small and focused on a single responsibility."*

---

### 1.5. State Pattern / Order State Machine (Sipariş Durum Geçişleri)

- **🎯 E-Ticaret Senaryosu:** Bir sipariş sırasıyla şu durumlardan geçer: `PendingPayment` → `Paid` → `SentToErp` → `Picking` → `Shipped` → `Delivered` (veya `Cancelled` / `Returned`).
- **❓ Neden ve Amaç:** Dağıtık bir e-ticaret sisteminde webhook'lar ve kullanıcı istekleri aynı anda gelebilir. Örneğin sipariş depoda `Shipped` (kargolandı) durumuna geçtikten sonra `Cancel()` çağrılırsa veya henüz `PendingPayment` (ödenmemiş) bir sipariş yanlışlıkla ERP'ye gönderilmeye çalışılırsa büyük finansal zarar oluşur. Durum geçiş kuralları dışarıda değil, doğrudan `Order` nesnesinin içinde korunmalıdır.
- **🗣️ Mülakatta İngilizce Anlatımı:**
  > *"An e-commerce order has a strict lifecycle. Instead of letting external services set `order.Status = Shipped` directly via public setters, we model the order lifecycle using the **State Machine pattern** inside the `Order` domain entity. For example, calling `order.Cancel()` when the state is already `Picking` or `Shipped` throws a domain error and forces a Return (RMA) flow instead."*

---

### 1.6. Specification & Chain of Responsibility (Kampanya ve Sipariş Kuralları)

- **🎯 E-Ticaret Senaryosu:** Sepete bir kupon veya kampanya uygulanırken 5 farklı kuralın kontrol edilmesi gerekiyor: *(1) Kupon süresi geçerli mi? (2) Sepet tutarı €100 üzerinde mi? (3) Sepetteki ürünler indirimli kategoride mi? (4) Müşteri bu kuponu daha önce kullandı mı?*
- **❓ Neden ve Amaç:** Bu kuralları tek bir metodun içine iç içe `if` bloklarıyla yazmak yerine her kuralı küçük bir **Specification** (veya **Chain of Responsibility** halkası) olarak yazarız. Böylece kurallar hem tek tek unit test edilir hem de farklı kampanyalarda yeniden kullanılır.
- **🗣️ Mülakatta İngilizce Anlatımı:**
  > *"For complex promotion, pricing, or order allocation rules, we use the **Specification** or **Chain of Responsibility** pattern. Each business rule—such as minimum basket amount, country eligibility, or stock availability—is encapsulated in its own small class. This makes complex e-commerce rules easy to compose and unit test."*

---

## 2. Katman B: DDD (Domain-Driven Design) ve Uygulama Mimarisi Desenleri

### 2.1. Aggregate Root & Value Object Pattern (Sipariş Bütünlüğü ve Para/SKU)

- **🎯 E-Ticaret Senaryosu:**
  - **Value Object:** Fiyat ve para birimini `decimal Price` olarak tutarsak, bir geliştirici yanlışlıkla `EUR` olan bir tutarla `GBP` olan bir tutarı toplayabilir veya fiyata `-50` atayabilir. Bunun yerine `Money(decimal Amount, string Currency)` **Value Object** (`readonly record`) kullanırız.
  - **Aggregate Root:** Bir siparişte `Order` (kök) ve `OrderLine` (satırlar) vardır. Eğer başka bir kod doğrudan `dbContext.OrderLines.Add(...)` yaparsa, siparişin toplam tutarı (`Order.TotalAmount`) ve maksimum ürün limiti kontrolü bypass edilmiş olur!
- **❓ Neden ve Amaç:** **Aggregate Root**, işlem (transaction) ve tutarlılık sınırıdır. Dışarıdan hiçbir kod alt nesnelere (`OrderLine`) doğrudan dokunamaz; her değişiklik `order.AddLine(...)` üzerinden geçmek zorundadır.
- **💻 C# (.NET) Örneği:**
  ```csharp
  public sealed record Money(decimal Amount, string Currency)
  {
      public static Money Create(decimal amount, string currency) =>
          amount < 0 ? throw new DomainException("Amount cannot be negative.") : new(decimal.Round(amount, 2), currency);

      public Money Add(Money other) =>
          Currency != other.Currency ? throw new DomainException("Currency mismatch.") : new(Amount + other.Amount, Currency);
  }

  public sealed class Order : AggregateRoot<Guid>
  {
      private readonly List<OrderLine> _lines = [];
      public IReadOnlyCollection<OrderLine> Lines => _lines.AsReadOnly(); // Dışarıdan .Add() yapılamaz!
      public OrderStatus Status { get; private set; }

      public void AddLine(string sku, int qty, Money unitPrice)
      {
          if (Status != OrderStatus.Draft) throw new DomainException("Cannot modify a placed order.");
          if (qty <= 0) throw new DomainException("Quantity must be positive.");
          _lines.Add(new OrderLine(sku, qty, unitPrice));
      }
  }
  ```
- **🗣️ Mülakatta İngilizce Anlatımı:**
  > *"In DDD, we use **Value Objects** (like `Money`, `Sku`, and `Address`) as immutable C# records that validate themselves on creation—so you can never have a negative price or add Euros to Dollars accidentally. And we use the **Aggregate Root** pattern for `Order`: the `_lines` list is private and exposed only as `IReadOnlyCollection`, so any change must go through methods on `Order`, guaranteeing that our business invariants are never bypassed."*

---

### 2.2. Domain Events vs. Integration Events (Servis İçi ve Servisler Arası Olaylar)

- **🎯 E-Ticaret Senaryosu:** Müşteri ödemeyi tamamladığında (`Order.MarkAsPaid()`):
  1. Kendi veritabanımızdaki stok rezervasyon durumu `Confirmed` yapılmalı ve sipariş tarihçesine kayıt atılmalı (**Domain Event** — aynı servis/sınır içinde).
  2. NetSuite ERP'ye sipariş gönderilmeli, Channable pazaryeri stoğu güncellenmeli ve Klaviyo'dan müşteriye "Siparişiniz alındı" e-postası gitmeli (**Integration Event** — dış sistemlere/kuyruğa).
- **❓ Neden ve Amaç:** `Order.MarkAsPaid()` metodunun içinden doğrudan NetSuite, Channable ve Klaviyo HTTP servislerini çağırırsak, Klaviyo yavaşladığında ödeme işlemi de yavaşlar veya çöker. Olay fırlatarak sorumlulukları birbirinden koparırız (**Decoupling**).
- **🗣️ Mülakatta İngilizce Anlatımı:**
  > *"We clearly separate **Domain Events** from **Integration Events**. When an order is paid, the `Order` entity raises an `OrderPaidDomainEvent` in memory to handle local side effects within the same bounded context. Then, an event handler converts that into a versioned `OrderPaidIntegrationEvent`, writes it to the Outbox table, and publishes it to the message broker so ERP sync, Channable feeds, and Klaviyo emails happen asynchronously without slowing down checkout."*

---

### 2.3. CQRS & Mediator Pattern (Okuma ve Yazma Sorumluluğunu Ayırma)

- **🎯 E-Ticaret Senaryosu:**
  - **Write (Command):** Sipariş oluşturma, iade başlatma, stok rezerve etme. Burada sıkı iş kuralları, validasyon, iyimser kilitleme (`xmin` / `RowVersion`) ve Outbox kaydı gerekir (**EF Core + DDD Aggregate**).
  - **Read (Query):** Ürün sayfası, sipariş geçmişi ekranı, admin paneli raporları. Burada iş kuralı yoktur, sadece veriyi en hızlı şekilde ekrana basmak gerekir (**Dapper veya EF Core `AsNoTracking` + DTO Projection**).
- **❓ Neden ve Amaç:** Tek bir model ve tek bir `OrderService` sınıfıyla hem karmaşık yazma kurallarını hem de hızlı raporlama sorgularını yapmaya çalışmak 5.000 satırlık "God Class" yaratır. **CQRS** okuma ve yazma modellerini ayırır; **Mediator (`MediatR`)** ise her isteği kendi bağımsız `CommandHandler` veya `QueryHandler` sınıfına yönlendirir (**Vertical Slicing**).
- **🗣️ Mülakatta İngilizce Anlatımı:**
  > *"In e-commerce, read traffic is much higher than write traffic, and their requirements are very different. Using **CQRS**, our **Command handlers** use EF Core and DDD aggregates to enforce business rules and write to the Outbox in a single transaction. Meanwhile, our **Query handlers** bypass the domain model completely and use Dapper or EF Core `.AsNoTracking()` projections to return fast, lightweight DTOs."*

---

### 2.4. Repository & Unit of Work Pattern (Atomik Kayıt)

- **🎯 E-Ticaret Senaryosu:** Sipariş oluşturulurken `orders` tablosuna kayıt atılıyor, `coupons` tablosunda kullanım sayısı artırılıyor ve `outbox_messages` tablosuna event ekleniyor. Bu 3 işlemin **ya hep birlikte başarılı olması ya da hata durumunda üçünün birden geri alınması (Rollback)** gerekir.
- **❓ Neden ve Amaç:** **Repository**, domain katmanını veritabanı detaylarından soyutlar (Aggregate Root başına bir Repository yazılır). **Unit of Work** (EF Core'da `DbContext` + `SaveChangesAsync`) ise bellekte yapılan tüm değişiklikleri ve Outbox mesajlarını **tek bir veritabanı transaction'ı** içinde atomik olarak commit eder.

---

## 3. Katman C: Dağıtık Sistem, API ve Entegrasyon Desenleri (Wovar'ın Kalbi)

### 3.1. Transactional Outbox & Inbox (Idempotent Consumer) Pattern

- **🎯 E-Ticaret Senaryosu:** Sipariş PostgreSQL'e kaydedildi ve şimdi NetSuite ERP ile Klaviyo'ya haber vermek için Kafka / Azure Service Bus'a mesaj atılacak.
- **❓ Neden ve Amaç:**
  - **Outbox (Producer tarafı):** DB'ye `COMMIT` attıktan hemen sonra kuyruğa mesaj atarken ağ koparsa sipariş DB'de kalır ama ERP'ye asla gitmez (**Dual-Write Problemi**). Çözüm: İş verisini ve `outbox_messages` kaydını **aynı PostgreSQL transaction'ı** içinde yazmak, ardından Debezium CDC veya Polling Worker ile kuyruğa aktarmak.
  - **Inbox (Consumer tarafı):** Kuyruklar *at-least-once* çalıştığı için aynı mesaj iki kez gelebilir. Consumer kendi veritabanındaki `processed_events` (Inbox) tablosuna `event_id` değerini iş güncellemesiyle **aynı transaction içinde** yazar; mükerrer mesaj gelirse `ON CONFLICT DO NOTHING` ile atlar.
- **🗣️ Mülakatta İngilizce Anlatımı:**
  > *"Whenever we need to update PostgreSQL and publish an event to a message broker, we use the **Transactional Outbox Pattern** on the producer side and the **Inbox (Idempotent Consumer) Pattern** on the consumer side. This eliminates the dual-write problem and guarantees that even if a message is delivered twice by the broker, the consumer only processes its side effects once."*

---

### 3.2. Saga Pattern & Compensating Actions (Dağıtık İşlem Geri Alma)

- **🎯 E-Ticaret Senaryosu:** Çok adımlı bir sipariş akışı düşün:
  1. `InventoryService` → Stoğu rezerve etti.
  2. `PaymentService` → Müşterinin kredi kartından parayı çekmeye çalıştı ama kart bakiyesi yetersiz (`PaymentFailed`)!
  3. Şimdi 1. adımda ayrılan stoğun tekrar serbest bırakılması ve siparişin `PaymentFailed` durumuna çekilmesi gerekir.
- **❓ Neden ve Amaç:** Farklı mikroservisler veya dış API'ler (PSP, ERP) arasında tek bir veritabanı transaction'ı (`BEGIN ... ROLLBACK`) açamayız. **Saga Pattern**, çok adımlı dağıtık süreçleri yönetir: herhangi bir adımda hata olursa, daha önce başarılı olmuş adımları geri almak için **Compensating Actions (Telafi Edici İşlemler — örn. `ReleaseStockCommand` veya `RefundPaymentCommand`)** çalıştırır.
- **Orkestrasyon vs. Koreografi:**
  - **Choreography (Olay bazlı):** 2-3 adımlık basit süreçlerde servisler birbirinin event'ini dinler.
  - **Orchestration (Merkezi yönetici):** 4+ adımlı karmaşık sipariş/ödeme/ERP süreçlerinde merkezi bir durum makinesi (Orchestrator) adımları sırayla yönetir; takip etmesi ve hata ayıklaması çok daha kolaydır.
- **🗣️ Mülakatta İngilizce Anlatımı:**
  > *"Since you cannot span a traditional database transaction across external APIs like BigCommerce, a Payment Provider, and NetSuite ERP, we use the **Saga Pattern with Compensating Actions**. For example, if stock is reserved in step one but payment capture fails in step two, the Saga triggers a compensating action to release the stock reservation automatically so inventory is never stuck."*

---

### 3.3. Resilience Patterns: Retry, Circuit Breaker & Bulkhead

- **🎯 E-Ticaret Senaryosu:** Wovar'ın .NET entegrasyon katmanı NetSuite ERP'ye sipariş gönderiyor. NetSuite anlık ağ dalgalanması yaşadı (`503`) veya bakım nedeniyle 20 dakikalığına tamamen kapandı.
- **❓ Neden ve Amaç:**
  1. **Retry with Exponential Backoff + Jitter:** 1-2 saniyelik geçici ağ kopmalarında isteği hemen çöpe atmak yerine 2sn, 4sn, 8sn (+ rastgele milisaniye) bekleyerek tekrar deneriz (tabii ki **Idempotency Key** veya **Read-Before-Retry** ile!).
  2. **Circuit Breaker (Devre Kesici):** Eğer NetSuite tamamen çöktüyse ve üst üste 10 istek hata verdiyse, devreyi 1 dakikalığına "Açık (Open)" konuma getirip istek atmayı keseriz. Mesajlar kuyrukta güvenle bekler; ne kendi sunucumuzu ne de yeniden ayağa kalkmaya çalışan ERP'yi boğmayız.
  3. **Bulkhead Isolation (Bölme İzolasyonu):** Gemilerdeki su geçirmez bölmeler gibidir. Klaviyo (pazarlama) entegrasyonu için ayrı bir `HttpClient` / kuyruk havuzu, NetSuite (ERP) için ayrı bir havuz kullanırız. Böylece Klaviyo API'si kilitlenirse, hayati öneme sahip ERP sipariş senkronizasyonunun thread/bağlantı havuzunu tüketemez!
- **🗣️ Mülakatta İngilizce Anlatımı:**
  > *"When integrating multiple external systems, failures are normal. We combine three resilience patterns using Polly / `.NET Resilience`: **Exponential Backoff with Jitter** for transient network blips, a **Circuit Breaker** to stop flooding an external API when it is completely down, and **Bulkhead Isolation** (separate queues and HTTP pools per integration) so a slow marketing API like Klaviyo can never starve critical ERP or payment flows."*

---

### 3.4. Strangler Fig Pattern (Ajans/Legacy Kodunu Kademeli Devralma)

- **🎯 E-Ticaret Senaryosu (Wovar'ın 2026 Hedefi!):** Wovar şu an dış ajansın geliştirdiği e-ticaret ve entegrasyon kodlarını kullanıyor ve 2026 sonuna kadar her şeyi in-house (iç) ekibe devralıp iyileştirmek istiyor.
- **❓ Neden ve Amaç:** Çalışan bir sistemi çöpe atıp 6 ay boyunca sıfırdan yazmaya kalkmak (**Big-Bang Rewrite**) en tehlikeli mühendislik hatasıdır; iş kuralları unutulur ve canlıya geçiş günü kriz çıkar. **Strangler Fig (Boğucu İncir)** deseninde, önde bir API Gateway / Router (veya mesaj kuyruğu yönlendiricisi) durur. Dış ajansın kodundaki modülleri (örneğin önce Stok Senkronizasyonu, sonra Sipariş Aktarımı, sonra PIM Entegrasyonu) **tek tek** yeni, test edilmiş .NET servislerimize alır ve trafiği adım adım yeni servise yönlendiririz.
- **🗣️ Mülakatta İngilizce Anlatımı:**
  > *"When transitioning a live e-commerce platform from an external agency or legacy codebase to an in-house team, I always favor the **Strangler Fig pattern** over a risky big-bang rewrite. We first put automated integration tests and observability around the existing flows, and then replace or refactor one integration flow at a time behind a clean interface or feature flag. This lets us deliver continuous improvements while keeping daily sales 100% safe."*

---

## 4. Mülakatta Altın Değerinde 3 Prensip (Over-Engineering Tuzağından Kaçınma)

Wovar'ın Engineering Lead'i veya CTO'su senin bütün bu desenleri bildiğini gördüğünde, **"Acaba her basit işe 10 katmanlı karmaşık pattern mi kuruyor?"** diye test etmek isteyebilir. Şu 3 cümleyi kurduğunda mülakatı kesin olarak kazanırsın:

1. **Pragmatism & YAGNI (You Aren't Gonna Need It):**
   > *"I never apply design patterns just to make the code look clever. Every pattern adds complexity, so I only introduce an abstraction or pattern when there is a real business complexity, multiple implementations, or a clear reliability requirement."*
   *(Kod zekice görünsün diye asla tasarım deseni uygulamam. Her desen karmaşıklık ekler; bu yüzden bir soyutlamayı veya deseni yalnızca gerçek bir iş karmaşıklığı, birden fazla implementasyon veya net bir güvenilirlik ihtiyacı olduğunda kullanırım.)*

2. **DDD'yi Sadece Karmaşık İş Mantığında Kullanmak:**
   > *"For core flows with rich business rules—like Order Processing and Stock Reservation—I use full DDD Aggregates and Clean Architecture. But for simple CRUD or data-forwarding endpoints, I keep it simple with thin vertical slices."*
   *(Sipariş İşleme ve Stok Rezervasyonu gibi zengin iş kuralları olan çekirdek akışlarda tam DDD Aggregate'leri ve Clean Architecture kullanırım. Ancak basit CRUD veya veri iletme uç noktalarında ince dikey dilimlerle (vertical slices) işi basit tutarım.)*

3. **Desenlerin Amacı Test Edilebilirlik ve Değişim Kolaylığıdır:**
   > *"For me, the ultimate test of good architecture and design patterns is how easily we can write unit and integration tests for it, and how safely we can change one external system without breaking the rest of the platform."*
   *(Benim için iyi bir mimarinin ve tasarım desenlerinin nihai testi, onun için ne kadar kolay unit ve entegrasyon testi yazabildiğimiz ve platformun geri kalanını kırmadan bir dış sistemi ne kadar güvenle değiştirebildiğimizdir.)*

