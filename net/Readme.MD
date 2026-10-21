## Özgür Mülakat Dosyası

* [Kendim Hakkında](AboutMySelf.MD)
* [Kendime ait anlatılarım](MySelf.MD)
* [CV](Özgür Kurul - CV.pdf)
* [İş İlanı](JobDetail.MD)

### Konu Başlık notlarım
* Space-Time Trade off (Hafıza - Zaman Ödünleşimi)
* .NET Veri Yapıları
* Stable and scalable projects
* Agile development environment
* SQL, NoSQL, and in-memory
* Backend services
* Distributed systems
* Python
* Node.js
* React, angular, next.js etc frontend technologies
* concurrency problems and redis lock
* what is the Task method? 
* Mesaj Kuyruk yapılarında dlq

### Öneriler
* Sesli Düşün (Think Out Loud): 2 saatlik süreçte kodu yazıp bitirmenden ziyade, oraya nasıl ulaştığını görmek isteyecekler. Trade-off'ları (ödünleşimleri) sesli belirt. "Bunu böyle yaparsak CPU'dan kazanırız ama memory'den kaybederiz" gibi analizler çok değerlidir.
* Edge Case'leri Gözet: Kodun çalışması yetmez. "Ya currency formatı yanlış gelirse?", "Ya StreamerId null gelirse?", "Ya aynı anda aynı bakiyeden iki farklı işlem düşmeye çalışırsa (Race Condition)?" gibi soruları onlar sormadan sen ele almalısın.
* Mevcut Kodu Genişletme: Gönderdiğin Coderbyte kodunu açıp, "Bunu eşzamanlı (multi-threaded) çalışacak şekilde nasıl refactor ederiz?", "Eğer bu veri bir dosyadan değil de saniyede 10.000 mesaj akan bir kuyruktan gelseydi kodda neyi değiştirirdik?" gibi sorular üzerinden pair-programming yapabilirsiniz.
* Mini Sistem Tasarımı & Implementasyonu: Sıfırdan bir In-Memory Rate Limiter, Thread-Safe bir Cache mekanizması (örneğin LRU Cache) veya basit bir pub/sub sistemi yazmanı isteyebilirler.
* Data Structures & Algoritmalar: İlanın kalbinde "high-scale problems" yatıyor. Bu yüzden zaman karmaşıklığı ($O(N)$, $O(1)$) ve bellek yönetimi kritik olacak. Seçtiğin veri yapılarının nedenlerini (neden List yerine Dictionary, neden ConcurrentDictionary vb.) savunman beklenecektir.
* Yüksek Ölçekli Veri Akışı: Saniyede on binlerce veri noktasının işlendiği yoğun telematik veya sensör akışı sistemlerindeki tecrüben burada senin en büyük silahın. Anlık konum veya hız verisini işlemekle, milyonlarca anlık kullanıcının beğeni/bağış (streamer) verisini işlemek mimari olarak birbirine çok benzer.
* Teknoloji Bağımsız Düşünme: Sana .NET sormayacaklar ama sen cevaplarını mimari araçlarla vereceksin. Gelen yükü nasıl dengelersin? Veritabanında darboğaz oluşursa ne yaparsın? (Burada PostgreSQL sharding, Read-Replica mantığı veya Redis ile In-Memory caching devreye girmeli).
* Asenkron İletişim: Dağıtık sistemlerde servislerin birbiriyle konuşması kritik. Event-driven mimariler, Kafka gibi message broker'ların veri kaybını (data loss) önlemedeki rolü ve gRPC gibi yüksek performanslı protokollerin nerelerde tercih edileceği üzerine sağlam tartışmalar dönebilir.

### Scorp Mülakatı
* [İş İlanı](JobDetail.MD)
* [Scorp Assessment](Assesment/Question.md)

### Konular
* [Big O Notasyonunu İçeriği](BigONotation.MD)
* [Pekiştirmek için tipik bir mülakat sorusunu](Question-Reconciliation.MD)
* [Garbage Collector "Gens" (Generations - Nesiller)](GarbageCollector.MD)
* [Bellek Yönetimine (Memory Management) Geçiş](MemoryManagement.MD)
* [Asenkron Programlama (Async/Await) ve Concurrency](ConcurrencyAndAsync.MD)
* [Sistem dizaynı ve yüksek ölçekli veritabanı yönetimi](SystemDesignWithHighScalableDb.MD)
* [Veri Tutarlılığı](DataConsistency-CAP-ACID.MD)
* [Dağıtık Mimariler Arası İletişim](DistributedSystems.MD)
* [SOLID](SOLID.MD)
* [Veritabanı İndeksleme (Database Indexing) ve Sorgu Optimizasyonuna (Query Optimization)](DatabaseIndexingAndQueryOptimization.MD)
* [Cross-cutting concerns](CrossCuttingConcerns.MD)
* [Mikroservis Mimari Desenleri](MicroServicePatterns.MD)
* [Kod Kalitesi ve Test Süreçleri](Testing.MD)
* [Deployment (Dağıtım) stratejileri](Deployment.MD)
* [Observability (Gözlemleme - Loglama, Metrikler ve Dağıtık İzleme)](Observability.MD)






