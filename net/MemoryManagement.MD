### Bellek Yönetimine (Memory Management) Geçiş

Madem `HashSet` diyerek RAM'i oyuna dahil ettik ve yüksek ölçekli sistemlerden bahsediyoruz, senin de planladığın gibi buradan bellek kullanımı konularına geçiş yapabiliriz. Canlı sistemlerde milyonlarca kaydı hafızaya alırken .NET'in bellek yönetimi (Stack vs Heap) ve Garbage Collector (GC) davranışları sistemin kaderini belirler. 

Scorp mülakatında sana şu soruyu yönelttiklerini düşünelim: **"Milyonlarca anlık ödeme veya telemetri verisini hafızada işlerken, veri modelini `class` (Reference Type) yerine `struct` veya `record struct` (Value Type) olarak tasarlamanın, bellek tahsisi (allocation) ve Garbage Collector (GC) performansı üzerinde nasıl bir etkisi olur?"** Nasıl bir yaklaşım sunardın?


**Cevap;** *"Yüksek frekanslı veriler için struct kullanarak Heap tahsisini (Heap Allocation) sıfıra indiririz. GC'nin takip etmesi gereken nesne sayısını düşürerek Garbage Collector üzerindeki baskıyı tamamen ortadan kaldırırız,"*.

## "Generation" (Gen0, Gen1, Gen2) ve Stack/Heap ayrımı.

### 1. Struct'lar ve GC'nin Gerçek İlişkisi (Stack vs. Heap)

En büyük düzeltmemiz şu: `struct` veya `record struct` (Değer Tipleri - Value Types) kullanmak, veriyi Gen2'den Gen1'e çekmez. **Aslında yerel (local) değişken olarak tanımlanan struct'lar GC'nin radarına hiç girmez!**

*   **Class (Reference Type):** Hafızanın **Heap** bölgesinde tutulur. GC tarafından takip edilir. Yaşam döngüsüne göre Gen0'da doğar, temizlenmezse Gen1'e, oradan da Gen2'ye terfi eder.
*   **Struct (Value Type):** Hafızanın **Stack** bölgesinde tutulur. Metodun işi bittiğinde (scope kapandığında) Stack'ten otomatik olarak silinir. Ortada bir GC işlemi, nesne takibi veya Generation (Gen0/1/2) kavramı yoktur. Hızın artmasının ve memory'nin şişmemesinin asıl sebebi, GC'ye hiç iş bırakmamaktır (Zero Allocation).

### 2. Yüksek Ölçekli Verilerde "Array of Structs" Farkı

Saniyede binlerce paketin aktığı bir araç takip sistemi veya yoğun anlık sensör verisi işlediğini düşünelim. Milyonlarca veri noktasını bir listede tutman gerektiğinde asıl sihir burada başlar:

*   **`List<SensorClass>` (Class kullanırsan):** Heap üzerinde 1 adet liste nesnesi ve listenin içinde belleğin farklı yerlerine dağılmış 1.000.000 adet sensör referansı oluşur. GC'nin takip etmesi gereken **1.000.001** ayrı obje vardır. GC çalıştığında sistemi kilitler (Stop-the-world) ve hepsini taramak zorunda kalır.
*   **`List<SensorStruct>` (Struct kullanırsan):** Heap üzerinde sadece 1 adet liste nesnesi oluşur. Struct değerleri bu listenin içine *inline* (bitişik ve peş peşe) olarak paketlenir. GC'nin takip etmesi gereken sadece **1** adet nesne vardır. GC üzerindeki baskı (Memory Pressure) neredeyse sıfıra iner.

### 3. CPU Önbelleği (Cache Locality)

Struct'lar bellekte ardışık (sıralı) dizildikleri için CPU, veriyi RAM'den çekip L1/L2 önbelleğine alırken çok daha verimli çalışır (Cache Hit). Sınıflar (Referans tipler) ise bellekte dağınık tutulduğu için CPU, pointer'ları takip etmek (dereferencing) için sürekli adresten adrese atlamak zorunda kalır (Cache Miss). "Erişim hızı artar" tezinin arkasındaki teknik ve fiziksel gerçek budur.

---

### Soru: Şişman struct ile value type iletimi
Struct'lar çok güzel, evet. Ancak içinde 15-20 tane property barındıran oldukça "şişman" bir `struct` tasarladın. Bu devasa yapıyı, üzerinde işlem yapması için başka bir metoda parametre olarak gönderdiğinde aniden performansta ciddi bir düşüş yaşadığını gördün. Bu düşüşün sebebi (Stack'in çalışma mantığı gereği) nedir ve bunu çözmek için C#'ın hangi parametre keyword'ünü (örneğin `ref`, `in` vb.) kullanırsın?

**Cevap:** Struct'lar (Value Types) varsayılan olarak **"pass-by-value" (değer ile geçiş)** davranışına sahiptir. Sen 15 property'si olan 100 byte'lık bir struct'ı bir metoda parametre olarak gönderdiğinde, .NET çalışma zamanı bu 100 byte'lık verinin tamamını Stack üzerinde **byte-by-byte kopyalar**. Metot içinde başka bir metot çağırırsan bir kopya daha oluşur. Bu sürekli kopyalama işlemi CPU döngülerini israf eder ve performansı ciddi şekilde düşürür. 

Senin belirttiğin **`ref`** keyword'ü, verinin kopyalanmasını engeller ve Stack üzerindeki orijinal bellek adresini (pointer) metoda iletir. Bu, performans sorununu çözer. Ancak mimari ve güvenlik açısından eksik bir çözümdür. 

Senior/Lead seviyesinde verilmesi beklenen tam yanıt **`in`** (veya modern C# ile gelen **`ref readonly`**) keyword'üdür. 
*   **`ref`** kullanırsan, veriyi alan metot o struct'ın içindeki değerleri değiştirebilir (mutability).
*   **`in`** kullanırsan, struct yine adres üzerinden (referansla) kopyalanmadan iletilir, ancak derleyici (compiler) o verinin metot içinde değiştirilmesini yasaklar (immutable/read-only). Böylece hem yüksek performans sağlanır hem de verinin yanlışlıkla bozulması engellenir.