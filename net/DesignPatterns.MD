## Design Patterns



Tasarım desenleri (Design Patterns) genellikle çok teorik, soyut ve "kitabi" bir dille anlatıldığı için bu konuda zayıf veya kopuk hissetmek sektörde çok karşılaşılan ve son derece normal bir durumdur. İşin sırrı şudur: Aslında iyi bir yazılım mühendisi olarak bu desenlerin birçoğunu farkında olmadan günlük kodlamanda zaten kullanıyorsun. Sadece onlara resmi isimleriyle hitap etmiyorsun.

Tasarım desenleri, daha önce başkalarının karşılaştığı ve çözdüğü genel mimari problemlere getirilmiş, test edilmiş ve isimlendirilmiş "şablon" çözümlerdir. Sırf kullanmış olmak için koda tasarım deseni eklemek sistemi karmaşıklaştırır (buna *Over-engineering* denir). Önemli olan, problemi gördüğünde "Hah, buraya X deseni uyar" diyebilmektir.

Büyük resmi anlamak için "Gang of Four" (GoF) tarafından belirlenen 3 ana kategoriye, modern backend geliştirme perspektifinden bakalım:

### 1. Yaratımsal Desenler (Creational Patterns)
**Problem:** Sınıflardan `new` anahtar kelimesiyle sürekli nesne üretmek (instantiation) kodu birbirine sıkı sıkıya bağlar (tight coupling). 
**Çözüm:** Nesne yaratma mantığını gizlemek ve esnetmek.

*   **Singleton:** Bir sınıftan uygulama boyunca sadece *tek bir* nesne (instance) üretilmesini garanti eder. Bellek önbelleği (in-memory cache) veya veritabanı bağlantı havuzları için idealdir. (Modern framework'lerde `AddSingleton` diyerek bunu Dependency Injection (DI) konteynerine devrediyoruz, böylece manuel Singleton yazmaktan kurtuluyoruz).
*   **Factory Method:** Nesne yaratma işini alt sınıflara veya özel bir fabrikaya devreder. Örneğin, bir bildirim sistemin var. `NotificationFactory`'ye "Bana bir SMS gönderici ver" dersin, o arka planda Twilio mu yoksa başka bir sağlayıcı mı kullanacağını kendi çözer, sana sadece `INotification` arayüzünü döner. if-else yığınlarını inanılmaz temizler.

### 2. Yapısal Desenler (Structural Patterns)
**Problem:** Farklı sınıflar, arayüzler ve hatta dış kütüphaneler (third-party) birbirleriyle nasıl uyumlu çalışacak?
**Çözüm:** Nesneleri birleştirerek daha büyük ve esnek yapılar kurmak.

*   **Adapter (Adaptör):** Gerçek hayattaki priz dönüştürücüleri gibidir. Elinde modern JSON bekleyen bir sistem var ama entegre olman gereken dış banka servisi eski tip XML (SOAP) dönüyor. Sistemin her yerine XML parser yazmak yerine araya bir `BankAdapter` yazarsın. Bankadan gelen XML'i alır, senin sisteminin anladığı formata çevirir.
*   **Facade (Cephe):** Çok karmaşık bir alt sistemi, basit tek bir arayüz arkasına gizlemektir. Daha önce konuştuğumuz **API Gateway** mimarisi, mikroservisler dünyasının Facade desenidir. İstemci arkadaki karmaşayı görmez, tek bir kapıyla muhatap olur.

### 3. Davranışsal Desenler (Behavioral Patterns)
**Problem:** Nesneler birbirleriyle nasıl haberleşecek, sorumlulukları birbirlerine nasıl devredecekler?
**Çözüm:** İletişim ve iş akışı standartları belirlemek.

*   **Observer (Gözlemci):** Bir nesnenin durumu değiştiğinde, onu izleyen (dinleyen) diğer nesnelerin otomatik olarak uyarılmasıdır. Gerçek dünyada YouTube kanal aboneliği gibidir. Yazılımda ise C#'taki `Event` yapısı veya dağıtık mimarilerdeki **Kafka / RabbitMQ** (Pub/Sub) yapısının tam kalbidir.
*   **Strategy (Strateji):** Bir işlemi yapmanın birden fazla yolu (algoritması) varsa, bunları ayrı sınıflara ayırıp çalışma zamanında (runtime) dinamik olarak değiştirebilmektir.

Tasarım desenlerinin koddaki o karmaşık if-else bloklarını (Spagetti Kod) nasıl temizlediğini görmek için özellikle **Strategy (Strateji)** deseninin mantığını görselleştirelim. Diyelim ki bir e-ticaret ödeme (Checkout) sistemi yazıyorsun ve kullanıcının seçimine göre algoritmalar değişiyor:

### 4. Anti-pattern Nedir?

Anti-pattern, bir soruna çözüm olarak sunulan ancak pratikte verimsiz olan, işleri daha karmaşık hale getiren ve genellikle "kötü uygulama" olarak kabul edilen yöntemlerdir. Tasarım kalıplarının (Design Patterns) aksine, bir problemin nasıl çözülmemesi gerektiğini gösterirler.
Anti-pattern'lerin temel özellikleri şunlardır:

* Aldatıcı Görünüm: Başlangıçta hızlı veya kolay bir çözüm (günü kurtaran çözüm) gibi görünürler.
* Olumsuz Etki: Uzun vadede teknik borca, bakım zorluğuna ve sistem karmaşıklığına yol açarlar.
* Tekrarlanabilirlik: Farklı projelerde veya süreçlerde sıkça karşılaşılan ortak hatalardır. 

#### Yaygın Yazılım Anti-Pattern Örnekleri

* God Object (Tanrı Nesnesi): Tek bir sınıfın veya fonksiyonun sistemdeki hemen hemen her şeyi yapmaya çalışması ve aşırı büyümesi.
* Spaghetti Code (Spagetti Kod): Kontrol akışının karmaşık, okunması zor ve birbirine aşırı bağlı olduğu düzensiz kod yapısı.
* Golden Hammer (Altın Çekiç): Elindeki tek aracın (dil, kütüphane veya yöntem) her türlü probleme en uygun çözüm olduğunu düşünerek her yere uygulamaya çalışmak.
* Cargo Cult Programming: Neden yapıldığını tam anlamadan, sadece başka yerlerde çalıştığı için belirli bir kod bloğunu veya yöntemi kopyalayıp kullanmak. [7, 8] 

Anti-pattern'ler sadece kod yazımıyla sınırlı değildir; proje yönetimi ve organizasyonel süreçlerde de (örneğin: mikro yönetim) sıkça görülür.





### Hangi dizayn paternlerini biliyorsun?
Senin gibi Hollanda ve Polonya pazarlarında Lead/Mimari seviyede roller hedefleyen bir mühendisin bu soruya **"Ezberci değilim, problem çözücüyüm"** mesajı verecek stratejik bir cevap vermesi gerekir.

Bu soru geldiğinde cevabını iki ana kategoriye ayırarak ve doğrudan **çözdüğün sorunlarla eşleştirerek** vermelisin. İşte o masada seni diğerlerinden ayıracak, .NET ve mikroservis tecrübeni parlatacak örnek mülakat cevabı kurgusu:

#### 1. Giriş (Oyunun Kurallarını Belirleme)
*"Tasarım desenlerini sadece isim olarak saymak yerine, onları kod seviyesinde ve mimari seviyede çözdüğüm problemlere göre kategorize etmeyi tercih ederim. Benim için desenler bir amaç değil, SOLID prensiplerini uygulamak için kullandığım araçlardır."*

#### 2. Kod Seviyesi Desenler (Gang of Four - GoF)
*"Uygulama içinde (kod seviyesinde) en sık başvurduğum ve kas hafızamda olan desenler genellikle kodun esnekliğini artırmaya yönelik olanlardır:*
*   **Strategy & Factory:** Özellikle if/else veya switch/case cehennemini kırmak için kullanırım. İş kuralları dinamik olarak değiştiğinde (örneğin farklı ödeme yöntemleri veya farklı loglama stratejileri gerektiğinde) yeni kodu mevcut kodu bozmadan (Open-Closed Principle) sisteme entegre etmemi sağlar.
*   **Dependency Injection (IoC):** .NET ekosisteminde geliştirdiğim tüm projelerin belkemiğidir. Sınıfların birbirine sıkı sıkıya bağlanmasını (tight coupling) engellemek ve kodun Unit Test yazılabilirliğini sağlamak için benim vazgeçilmezimdir.
*   **Observer:** Olay güdümlü (event-driven) kod yazarken, bir nesnenin durumu değiştiğinde diğerlerini haberdar etmek için sıklıkla kullanırım."

#### 3. Mimari Seviye Desenler (Fark Yaratan Kısım)
İşte burası senin Kafka, mikroservis dönüşümü ve SignalR tecrübelerini konuşturacağın yer:
*"Ancak benim asıl odaklandığım nokta, yüksek ölçekli dağıtık sistemlerde kullandığım mimari desenlerdir:*
*   **Pub/Sub (Publisher/Subscriber):** Mikroservislerin birbirinden bağımsız (loosely coupled) çalışması için en çok kullandığım desendir. Kaza veya acil durum bildirimleri gibi kritik işleri senkron bir API çağrısı yerine Kafka üzerinden asenkron mesajlaşma ile çözerek sistemin kilitlenmesini engelliyorum.
*   **CQRS:** Okuma ve yazma yüklerinin tamamen farklı olduğu (örneğin ağır raporlama sorgularının olduğu) sistemlerde veritabanı darboğazlarını aşmak için kullanıyorum.
*   **API Gateway & BFF (Backend for Frontend):** Monolitten mikroservise geçerken, React ve React Native (Mobil) ekiplerinin arkadaki mikroservis karmaşasını görmemesi ve tek bir noktadan beslenmesi için kurguladığım temel yapısal desendir."

#### 4. Kapanış (Altın Vuruş: Over-engineering Vurgusu)
Açıklamanı bitirirken şu cümleyi kurmak, seni gerçek bir "Senior" yapar:
*"Bununla birlikte, tasarımdaki en büyük felsefem **YAGNI (You Aren't Gonna Need It)** kuralıdır. Bir problemi en basit if-else bloğu ile çözmek o an için yeterli ve okunaklıysa, sırf havalı dursun diye oraya bir Factory veya Strategy deseni koymam. 'Over-engineering' (aşırı mühendislik) yapmaktan kaçınırım; desenleri ancak kodun gelecekteki değişim maliyeti yüksekse sisteme dahil ederim."*



### "Strategy ve Factory desenleri arasındaki fark nedir?"

Bu ikisi birbirine çok karıştırılır çünkü ikisi de kodda `if-else` veya `switch-case` bloklarını yok etmek için kullanılır ve ikisi de arayüzler (Interface) üzerinden çalışır.

#### 1. "Niyet" (Intent) Farkı
Masada kuracağın ilk cümle şu olmalıdır: 
*"İkisi de if-else bloklarını temizlese de aralarındaki temel fark niyetlerindedir. **Factory nesne yaratmakla (Creational), Strategy ise nesnenin nasıl davranacağıyla (Behavioral) ilgilenir.***"

*   **Factory'nin Sorumluluğu (Kim?):** "Bana hangi nesnenin lazım olduğuna kim karar verecek ve onu kim üretecek?" sorusunu cevaplar. Kodun içinde `new` anahtar kelimesini gizler.
*   **Strategy'nin Sorumluluğu (Nasıl?):** "Elimdeki bu nesne, kendisine verilen işi hangi algoritmayla/yöntemle yapacak?" sorusunu cevaplar. Çalışma zamanında (runtime) bir işin yapılış şeklini değiştirmeyi sağlar.

#### 2. Gerçek Hayat Senaryosu Üzerinden Açıklama
Teoriyi verdikten sonra hemen kendi tecrübeni masaya koyarak somutlaştır:

*"Bunu e-ticaret ödeme (checkout) altyapısı üzerinden şöyle açıklayabilirim:*
*Sistemde kullanıcının seçebileceği 3 ödeme yöntemi var: Kredi Kartı, Kripto ve Havale. Her birinin bankaya bağlanma ve komisyon hesaplama algoritması tamamen farklı.*

1.  *Önce, bu algoritmaların her birini ayrı sınıflara yazarım (`CreditCardStrategy`, `CryptoStrategy`). Bu sınıfların hepsi `IPaymentStrategy` arayüzünden türer. Bu sayede ana ödeme metodum, içerde hangi matematiğin döndüğünü bilmez. **(Bu Strategy desenidir. İşin NASIL yapılacağını soyutladım).***
2.  *Ancak, kullanıcının ekrandan 'Kripto' seçtiğini anladığım anda, ana kodumun içine `if (type == "Crypto") new CryptoStrategy()` yazarsam Strategy kullanmamın bir anlamı kalmaz, kod yine kirlenir. İşte burada **Factory** devreye girer. `PaymentFactory.Create(type)` çağrısı yaparım. Factory bana doğru strateji nesnesini üretip geri döner. **(Bu Factory desenidir. KİMİN üretileceğini soyutladım).***"

#### 3. Senior/Lead Dokunuşu (Altın Kural)
Bu örnekten sonra mülakatı tamamen domine edecek o "Lead Engineer" cümlesini kurmalısın:

*"Özetle; gerçek dünya projelerinde, özellikle dağıtık sistemlerde bu iki desen neredeyse her zaman **birlikte çalışır**. Factory, doğru Strategy'yi yaratmakla görevlidir. Hatta günümüzde .NET ekosisteminde özel bir Factory sınıfı yazmak yerine, **Dependency Injection (DI) konteynerinin kendisini bir Factory gibi kullanarak** (örneğin `IEnumerable<IPaymentStrategy>` enjekte ederek) çalışma zamanında doğru stratejiyi çözümlüyoruz (resolve ediyoruz)."*

#### Bu Savunmanın Mülakatçıdaki Etkisi
Mülakatçı bu cevabı duyduğunda senin hakkında şu notları düşecektir:
1.  Paternleri kitaplardan değil, gerçek üretim (production) kodundan öğrenmiş.
2.  Modern framework'lerin (Dependency Injection) paternleri nasıl evrimleştirdiğini biliyor.
3.  "Separation of Concerns" (Sorumlulukların Ayrılığı) prensibini kod seviyesinde çok iyi anlıyor.

