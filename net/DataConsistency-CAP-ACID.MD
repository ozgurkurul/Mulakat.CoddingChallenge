## Veri tutarlılığı (Data Consistency), CAP ve ACID
Mikroservis mimarilerinde uygulama sunucularını (stateless oldukları için) yatayda ölçeklemek (scale-out) kolaydır; sadece yeni instance'lar ayağa kaldırırsın. Ancak durum veritabanına geldiğinde (stateful), işler tamamen değişir. Milyonlarca kullanıcısı olan bir sistemde tek bir veritabanı (Monolith DB), CPU, RAM veya Disk I/O sınırlarına hızla çarpar.

Bu darboğazı aşmak için kullanılan veri dağıtım stratejilerini ve temel teoriyi netleştirelim.

### 1. Partitioning (Bölümlendirme) vs. Sharding (Parçalama)
Mülakatlarda en çok karıştırılan iki kavramdır. İkisi de veriyi böler ancak mimari yapıları farklıdır.

*   **Partitioning:** Veriyi **aynı veritabanı sunucusu (instance)** içinde mantıksal veya fiziksel olarak daha küçük parçalara ayırmaktır. Amacı sorgu performansını artırmaktır.
    *   *Horizontal Partitioning:* Tablodaki satırları bölmek (Örn: 2025 verileri bir partition'da, 2026 verileri diğerinde).
    *   *Vertical Partitioning:* Sık kullanılmayan, çok yer kaplayan kolonları (Örn: `ProfilFotografiByte` kolonu) farklı bir tabloya ayırmak.
*   **Sharding (Shared-Nothing Architecture):** Veriyi birden fazla **bağımsız fiziksel sunucuya (node)** dağıtmaktır. Amaç donanım limitlerini aşmaktır (Scale-out). Her shard kendi başına bağımsız bir veritabanıdır.
    *   *Zorlukları:* Farklı shard'lar arasında `JOIN` işlemi yapılamaz. Transaction yönetimi (Distributed Transactions / 2PC) çok maliyetlidir.

### 2. Sharding Stratejileri (Veriyi Nasıl Dağıtacağız?)
*   **Range Based (Aralık Tabanlı):** ID'si 1-1.000.000 arası olanlar Node A'ya, 1.000.001-2.000.000 arası olanlar Node B'ye gider.
    *   *Sorun:* Yeni kayıtların hepsi her zaman en son Node'a gideceği için "Hotspot" (darboğaz) oluşur.
*   **Hash Based (Özet Tabanlı):** Gelen verinin bir anahtarı (örn: `UserId`) hash fonksiyonundan geçirilir ve toplam sunucu sayısına modu alınır (`Hash(UserId) % NodeCount`).
    *   *Avantaj:* Veri sunuculara çok eşit dağılır.
    *   *Sorun:* Sisteme yeni bir sunucu eklediğinde mod formülü değişeceği için (re-sharding) tüm verilerin yerini değiştirmen gerekir. (Bu sorunu çözmek için sistem dizaynında **Consistent Hashing** kullanılır).
*   **Consistent Hashing (Tutarlı Karma):** dağıtık sistemlerde (örneğin önbellekleme sistemleri veya veritabanı kümeleri) verileri sunucular arasında dengeli bir şekilde dağıtmak için kullanılan bir algoritmadır. Geleneksel karma yöntemlerinin aksine, sisteme yeni bir sunucu eklendiğinde veya bir sunucu çıkarıldığında verilerin büyük bir kısmının yer değiştirmesini engeller.
    *   *Temel Özellikleri ve Çalışma Mantığı:*
        * Hash Halkası (Hash Ring): Consistent hashing'de, hem sunucular hem de veriler (anahtarlar) hayali bir daire veya halka üzerine yerleştirilir.
        * Eşleştirme: Bir verinin hangi sunucuda saklanacağı, halka üzerinde o verinin konumundan saat yönünde ilerleyerek karşılaşılan ilk sunucuya bakılarak belirlenir.
        * Minimum Yer Değiştirme: Geleneksel $n \pmod m$ yönteminde sunucu sayısı ($m$) değiştiğinde neredeyse tüm anahtarların yeri değişirken, tutarlı karmada sadece eklenen veya çıkarılan sunucuya komşu olan veriler etkilenir.
        * Sanal Düğümler (Virtual Nodes): Verilerin sunucular arasında daha homojen dağılmasını sağlamak için her fiziksel sunucu, halkanın farklı noktalarında birden fazla "sanal düğüm" olarak temsil edilebilir.
    *   *Neden Kullanılır?*
        1. Yatay Ölçeklenebilirlik: Sisteme kolayca yeni sunucular ekleyerek kapasite artırımı yapmanıza olanak tanır.
        2. Yüksek Performans: Dinamik olarak değişen sistemlerde (bir sunucunun çökmesi gibi) verinin yeniden dağıtılması sırasında oluşan ağ trafiğini ve işlem yükünü minimize eder.
        3. Önbellek Verimliliği: Özellikle Redis veya Memcached gibi dağıtık önbellek sistemlerinde, sunucu değişikliklerinde "cache miss" (önbellekte bulamama) oranını çok düşük tutar.
*   **Directory Based (Dizin Tabanlı):** Hangi verinin hangi sunucuda olduğunu tutan ayrı bir "Lookup/Router" veritabanı kullanılır.

### 3. CAP Teoremi
Dağıtık sistemlerin anayasasıdır. Bir ağ üzerinde çalışan dağıtık bir sistem, aşağıdaki üç garantiden **aynı anda sadece ikisini** sağlayabilir:

1.  **Consistency (Tutarlılık - C):** Sisteme yazılan bir veri, saniyesinde tüm node'lara kopyalanır. Hangi node'a okuma isteği atarsan at, sana her zaman en güncel veriyi (veya hata) döner.
2.  **Availability (Erişilebilirlik - A):** Sisteme gelen her okuma ve yazma isteği mutlaka bir cevap (hata olmayan) alır. Ancak dönen cevap en güncel veri olmayabilir.
3.  **Partition Tolerance (Bölünme Toleransı - P):** Sunucular (node'lar) arasındaki ağ bağlantısı kopsa veya paketler kaybolsa bile sistem bütünüyle çalışmaya devam eder.

**Gerçek Dünya Kuralı:** Ağ bağlantıları her zaman kopabileceği için dağıtık sistemlerde **P (Partition Tolerance) zorunludur, vazgeçilemez.** Bu nedenle mülakatta sana "Sistemin CP mi, yoksa AP mi olacak?" diye sorulur.
*   **CP Sistemler (Tutarlı ama Erişilemeyebilir):** Finansal işlemler, bakiye düşme, ödeme sistemleri. (Örn: Banka hesabı. Ağ kopuksa işlem reddedilir, eski bakiye gösterilmez). *Araçlar: MongoDB, PostgreSQL, Redis (Cluster).*
*   **AP Sistemler (Erişilebilir ama Tutarsız Olabilir - Eventual Consistency):** Sosyal medya akışları, beğeni sayıları, telemetri verileri. (Örn: Birinin fotoğrafını beğenirsin, o an ağda kopukluk olsa bile sistem o beğeniyi kabul eder, diğer kullanıcılara 3 saniye sonra yansıması sorun değildir). *Araçlar: Cassandra, DynamoDB, CouchDB.*

---

## Mülakat Senaryosu: "Likes" Mikroservisi

Scorp'un teknik ekibi senin önüne şu problemi koyuyor:

"Şu anda canlı yayınlardaki anlık 'Beğeni (Like)' ve 'Yorum' işlemlerini tek bir PostgreSQL veritabanında tutuyoruz. Ancak anlık izleyici sayısı 1 milyonu geçen yayınlarda, saniyede yüz binlerce insert isteği geliyor ve veritabanı kilitleniyor. Bu servisi Sharding uygulayarak dağıtık bir yapıya geçirmeye karar verdik."

**Soru:**
1. Bu sistem için Hangi Sharding stratejisini (Hash, Range vb.) seçersin ve "Shard Key" (dağıtım anahtarı) olarak neyi kullanırsın? (Örneğin `UserId` mi, `StreamId` mi, `LikeId` mi?) Nedenini trade-off'larıyla açıklar mısın?
2. CAP teoremine göre bu "Like ve Yorum" mikroservisi CP mi olmalıdır, AP mi? Neden?

### Öneri Çevap: 
Soru 1: StreamId kullanarak hash yapısı kurarım (consistent hashing). çünkü yayınlar en son üretilen içerikler olmaktadır. UserId ve LikeId belirli bir zamanda oluşturulmuş ve üzerinden çok uzun zaman geçmiş olabilir. StreamId'ye göre sharding dengeli bir dağılmayı sağlayabilir sunuculara. Yine de bir canlı yayın içeriğinin 1 milyonu geçen sırada araya redis counter gibi yapı kurgulamak like süreçleri için faydalı olabilir. 
Soru 2: AP olmalıdır. Çünkü yorum ve like bilgisi istatistiki bilgidir ve en son net bilginin realtime'da %100 doğru olması zorunlu değildir, yaklaşık doğru olması kabul edilir.

### 1. Sharding Stratejisi Analizi: Neden `StreamId`?
Cevabın temelde çok doğru bir mühendislik içgörüsüne dayanıyor: İzole edilmiş, yüksek yoğunluklu (hotspot) işlemleri bir arada tutmak.

*   **`UserId` Neden Kötü Bir Fikirdir?:** Eğer Shard Key olarak `UserId` seçilirse, aynı canlı yayındaki kullanıcılar binlerce farklı Shard'a dağılır. Bir yayındaki toplam beğeni sayısını (`COUNT(*)`) bulmak için tüm Shard'lara sorgu atıp (Scatter-Gather) sonuçları birleştirmen gerekir ki bu devasa bir performans kaybıdır.
*   **`LikeId` Neden Kötü Bir Fikirdir?:** Tamamen rastgele dağılır. Hangi yayının beğenisi nerede belli olmaz. Yine Scatter-Gather problemi yaşanır.
*   **`StreamId` Neden Doğrudur (Ama Bir Tehlikesi Vardır)?:** `StreamId` seçtiğinde, o yayına ait *tüm* beğeniler tek bir fiziksel sunucuya (Shard'a) gider. Bu, o yayın için okuma (Read) işlemlerini çok hızlandırır.
    *   **Tehlike (Hotspot):** Scorp'taki 10 milyon takipçili bir yayıncı yayına girdiğinde, saniyede 50.000 beğeni tek bir Shard'a hücum eder. Diğer Shard'lar boş yatarken, o Shard CPU ve Disk I/O limitlerine çarparak çökebilir. Buna **"Celebrity Problem" (Ünlü Problemi)** veya **Hot Shard** denir.

**Sistem Dizaynı Dokunuşu (Senin Redis Önerin):**
Mülakatta tam da bu noktada senin o *"araya Redis counter gibi yapı kurgulamak"* önerin kurtarıcı olur! 
Eğer `StreamId` üzerinden hashliyorsak, o devasa yükü veritabanına doğrudan indirmeyiz. Beğeniler önce Redis'teki bir In-Memory Counter'da (örneğin `INCR stream:123:likes`) toplanır. Arka plandaki asenkron bir worker (Event-Driven mimari), Redis'ten aldığı bu toplu sayıları (örneğin her 2 saniyede bir "+5000 beğeni" şeklinde) tek bir `UPDATE` veya `INSERT` sorgusuyla Shard'a yazar (Write-Behind Caching / Batching). Böylece hem Sharding yapmış hem de Hotspot sorununu çözmüş olursun.

### 2. CAP Teoremi Analizi: Neden AP?
Mükemmel. Yorum ve like bilgileri kesinlikle **AP (Availability and Partition Tolerance)** olmalıdır. Bu tür sistemlerde "Eventual Consistency" (Nihai Tutarlılık) kabul edilir. 

Scorp gibi bir uygulamada izleyici, tıkladığı "Beğen" butonunun takıldığını (Availability kaybı) veya hata verdiğini görmek istemez. Beğeninin sayaca 2 saniye geç yansıması (Consistency kaybı) kullanıcı deneyimini bozmaz ama butonun çalışmaması bozar.

---
### ACID

ACID, ilişkisel veritabanı (RDBMS - örneğin PostgreSQL) transaction'larının (işlemlerinin) güvenilir bir şekilde işlenmesini garanti eden dört temel özelliğin kısaltmasıdır. Teknik mülakatlarda bu kavramlar, özellikle yüksek eşzamanlılığın (concurrency) olduğu senaryolar üzerinden sorulur.

### 1. Atomicity (Bölünmezlik / Ya Hep Ya Hiç)
Bir transaction içindeki tüm işlemlerin tek bir bütün olarak ele alınmasıdır. İşlemlerden biri bile hata verirse, tüm süreç iptal edilir (Rollback) ve veritabanı işlem öncesi haline döner.
*   **Pratik Karşılığı:** Az önce konuştuğumuz Outbox Pattern'ın temelidir. Bakiye güncelleme ve Outbox tablosuna mesaj yazma işlemi aynı `BEGIN...COMMIT` bloğundadır. İkisi birden gerçekleşmek zorundadır.

### 2. Consistency (Tutarlılık)
Bir transaction tamamlandığında (başarılı veya başarısız), veritabanının bir geçerli durumdan başka bir geçerli duruma geçmesini garanti eder.
*   **Pratik Karşılığı:** Veritabanına önceden tanımlanmış kuralların (Constraints, Foreign Keys, Unique Keys, Not Null vb.) ihlal edilmemesidir. Örneğin, bakiyenin eksiye düşmemesi için veritabanında bir `CHECK (Amount >= 0)` kısıtı varsa, bu kısıtı bozan bir transaction anında reddedilir.
*   *Önemli Not:* CAP teoremindeki "Consistency" (tüm node'ların aynı veriyi görmesi) ile ACID'deki "Consistency" (veri bütünlüğü kuralları) aynı şey **değildir**. Mülakatta bu ayrımı belirtmek önemlidir.

### 3. Isolation (İzolasyon / Yalıtım) - *Mülakatların En Eleyici Kısmı*
Aynı anda çalışan birden fazla transaction'ın (eşzamanlılık) birbirlerini nasıl ve ne seviyede göreceğini belirler. Kötü izole edilmiş sistemlerde veriler birbirine girer.
*   **İzolasyon Seviyeleri (Hızlıdan Güvenliye):**
    1.  **Read Uncommitted:** En hızlısıdır ama bir transaction'ın henüz commit etmediği (belki de rollback yapacağı) veriyi diğeri okuyabilir. Buna **Dirty Read** denir.
    2.  **Read Committed:** Sadece commit edilmiş veriler okunur. (PostgreSQL ve SQL Server'ın varsayılan seviyesidir).
    3.  **Repeatable Read:** Bir transaction içindeyken bir kaydı iki kere okursan, araya başka biri girip o kaydı değiştirse bile sen hep ilk okuduğun değeri görürsün.
    4.  **Serializable:** En güvenli ama en yavaş seviyedir. Tüm transaction'lar sanki arka arkaya (sırayla) çalışıyormuş gibi kilitlenir. Eşzamanlılık (Concurrency) tamamen ortadan kalkar.

### 4. Durability (Kalıcılık / Dayanıklılık)
Bir transaction `COMMIT` ile onaylandıktan sonra, o milisaniyede veritabanı sunucusunun fişi çekilse veya sistem çökse dahi o verinin kaybolmayacağının garantisidir.
*   **Pratik Karşılığı:** Veritabanları veriyi önce RAM'de (Buffer) tutar, diske yazmak maliyetlidir. Ancak transaction commit edildiğinde veri doğrudan **WAL (Write-Ahead Log)** adı verilen sıralı log dosyasına fiziksel olarak yazılır. Sistem çökerse, yeniden başlarken WAL okunarak veriler kurtarılır.

---

### Mülakat Senaryosu: "Lost Update" Problemi

ACID ve özellikle İzolasyon (Isolation) konusunu tam olarak oturtmak için şu gerçek dünya problemini çözelim:

Scorp uygulamasında bir yayıncının 100 USD bakiyesi var. 
*   **Kullanıcı A**, yayıncıya 10 USD gönderiyor (Thread 1).
*   Tam o milisaniyede **Kullanıcı B** de yayıncıya 50 USD gönderiyor (Thread 2).

Veritabanında şu senaryo yaşanıyor:
1.  Thread 1 bakiyeyi okur (100).
2.  Thread 2 bakiyeyi okur (100).
3.  Thread 1 bakiyeye 10 ekler ve 110 olarak günceller (Update Amount = 110).
4.  Thread 2 kendi okuduğu 100'ün üzerine 50 ekler ve 150 olarak günceller (Update Amount = 150).

Sonuç: Bakiye 160 USD olması gerekirken, veritabanında 150 USD yazar. 10 USD kaybolmuştur (Lost Update).

**Soru:**
Bu kritik "Lost Update" problemini PostgreSQL gibi bir RDBMS üzerinde çözmek için kodlama veya mimari düzeyde hangi stratejiyi kurgularsın? (İpucu: Locking mekanizmaları - Pessimistic veya Optimistic).

Cevap: Row versioning yapısı kullanırım ve bu optimistic locking mekanizması olur. Eğer transaction sırasında çektiğim versiyon değeri, commit sırasında değişmiş ise rollback yapılır. pesimistic mekanizma olarak tabloda satırı kilitleriz, güncellemeyi yaparız sonra kilidi açarız. eşzamanlılık gerektirdiğinde diğer kullanıcılar kilidin serbest kalmasını beklerler. 

Lost Update problemini çözmek için uygulanabilecek her iki endüstri standardı yöntemi de eksiksiz özetledin. Mülakatçı bu cevaptan, veri bütünlüğünü sağlarken sistemin performansını da nasıl yöneteceğini bildiğini çıkaracaktır.

Senin verdiğin cevapları daha teknik ve yapısal hale getirelim:

### 1. Optimistic Locking (İyimser Kilitleme - Senin Row Versioning Çözümün)
Bu yöntem, "Nasıl olsa çakışma (conflict) çok nadir olur" varsayımına dayanır. Performans odaklıdır.
*   **İşleyiş:** Tabloya `Version` veya `RowVersion` (integer) kolonu eklenir. 
    1. Kullanıcı A ve B bakiyeyi okurken versiyonu da okur (Örn: `Version = 1`).
    2. Kullanıcı A işlemi önce bitirir, bakiyeyi güncellerken versiyonu kontrol eder ve artırır:
       `UPDATE Balances SET Amount = 110, Version = 2 WHERE Id = 123 AND Version = 1`. (Başarılı)
    3. Kullanıcı B işlemi bitirip kendi güncellemesini yolladığında:
       `UPDATE Balances SET Amount = 150, Version = 2 WHERE Id = 123 AND Version = 1`. (Başarısız).
*   **Sonuç:** Veritabanı "0 satır güncellendi" döner. Entity Framework Core gibi ORM'ler bunu `DbUpdateConcurrencyException` olarak fırlatır. Geliştirici bu hatayı yakalar (catch) ve işlemi yeniden dener (Retry) ya da kullanıcıya hata döner.
*   **Nerede Kullanılır?:** Okuma işlemlerinin (Read) çok, aynı veriyi aynı anda güncelleme işlemlerinin (Write) az olduğu, yüksek performans gerektiren sistemlerde (Örn: Kullanıcı profili güncelleme).

### 2. Pessimistic Locking (Kötümser Kilitleme - Senin Satır Kilidi Çözümün)
Bu yöntem, "Çakışma kesin olacak, işimi şansa bırakamam" varsayımına dayanır. Veri güvenliği odaklıdır.
*   **İşleyiş:** Veritabanı seviyesinde fiziksel kilit koyulur.
    1. Kullanıcı A işlemi başlattığında satırı kilitler: `SELECT Amount FROM Balances WHERE Id = 123 FOR UPDATE`. 
    2. Kullanıcı B aynı anda işlem yapmak isterse, Kullanıcı A'nın işlemi bitene (Commit/Rollback) kadar veritabanı seviyesinde bekletilir (Bloklanır).
    3. Kullanıcı A işlemi bitirir, kilit kalkar. Kullanıcı B en güncel veriyi (110) okuyarak kendi işlemini yapar (160).
*   **Nerede Kullanılır?:** Aynı veriye aynı anda çok fazla yazma isteği gelen (High Contention) ve finansal doğruluğun kritik olduğu yerlerde (Örn: Bilet rezervasyonu, banka havalesi).