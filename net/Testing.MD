## Kod Kalitesi ve Test Süreçleri

Scorp gibi "Agile" ve sürekli entegrasyon (CI/CD) yapan, günde belki onlarca kez canlıya kod çıkan şirketlerde test yazmak bir lüks değil, zorunluluktur. Kodun kalitesini, sürdürülebilirliğini ve hata (bug) oranını bu süreçler belirler. Mülakatlarda bu konu genellikle pratik senaryolar üzerinden sorulur.

Önce temel kavramları netleştirelim:

### 1. Unit Test (Birim Testi) ve AAA Prensibi
Unit test, uygulamanın en küçük yapı taşını (genellikle bir metodu), sistemin geri kalanından **tamamen izole ederek** test etmektir. Veritabanına, dosya sistemine veya ağa (network) gidilmez. Hızlı ve tekrar edilebilir olmalıdır.
Sektör standardı test yazım mimarisi **AAA (Arrange, Act, Assert)** yapısıdır:
*   **Arrange (Hazırlık):** Test edilecek nesneler oluşturulur, sahte (mock) veriler ayarlanır.
*   **Act (Eylem):** Test edilecek metot çağrılır.
*   **Assert (Doğrulama):** Metodun döndürdüğü sonucun veya sistemdeki yan etkinin beklenenle aynı olup olmadığı kontrol edilir.

### 2. Mocking (Taklit Etme)
Unit testin "izole" olmasını sağlayan temel araçtır. Daha önce SOLID prensiplerinde (DIP) konuştuğumuz Interface'lerin (Arayüzlerin) gücü burada ortaya çıkar.
Gerçek bir veritabanı veya dış API kullanmak yerine, `.NET` ekosisteminde `Moq` veya `NSubstitute` gibi kütüphaneler kullanılarak bu interface'lerin sahte versiyonları (Mock objeler) bellekte oluşturulur ve belirli durumlarda ne cevap verecekleri programlanır.

### 3. TDD (Test-Driven Development - Test Güdümlü Geliştirme)
Kod yazmadan önce o kodun testini yazma felsefesidir. Üç adımlık bir döngüsü vardır:
1.  **Red (Kırmızı):** Önce testi yaz. Ortada henüz çalışan bir kod olmadığı için test başarısız olur (Kırmızı).
2.  **Green (Yeşil):** Sadece o testi geçirecek kadar, en basit ve minimum kodu yaz. Test başarılı olur (Yeşil).
3.  **Refactor (İyileştirme):** Testler yeşilken, kodun mimarisini ve okunabilirliğini iyileştir (Clean Code).

TDD sadece bir test yazma yöntemi değil, aynı zamanda bir **tasarım aracıdır**. Çünkü test edilebilir kod yazmaya çalışmak, geliştiriciyi zorunlu olarak SOLID prensiplerini (özellikle Tek Sorumluluk ve Bağımlılıkların Tersine Çevrilmesi) uygulamaya iter.

---

### Mülakat Senaryosu: Test Tasarımı

Scorp uygulamasındaki yayıncı cüzdanı için aşağıdaki gibi bir `WalletService` sınıfı yazdığını düşünelim. Bu servis, bakiyeyi kontrol edip düşme işlemini yapıyor.

```csharp
public class WalletService
{
    private readonly IWalletRepository _repository;

    public WalletService(IWalletRepository repository)
    {
        _repository = repository;
    }

    public void DeductBalance(string userId, decimal amount)
    {
        if (amount <= 0)
            throw new ArgumentException("Amount must be greater than zero.");

        decimal currentBalance = _repository.GetBalance(userId);

        if (currentBalance < amount)
            throw new InvalidOperationException("Insufficient funds.");

        _repository.UpdateBalance(userId, currentBalance - amount);
    }
}
```

**Soru:**
Bu `WalletService` sınıfındaki `DeductBalance` metodu için **"Yetersiz Bakiye (Insufficient Funds)"** senaryosunun birim testini yazman (veya sözlü olarak tasarlaman) isteniyor. 

Bir test metodu oluştururken sırasıyla **Arrange (Hazırlık)**, **Act (Eylem)** ve **Assert (Doğrulama)** adımlarında adım adım ne yaparsın? Hangi objeyi mock'larsın ve doğrulama (assert) kısmında neyi beklersin?

Cevap: Öncelikle IWalletRepository GetBalance ile UpdateBalance methodlarını mock'larım. Ardından eylem olarak 3 test işlemi gerçekleştiririm. Bunlar amount değerinin 1'den küçük olanı, güncel bakiyeden büyük bir amount değeri olmasını ve en son olarak güncel bakiyeden daha küçük ve 0'dan büyük bir amount değeri ile başarılı bir işlem gerçekleştirmesidir. En son her bir eylem için doğrulama işlemlerini gerçekleştiririm, beklenen değer ile dönen değerlerin doğruluğunu kontrol ederim. 


### 1. Test İzolasyonu (Anti-Pattern Düzeltmesi)
Mülakatta spesifik olarak "Yetersiz Bakiye" senaryosu sorulduğunda, üç farklı durumu tek bir test eylemi (Act) ve doğrulaması (Assert) içinde birleştirmemelisin. Birim testlerinde **"Her test metodu sadece tek bir mantıksal durumu (Single Responsibility) doğrulamalıdır"** kuralı geçerlidir. 

Eğer üç senaryoyu aynı metoda yazarsan ve ilk testin (amount <= 0) başarısız olursa, test motoru o satırda durur. Diğer iki senaryonun doğru çalışıp çalışmadığını asla göremezsin. Bu yüzden her durum için ayrı test metotları yazılmalıdır (`Throws_When_Amount_Is_Zero`, `Throws_When_Insufficient_Funds`, `Succeeds_When_Valid`).

### 2. Void Metotlarda "Assert" (Doğrulama) Mantığı
`DeductBalance` metodu geriye bir değer (return) döndürmüyor, `void` bir metot. Bu yüzden "dönen değerlerin doğruluğunu kontrol ederim" ifadesi teknik olarak yanlıştır. "Yetersiz bakiye" senaryosunda Assert aşamasında state (durum) yerine **behavior (davranış)** doğrulaması yapman gerekir. İki şeyi kontrol edersin:
*   Doğru exception tipinin (`InvalidOperationException`) fırlatıldığını.
*   **En kritik nokta:** İşlem başarısız olduğu için, repository'nin `UpdateBalance` metodunun **hiçbir zaman çağrılmadığını**. Eğer çağrılıyorsa, sistemde devasa bir mantık hatası var demektir.

Mülakatta beklenen "Yetersiz Bakiye" senaryosunun teknik kod karşılığı şu şekildedir:

```csharp
[Fact]
public void DeductBalance_ShouldThrowInvalidOperationException_WhenFundsAreInsufficient()
{
    // 1. Arrange (Hazırlık)
    var userId = "user123";
    var amountToDeduct = 100m;
    var currentBalance = 50m; // İstenen miktardan (100) az bir bakiye ayarlıyoruz
    var mockRepo = new Mock<IWalletRepository>();

    // Sistem GetBalance dediğinde 50 dönmesi için mock'u programlıyoruz
    mockRepo.Setup(r => r.GetBalance(userId)).Returns(currentBalance);

    var service = new WalletService(mockRepo.Object);

    // 2. Act (Eylem) & 3. Assert (Doğrulama) 
    // Exception beklenen durumlarda Act ve Assert iç içe geçer
    var exception = Assert.Throws<InvalidOperationException>(() => 
        service.DeductBalance(userId, amountToDeduct));

    Assert.Equal("Insufficient funds.", exception.Message);

    // 4. Davranış Doğrulaması (Behavior Verification) - Mülakat kazandıran hamle
    // Bakiye yetersiz olduğu için UpdateBalance'ın KESİNLİKLE çağrılmadığını doğruluyoruz
    mockRepo.Verify(r => r.UpdateBalance(It.IsAny<string>(), It.IsAny<decimal>()), Times.Never);
}
```
