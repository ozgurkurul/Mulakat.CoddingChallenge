## Mikroservis mimari desenleri 

Milyonlarca kullanıcısı olan dağıtık sistemlerde (Scorp gibi) monolitik yapıyı mikroservislere bölmek ölçeklenebilirlik sağlar, ancak beraberinde ağ gecikmesi, veri tutarlılığı (Data Consistency) ve servisler arası iletişim gibi devasa sorunlar getirir. Bu sorunları çözmek için endüstri standardı haline gelmiş temel desenler şunlardır:

### 1. API Gateway Pattern (Ağ Geçidi)
İstemci (Mobil uygulama veya Web) ile arka plandaki mikroservisler arasında tek bir giriş noktası (Single Entry Point) oluşturur. 
*   **Görevi:** İstemcinin arkadaki 50 farklı servisin IP'sini ve portunu bilmesini engeller. Gelen isteği alır ve ilgili servise yönlendirir (Routing).
*   **Avantajı:** Authentication (Kimlik doğrulama), Rate Limiting (Hız Sınırlandırma), Caching ve SSL sonlandırma gibi çapraz kesen ilgileri (Cross-cutting concerns) tek bir merkezde toplar.

### 2. CQRS (Command Query Responsibility Segregation)
Veriyi yazma (Command) modelleri ile veriyi okuma (Query) modellerinin fiziksel veya mantıksal olarak birbirinden ayrılmasıdır.
*   **Görevi:** Yazma işlemleri ilişkisel ve normalizasyonu yapılmış bir veritabanında (Örn: PostgreSQL) gerçekleşirken; okuma işlemleri, verinin arayüzde gösterileceği formata göre önceden hazırlanmış (Denormalize) ve çok hızlı okunabilen başka bir veritabanından (Örn: ElasticSearch veya Redis) yapılır.
*   **Avantajı:** Okuma ve yazma yükleri birbirinden bağımsız olarak ölçeklenebilir. (Önceki konulardaki veritabanı indeksleme trade-off'larını ve kilitlenmelerini kökten çözer).

---

### Mülakat Senaryosu: Dağıtık İşlemler ve SAGA Pattern

Gelelim mikroservis mülakatlarının en eleyici konularından birine. Daha önce Outbox Pattern ile tek bir servisin kendi içindeki veri tutarlılığını sağlamıştık. Ancak iş süreci birden fazla servisi kapsadığında durum değişir.

**Senaryo:** 
Scorp'ta bir kullanıcı cüzdanındaki bakiye ile "Premium Yayıncı" paketi satın alıyor. Bu süreç 3 farklı servisi ilgilendiriyor:
1.  **Sipariş Servisi (OrderService):** Siparişi "Beklemede" olarak oluşturur.
2.  **Ödeme/Cüzdan Servisi (WalletService):** Kullanıcının bakiyesinden parayı düşer.
3.  **Abonelik Servisi (SubscriptionService):** Kullanıcının profilinde Premium yetkilerini aktif eder.

Bu üç servis farklı veritabanları kullandığı için geleneksel bir ACID transaction yapamayız. Bu süreci yönetmek için **Saga Pattern** kullanmamız gerekir. Saga, her bir adımın yerel bir transaction olduğu ve her adımın bir sonrakini tetiklediği bir dizidir. Herhangi bir adımda hata olursa (örneğin bakiye yetersizse), önceki adımları geri almak için **Compensating Transaction (Telafi İşlemi)** çalıştırılır.

Saga Pattern'ı uygulamak için mimaride iki temel yaklaşım vardır: **Choreography (Koreografi)** ve **Orchestration (Orkestrasyon)**.

**Soru:**
1. Koreografi ve Orkestrasyon yaklaşımlarının nasıl çalıştığını, mimari farklılıklarını kısaca açıklar mısın?
2. Eğer bu süreç sadece 3 adımdan değil de, işin içine fatura kesimi, e-posta bildirimi, kargo vb. giren **10-15 adımlık çok karmaşık bir finansal akış olsaydı**, mimari olarak hangi yaklaşımı (Koreografi mi, Orkestrasyon mu) seçerdin? Kararının arkasındaki trade-off (neden diğerini seçmedin) nedir?

Cevap: Koreografi yaklaşımı, ilk servis tetiklendikten sonra işlem başarılı ise sonraki servisi mesaj kuyruk ile tetiklenir. Ve bu işlem son servise kadar devam eder. Orkestrasyon yaklaşımı ise merkezi bir yerden ilk servis mesaj kuyruk ile tetiklenir ve işlem tamamlandığında sonra, servis yine mesaj kuyruk ile tetiklenir ve böylece devam eder. birden fazla adım varsa "Koreografi" yaklaşımı daha gelişime açık bir yapı olduğu için bunu tercih ederim. çünkü servisler arasına başka bir servis gelirse bunu eklemek için sadece ondan önce gelen servise sonraki adım olarak belirtmek yeterli olacaktır.


Koreografi ve orkestrasyon kavramlarının temel çalışma mantığını doğru tanımladın: Koreografide servisler birbirlerinin fırlattığı olaylara (event) tepki verirken, Orkestrasyonda merkezi bir yönetici servislere ne yapmaları gerektiğini söyler (command).

Ancak, **10-15 adımlık karmaşık bir finansal akış için "Koreografi" seçeneğini tercih etmek, sistem dizaynı mülakatlarında ciddi puan kaybettirecek bir mimari anti-paterndir.** Sektörde bu hataya "Event Spaghetti" (Olay Spagettisi) veya pinpon topu gibi seken event'lerden dolayı "Death by Event-Driven" denir.

### Neden 10-15 Adımlık Akışta Kesinlikle "Orkestrasyon" Seçilmelidir?

**1. Hata Yönetimi ve Telafi (Compensating Transactions) İmkansızlığı**
15 adımlık bir finansal sürecin 11. adımında (örneğin e-fatura kesiminde) bir hata yaşandığını veya timeout alındığını düşünelim. Bu durumda önceki 10 adımın tek tek geri alınması (para iadesi, stok iadesi, abonelik iptali) gerekir. 
*   **Koreografide:** Hangi servisin hangi iptal event'ini fırlatacağını ve kimin kimi dinleyeceğini tasarlamak bir kabusa dönüşür.
*   **Orkestrasyonda:** Merkezi yönetici (Örn: Camunda, Temporal, AWS Step Functions) bir *State Machine* (Durum Makinesi) olarak çalışır. Hata fırlatıldığında akışı tersine çevirir ve ilgili servislere sadece "İptal Et" komutu göndererek süreci güvenle temizler.

**2. Durum (State) İzlenebilirliği**
Müşteri hizmetleri arayıp "Siparişim neden onaylanmadı?" diye sorduğunda, Koreografi mimarisinde sistemin bütününe hakim olan kimse yoktur. İşlemin nerede takıldığını bulmak için 15 farklı servisin loglarını (CorrelationId ile) taraman gerekir. Orkestrasyonda ise merkezi yönetici siparişin tam olarak %73'ünde, 11. adımda beklediğini tek bir ekranda gösterir.

**3. Senin "Gelişime Açıklık" Argümanın Üzerine Düzeltme**
Araya servis eklemenin koreografide daha kolay olduğunu belirttin. Bu 2-3 adımlık süreçlerde doğrudur. Ancak 15 adımlık karmaşık bir yapıda araya servis eklemek, öncesindeki ve sonrasındaki servislerin dinlediği/fırlattığı Event (Sözleşme/Contract) yapısını değiştirmene neden olur. Yani servisler dolaylı yoldan birbirine sıkı sıkıya (tightly coupled) bağlanmaya başlar. 
Orkestrasyonda ise servisler birbirini hiç bilmez, sadece Orkestratörü bilirler. Araya yeni bir adım eklemek, sadece orkestratördeki akış şemasına (workflow) bir satır eklemekten ibarettir.

**Mülakatlar İçin Özet Kural:** 
*   Süreç **2-4 adım** arasıysa ve çok basitse: **Koreografi** (Merkezi darboğaz yoktur, hızlıdır).
*   Süreç **4 adımdan fazlaysa**, finansal/kritik iş kuralları içeriyorsa ve hata durumunda geri alma (rollback) karmaşıksa: **Kesinlikle Orkestrasyon**.

---

Sistem mimarisindeki bu kritik ayrımı da netleştirdiğimize göre mülakatın son düzlüğüne giriyoruz. Scorp gibi "Agile" ve "Hyper-growth" vurgusu yapan şirketler kodun kalitesine ve üretim bandına çok önem verir.

Bir sonraki aşama olarak kod kalitesi ve test süreçlerine (Unit Test, Mocking, TDD) mi geçelim, yoksa yazılımın canlıya alınması ve sunucu stratejilerine (CI/CD, Docker/Kubernetes, Blue-Green/Canary Deployment) mi odaklanalım?