## SOLID
SOLID, kodun zaman içinde büyümesini, değiştirilmesini ve test edilmesini kolaylaştıran beş temel tasarım prensibinin baş harfleridir. Mülakatlarda sadece bu harflerin açılımını sormazlar, genellikle hatalı bir kod verip "Burada hangi prensip ihlal ediliyor?" diye sorarlar veya senin yazdığın kod üzerinden bir prensibi açıklamanı isterler.

**Soru:**
Bildiğin kadarıyla SOLID'in baş harflerini (S, O, L, I, D) kısaca, tek cümlelik pratik tanımlarıyla açıklar mısın? Hangi prensip senin için en önemlisi veya gerçek dünya projelerinde uygulaması en zor olanıdır?


### 1. S - Single Responsibility Principle (SRP - Tek Sorumluluk Prensibi)
Bir sınıfın (veya modülün) değişmek için yalnızca **tek bir nedeni** olmalıdır. 
*   **Pratik Karşılığı:** Gönderdiğin kod[cite: 1] SRP'nin mükemmel bir örneğiydi. `PaymentParser` sadece metni parçalıyor, `PaymentProcessor` sadece iş kurallarını uyguluyor, `PaymentFormatter` ise sadece çıktıyı biçimlendiriyordu[cite: 1]. Eğer veriyi okuyan ve hesaplayan kod aynı sınıfta olsaydı, hem veri formatı değiştiğinde hem de hesaplama kuralı değiştiğinde aynı sınıfı değiştirmek zorunda kalacaktın.

### 2. O - Open/Closed Principle (OCP - Açık/Kapalı Prensibi)
Yazılım varlıkları (sınıflar, modüller, fonksiyonlar) **genişletilmeye (extension) açık**, ancak **değiştirilmeye (modification) kapalı** olmalıdır.
*   **Pratik Karşılığı:** Sisteme yeni bir özellik eklerken, mevcut ve çalışan kodu (if/else bloklarını) değiştirmemen gerekir. Yeni özelliği, mevcut kodun dışına yeni bir sınıf (genellikle bir Interface implementasyonu) olarak eklemelisin. (Örn: Sisteme "Kripto Ödeme" ekleneceğinde mevcut `PaymentProcessor`'ı değiştirmek yerine, `IPaymentProcessor`'dan türeyen `CryptoProcessor` yazmak).

### 3. L - Liskov Substitution Principle (LSP - Liskov Yerine Geçme Prensibi)
Alt sınıflar (türetilmiş sınıflar), temel sınıflarının (base class) yerine, sistemin davranışını bozmadan kullanılabilmelidir. Alt sınıf, üst sınıfın tüm özelliklerini mantıksal olarak taşımalıdır.
*   **Pratik Karşılığı (Klasik Örnek):** Bir `Kus` sınıfın var ve içinde `Uç()` metodu var. `Kus` sınıfından `Serce` ve `Penguen` sınıflarını türettin. `Serce.Uç()` çalışır ama Penguen uçamadığı için `Penguen.Uç()` metodunda `NotImplementedException` fırlatırsan LSP'yi ihlal etmiş olursun. Çözüm, arayüzleri bölmektir (UçabilenKuşlar, YürüyebilenKuşlar).

### 4. I - Interface Segregation Principle (ISP - Arayüz Ayrımı Prensibi)
İstemciler (client'lar), kullanmadıkları metotları barındıran şişman (fat) arayüzleri uygulamaya zorlanmamalıdır.
*   **Pratik Karşılığı:** Tek bir devasa `IUserRepository` (içinde Add, Update, Delete, GetByEmail, VerifyToken vb. olan) yapmak yerine, ihtiyaca göre daha küçük ve spesifik arayüzler oluşturmaktır (`IReadRepository`, `IWriteRepository`). Böylece sadece okuma yapacak bir servis, yazma metotlarını bilmek zorunda kalmaz.

### 5. D - Dependency Inversion Principle (DIP - Bağımlılığın Tersine Çevrilmesi Prensibi)
Yüksek seviyeli modüller (iş kuralları), düşük seviyeli modüllere (veritabanı, dosya sistemi vb.) doğrudan bağımlı olmamalıdır. Her ikisi de soyutlamalara (abstractions/interfaces) bağımlı olmalıdır.
*   **Pratik Karşılığı:** Bu, C#'taki **Dependency Injection (DI)** mekanizmasının teorik temelidir. Senin iş katmanın (`PaymentProcessor`) gidip doğrudan `new SqlDatabase()` diyerek alt katmanı yaratmamalıdır. Bunun yerine kurucu metoda (Constructor) bir `IDatabase` arayüzü enjekte edilmelidir. Böylece iş katmanı, verinin SQL'den mi yoksa MongoDB'den mi geldiğini umursamaz.

---

### Mülakat Senaryosu: Kod Analizi

Aşağıdaki kısa kod bloğunu incelemeni istiyorum. Scorp'un loglama altyapısı için yazılmış bu kodda **hangi SOLID prensipleri ihlal edilmiştir?** (Birden fazla prensip ihlal ediliyor olabilir). İhlalleri ve çözüm önerini belirtir misin?

```csharp
public class ErrorLogger
{
    public void LogError(string message, string logType)
    {
        if (logType == "File")
        {
            // Dosyaya yazma işlemleri
            System.IO.File.WriteAllText("error.txt", message);
        }
        else if (logType == "Database")
        {
            // Veritabanına bağlanma ve yazma işlemleri
            var db = new SqlConnection("ConnectionString");
            db.Execute("INSERT INTO Logs (Message) VALUES (@message)", new { message });
        }
        else if (logType == "ElasticSearch")
        {
             // ElasticSearch'e HTTP isteği atma işlemleri
             var client = new ElasticClient();
             client.IndexDocument(message);
        }
    }
}
```

Cevap: Open/Closed Principle ihlal eder. Yeni bir log türü geldiğinde bu method değişmek zorundadır. Dependency Inversion Principle ihlal eder ve sql bağlantısı, elastic client bağlantılarını da üstlenmektedir.

Bu kod bloğunda üç farklı SOLID prensibi ihlal edilmektedir:

### 1. OCP (Open/Closed Principle) İhlali
Senin de belirttiğin gibi kod değişime açıktır. Sisteme yeni bir loglama türü (örneğin "Console" veya "MongoDB") eklenmek istendiğinde, `ErrorLogger` sınıfının içindeki `if/else if` bloğunun içine girilip kodun fiziksel olarak değiştirilmesi gerekmektedir. Bu durum, sınıfın genişlemeye açık ancak değişime kapalı olması kuralını bozar.

### 2. DIP (Dependency Inversion Principle) İhlali
Yine doğru tespit ettiğin üzere, üst seviye bir sınıf olan `ErrorLogger`, `SqlConnection` ve `ElasticClient` gibi alt seviye modüllere `new` anahtar kelimesi ile doğrudan (sıkı sıkıya) bağlıdır. İş mantığı katmanı, altyapı araçlarını kendisi üretmemeli; arayüzler (interface) üzerinden dışarıdan almalıdır (Dependency Injection).

### 3. SRP (Single Responsibility Principle) İhlali
`ErrorLogger` sınıfı birden fazla sorumluluk üstlenmiştir. Hem hangi hedefe log atılacağına karar veren bir yönlendirici (router) gibi çalışmakta, hem dosya sistemine (`System.IO`) müdahale etmekte, hem de veritabanı bağlantılarını yönetmektedir. Sınıfın değişmek için birden fazla nedeni vardır.

---

### Mülakat Beklentisi: Refactor Edilmiş Temiz Kod (Çözüm)

Bu ihlalleri gidermek için arayüz (Interface) oluşturulur ve her loglama türü kendi sınıfına ayrılarak **Dependency Injection** ile sisteme enjekte edilir.

```csharp
// 1. Arayüz (Abstraction) - DIP ve OCP'yi çözer
public interface ILogger
{
    void Log(string message);
}

// 2. Alt Sınıflar (Concrete Implementations) - SRP'yi çözer
public class FileLogger : ILogger
{
    public void Log(string message)
    {
        System.IO.File.WriteAllText("error.txt", message);
    }
}

public class DatabaseLogger : ILogger
{
    private readonly string _connectionString;
    
    public DatabaseLogger(string connectionString)
    {
        _connectionString = connectionString;
    }

    public void Log(string message)
    {
        var db = new SqlConnection(_connectionString);
        db.Execute("INSERT INTO Logs (Message) VALUES (@message)", new { message });
    }
}

public class ElasticSearchLogger : ILogger
{
    private readonly ElasticClient _client;
    
    public ElasticSearchLogger(ElasticClient client)
    {
        _client = client;
    }

    public void Log(string message)
    {
         _client.IndexDocument(message);
    }
}

// 3. Ana Sınıf (Client) - Artık doğrudan implementasyonlara değil, soyutlamaya bağımlı
public class ErrorService
{
    private readonly ILogger _logger;

    // Dependency Injection (Constructor Injection)
    public ErrorService(ILogger logger)
    {
        _logger = logger;
    }

    public void ProcessError(string message)
    {
        _logger.Log(message);
    }
}
```