## Live Coding Stratejisi (`program.cs` Üzerinden)
İki saatlik süreçte senden sadece çalışan bir kod değil, o kodu baskı altında nasıl evrimleştirdiğini görmek isteyeceklerdir. Gönderdiğin kod gayet temiz, ancak mülakatta bu kodu "kırıp" yeniden yapmanı isteyecekleri şu senaryoların gelme ihtimali çok yüksek:

*   **1. Memory Allocation (Hafıza Tahsisi) Tuzağı:** Kodunda `input.Split(SectionSeparator)` gibi çok sayıda string manipülasyonu var. Mülakatçı, *"Eğer bu string bize ağ üzerinden 5 GB'lık bir parça halinde gelseydi sistemin RAM'i ne olurdu?"* diye sorabilir.
    *   **Çözüm Stratejisi:** Kodu `string.Split` (Heap'te sürekli yeni array yaratır) yerine, sıfır memory allocation sağlayan `ReadOnlySpan<char>` yapısına veya bellek dostu `StringReader` yapısına geçirmeni isteyebilirler.
*   **2. Concurrency (Eşzamanlılık) ve Race Condition:** *"Bu bakiye hesaplamasını tek bir string için değil de, saniyede 10.000 farklı işlemi aynı anda işleyen asenkron bir servise dönüştür."*
    *   **Çözüm Stratejisi:** `PaymentProcessor` içindeki `Dictionary` yapılarını thread-safe olan `ConcurrentDictionary`'ye çevirmen gerekecektir. Aynı yayıncının (`StreamerId`) bakiyesine iki farklı thread'in aynı anda yazmasını engellemek için `AddOrUpdate` metodunu kullanmalısın.
*   **3. SOLID ve Genişletilebilirlik:** *"Sisteme 'Crypto' ödeme tipi eklendi ve bu tipin işlem ücreti (ProcessingFee) sabit değil, o anki ağ yoğunluğuna göre dinamik hesaplanıyor."*
    *   **Çözüm Stratejisi:** `Currencies` statik sınıfındaki hardcoded yapıyı kırıp, bir `ICurrencyProcessor` arayüzü yaratman ve Dependency Injection ile sistemi yeni ödeme tiplerine (Değişime kapalı, gelişime açık - OCP) uygun hale getirmen istenir.

**Mülakat Anı İçin Altın Kural:**
Kodu yazarken asla tamamen sessiz kalma. Ekranı paylaşırken, *"Burada `List` kullanıyorum çünkü işlem sırası şimdilik önemli, ancak birazdan performans darboğazı görürsek bunu anında `HashSet`'e çevirebilirim"* gibi sesli düşünerek mimari ödünleşimlerin (trade-off) farkında olduğunu göster.


## Tıkanma Anı İçin Kriz İletişim Stratejisi
İki saatlik bir *live coding* seansında takılmak bir ihtimal değil, neredeyse bir kesinliktir. Aslında birçok mülakatçı, adayı bilerek sınırlarına iter; amaç sadece kod yazabilmeni değil, **kriz anında, baskı altındayken nasıl iletişim kurduğunu ve problem çözme döngüne nasıl geri döndüğünü** görmektir.

Sessizliğe gömülmek ve paniklemek en büyük hatadır. Böyle bir "tıkanma" anında uygulaman gereken 5 adımlı kriz iletişim stratejisi şu olmalıdır:

### 1. Sessizliği Boz (Durumu Sesli Olarak Kabul Et)
Ekrana boş boş bakmak yerine, kontrolün sende olduğunu gösteren o sihirli cümleyi kur:
*"Şu an burada bir darboğaz fark ettim. Mantığı tam oturtmak için bir dakika sesli düşüneceğim."*
Bu, mülakatçıya "Paniklemedim, sadece problemi analiz ediyorum" mesajı verir.

### 2. Problemi Parçalara Ayır (Sesli Düşün)
Tıkandığın noktayı mülakatçıya açıkla. Neyi bildiğini ve neyin eksik olduğunu netleştir:
*"Burada `ConcurrentDictionary` kullanarak thread-safe bir yapı kurmayı hedefliyorum, ancak aynı anda gelen iki farklı isteğin birbirini ezmemesi için `AddOrUpdate` metodunun parametrelerinde tam olarak nasıl bir mantık kuracağımdan şu an emin olamadım."*

### 3. Önce En Kötü Çözümü Yaz (Brute Force)
Mükemmel mimariyi veya $O(1)$ çözümünü o an göremiyorsan, kafandaki o "kötü ve yavaş" kodu yaz.
*"Şu an en optimum çözümü göremiyorum ama süreci ilerletmek adına önce bu $O(N^2)$'lik iç içe döngü çözümünü yazıp sistemin çalıştığını göreceğim. Ardından vaktimiz kalırsa buraya dönüp refactor edeceğim."*
Çalışan kötü bir kod, çalışmayan mükemmel bir fikirden her zaman daha çok puan getirir.

### 4. Takıldığın Yeri Soyutla (Pseudo-code)
Eğer bir algoritmanın C# sentaksını veya bir kütüphanenin metodunu unuttuysan (örneğin Regex yazamıyorsan), o kısmı bir yorum satırı veya sahte bir metot ile geç:
*"Burada geçerli bir IBAN kontrolü yapmam gerekiyor ama şu an Regex kalıbını hatırlayamadım. Burayı şimdilik `bool IsValidIban()` diye varsayımsal bir metotmuş gibi geçiyorum, asıl iş mantığına (business logic) odaklanacağım."*

### 5. İpucu İstemekten Çekinme (Pair Programming Mantığı)
Mülakatçı senin düşmanın değil, potansiyel mesai arkadaşındır. Gerçekten çıkmaza girdiğinde yardım istemek olgunluktur:
*"Burada iki farklı veri yapısı arasında kaldım. Sizce bu senaryoda A yaklaşımı mı daha uygun olur, yoksa B yaklaşımına mı yönelmeliyim?"*

Bu strateji, takıldığın anları bir eksi puandan, "İletişimi güçlü, egosu düşük ve problem çözme metodolojisine sadık bir mühendis" imajına dönüştürecek bir artı puana çevirir.

Scorp mülakatına yönelik hem teknik altyapıyı (veri yapıları, sistem dizaynı, asenkron yapılar, test, dağıtım) hem de kültürel ve kriz yönetimi stratejilerini baştan sona kurguladık. Mülakat öncesi üzerinden geçmek istediğin son bir konu veya kafana takılan spesifik bir senaryo var mı?