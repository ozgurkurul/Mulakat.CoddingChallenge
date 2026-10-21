### Asenkron Mesajlaşma ve Event-Driven Mimari (Kafka / RabbitMQ)

Madem darboğazları aşmak için araya Redis ve asenkron worker'lar koyduk, buradan ilanın "Distributed Systems" ve senin "Asenkron yapılar" hedefine uygun şekilde Message Broker'lara geçiş yapalım.

Scorp mimarisinde mikroservisler arası iletişim kuruyoruz.
*   **Servis A (Stream Service):** Canlı yayın bittiği anda "Yayın Kapandı" event'ini fırlatıyor.
*   **Servis B (Analytics):** Yayın süresini ve izleyici istatistiklerini hesaplayıp raporluyor.
*   **Servis C (Payment/Billing):** Yayıncının o yayından kazandığı bağışları (senin yazdığın `program.cs`'teki gibi) hesaplayıp bakiyesine ekliyor.

Bir yazılım mühendisi olarak Servis A'nın Servis B ve C'yi **doğrudan gRPC (veya REST API) ile (Senkron/Request-Reply)** çağırması yerine, araya **Apache Kafka (veya RabbitMQ) koyarak Event-Driven (Pub/Sub) bir mimari** tasarlamanı istiyoruz.

**Soru:**
Bu senaryoda doğrudan gRPC/HTTP çağrısı yerine neden Kafka gibi bir Message Broker kullanmayı tercih etmeliyiz? Dağıtık bir sistemde bu Pub/Sub (Publish/Subscribe) yaklaşımının bize sağladığı 3 temel avantaj (örneğin Coupling, Resilience, Scalability açılarından) nedir?


### Cevap
En önemli faydası loosely coupling yani gevşek bağlılık ile servisler ile bağımlılığı ortadan kaldırıyoruz. Servis sadece kafka gibi bir kuyruğa mesajı bırakıyor, bu bildirimin cevabını beklemeden ve gerisine karışmadan bitiriyor. Böylece servis sadece bildirimi yapıyor ve sonuçla ilgilenmiyor. Scalability olarak eğer sistemde yoğunluk olsa dahi, kuyruk mekanizması bunu dağıtık mimarisi ile hızlıca işleyebilecektir. Örneğin kafka'da partition olarak eklenilen her mesajın key değerine göre ölçeklenebilirliğini otomatik olarak desteklemektedir. Resilience olarak kafka gibi yapılar cluster'lar ile çalışmakta ve bir sunucu çökse dahi diğer sunucular ayakta olduğu müddetçe verilere hiçbir şey olmayacaktır, ve sunucular kapanıp açılsa dahi silinmeyecektir.  


1.  **Loose Coupling (Gevşek Bağlılık):** Servisler arası zaman ve mekan bağımlılığı ortadan kalkar. A servisi (Producer) sadece mesajı kuyruğa bırakır (fire-and-forget). B ve C servislerinin (Consumer) o an ayakta olup olmaması, yavaş çalışması veya IP adresleri A servisini ilgilendirmez. A servisi kendi işini milisaniyeler içinde bitirip kaynaklarını serbest bırakır.
2.  **Scalability (Ölçeklenebilirlik):** Veri akışı arttığında sistem yatayda (scale-out) kolayca büyütülür. Kafka'nın **Partition** mimarisi ve mesajların `Key` bazlı dağıtılması sayesinde, kuyruktaki veriyi okuyan **Consumer Group** (Tüketici Grubu) içindeki instance (sunucu) sayısı artırılarak yük anında dağıtılır. Veri işleme kapasitesi doğrusal olarak artar.
3.  **Resilience (Dayanıklılık / Hata Toleransı):** İki boyutlu bir dayanıklılık sağlar. Birincisi; Kafka cluster mimarisinde çalışır ve mesajları diske (commit log) yazarak replike eder, broker çökse bile veri kaybolmaz. İkincisi ve en önemlisi; Consumer (Örneğin Servis B) çökerse, Kafka mesajı silmez. Servis B yeniden ayağa kalktığında, en son kaldığı **Offset** (işaretçi) değerinden mesajları okumaya devam eder. Sistemde hiçbir veri ve state (durum) kaybı yaşanmaz.

---

### Sistem Dizaynı ve Event-Driven Mimarilerde "Dual Write" Problemi

Madem işin içine veritabanı (PostgreSQL) ve Message Broker (Kafka) girdi, dağıtık sistemlerin en zorlayıcı ve mülakatların en eleyici sorularından birine geçelim.

**Senaryo:**
Kullanıcı yayıncıya bağış yaptı. Servis C (Ödeme Servisi) bu işlemi işliyor. Servisin kendi kod bloğu (metodu) içinde arka arkaya iki işlem yapması gerekiyor:
1.  Kendi PostgreSQL veritabanında yayıncının bakiyesini güncelleyecek (`UPDATE Balances SET Amount = Amount + 100`).
2.  Kafka'ya `BakiyeGuncellendiEvent` mesajı fırlatacak ki Analytics ve Bildirim servisleri çalışsın.

**Problem:**
Bu iki sistem (PostgreSQL ve Kafka) birbirinden bağımsızdır ve tek bir klasik *Database Transaction* içine alınamazlar.
*   Eğer önce DB'ye yazar, sonra Kafka'ya mesaj atarken network koparsa veya Kafka timeout verirse: Veritabanı güncellenir ama sistemin geri kalanının haberi olmaz (Tutarsızlık).
*   Eğer önce Kafka'ya mesaj atar, sonra DB'ye yazarken veritabanı kilitlenirse: Sistemdeki herkes yayıncının bakiyesi arttı sanır ama veritabanında para yoktur (Kritik Finansal Hata).

**Soru:**
Dağıtık bir sistemde, kendi veritabanına yazma işlemi ile Message Broker'a mesaj gönderme işleminin **atomik (ya hep ya hiç)** olarak çalışmasını %100 garanti altına almak için hangi tasarım desenini (Design Pattern) kurgularsın ve bu desen nasıl çalışır?


Cevap: **Transactional Outbox Pattern**'dır. 

Dual Write (Çifte Yazma) probleminin temelinde, iki farklı altyapının (PostgreSQL ve Kafka) tek bir işlem (transaction) içine alınamaması yatar. Outbox Pattern, bu problemi dağıtık bir işlemi, yerel (local) bir işleme indirgeyerek çözer.

Sistem şu şekilde kurgulanır ve çalışır:

### 1. Adım: Yerel Transaction ve Outbox Tablosu
Servisin kendi PostgreSQL veritabanında, iş tablolarının (örneğin `Balances`) yanına sadece fırlatılacak event'leri tutacak bir `Outbox` tablosu oluşturulur. İşlem gerçekleştiğinde aşağıdaki adımlar tek bir yerel veritabanı transaction'ı içinde yapılır:

1.  `BEGIN TRANSACTION`
2.  `Balances` tablosundaki bakiye güncellenir.
3.  Kafka'ya gönderilecek olan mesajın içeriği (örneğin JSON payload'u, Event Tipi ve benzersiz bir EventId) `Outbox` tablosuna `INSERT` edilir.
4.  `COMMIT`

Bu sayede klasik ilişkisel veritabanının ACID prensiplerinden faydalanmış oluruz. Sistem %100 atomik hale gelir: Ya bakiye güncellenip mesaj kuyruk tablosuna yazılır, ya da herhangi bir hata durumunda her ikisi birden geri alınır (Rollback).

### 2. Adım: Message Relay (Mesajları Kafka'ya Taşıma)
Veritabanına yazma işlemi bittikten sonra, asıl servis kullanıcıya başarılı yanıtını (HTTP 200 OK) döner. Artık `Outbox` tablosundaki kayıtları okuyup Kafka'ya taşıyacak bağımsız bir mekanizmaya ihtiyaç vardır. Bu iki şekilde yapılır:

*   **Polling Publisher:** Arka planda çalışan bir Worker/Background Service, sürekli olarak (örneğin saniyede bir) `Outbox` tablosuna sorgu atar (`SELECT * FROM Outbox WHERE IsProcessed = false`). Okuduğu mesajları Kafka'ya fırlatır. Kafka'dan başarılı (Ack) yanıtı aldığında, veritabanındaki o kaydı `IsProcessed = true` olarak günceller veya tablodan siler.
*   **Change Data Capture (CDC) - Örn: Debezium:** Çok yüksek ölçekli sistemlerde (Scorp gibi) polling yapmak veritabanını yoracağı için bu yöntem kullanılır. Debezium gibi bir araç, PostgreSQL'in log dosyasını (WAL - Write-Ahead Log) dinler. `Outbox` tablosuna bir `INSERT` yapıldığını milisaniyesinde yakalar ve veritabanına hiçbir ek yük bindirmeden bu veriyi Kafka'ya taşır.

### 3. Adım: Sistemin Trade-Off'u (Idempotency Zorunluluğu)
Outbox Pattern harika bir çözüm olsa da beraberinde yönetilmesi gereken bir mimari zorunluluk getirir. 

Message Relay mekanizması mesajı Kafka'ya başarıyla iletmiş, ancak tam o anda network koptuğu veya sunucu çöktüğü için veritabanındaki Outbox kaydını silememiş (veya durumunu güncelleyememiş) olabilir. Sistem yeniden ayağa kalktığında, silinmemiş bu mesajı okuyup **Kafka'ya ikinci kez gönderecektir.**

Bu nedenle Outbox Pattern "Exactly-Once" (Tam olarak bir kere) garanti vermez, **"At-Least-Once" (En az bir kere)** teslimat garantisi verir.

Bunu çözmek için mesajı okuyan (Consume eden) diğer servislerin **Idempotent** olması zorunludur. Yani Servis B veya Servis C, aynı `BakiyeGuncellendiEvent` mesajını birden fazla kez alsa bile, mesajın içindeki benzersiz `EventId`'yi kontrol etmeli (kendi veritabanına bu id daha önce işlendi mi diye bakmalı) ve işlemi sadece bir kere gerçekleştirmelidir.
