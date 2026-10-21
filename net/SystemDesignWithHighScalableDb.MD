### Sistem dizaynı ve yüksek ölçekli veritabanı yönetimi konularına geçelim.

Veritabanındaki yükü hafifletmek için Redis gibi bir in-memory cache sistemi kurguladığını düşünelim. Anlık olarak çok popüler olan bir yayıncının verisinin (örneğin 10 milyon takipçisi olan birinin profil bilgileri) Redis'teki cache süresi (TTL) dolduğu anda, aynı milisaniye içerisinde bu veriye erişmek isteyen 50.000 farklı kullanıcının isteği aynı anda veritabanına düşer. 

Sistemi anında çökertebilecek bu duruma sistem dizaynında **"Cache Stampede" (Önbellek İzdihamı)** denir. Bu problemi önlemek için mimaride nasıl bir strateji kurgularsın?


### Basit Bir Temel Çözüm Analizi
Kurguladığın yapıda iki kritik doğru nokta var:
1.  **Hybrid Cache (L1 Memory + L2 Redis):** Uygulama ile Redis arasındaki ağ (network) gecikmesini ortadan kaldırır. En sıcak veriler uygulamanın kendi RAM'inde tutulur.
2.  **Jitter (Rassal Zaman Ekleme):** TTL (Time to Live) süresine +1 ila +5 dakika random değer ekleme işlemine sistem dizaynında **Jittering** denir. Bu sayede uygulamanın farklı instance'larındaki L1 önbellekleri aynı milisaniyede değil, farklı zamanlarda düşer. Bu da yükü zamana yayarak izdihamı hafifletir.

### Cache Stampede (Önbellek İzdihamı) İçin Tam ve Kesin Çözümler

Jittering yükü dağıtsa da, 10 milyon takipçili bir yayıncının verisi L2'den (Redis) de düştüğü anda, o an gelen 50.000 isteğin veritabanına gitmesini tek başına engelleyemez. Yüksek ölçekli sistemlerde Cache Stampede'i kesin olarak çözmek için şu üç ana strateji uygulanır:

#### 1. Distributed Locking (Dağıtık Kilit Mekanizması - Mutex)
En yaygın ve en güvenli çözümdür. 
*   **İşleyiş:** Veri Redis'te bulunamadığında (Cache Miss), 50.000 isteğin veritabanına gitmesine izin verilmez. İsteği ilk yapan thread, Redis üzerinde bir kilit (Lock) oluşturur (örneğin `SETNX` veya Redlock algoritması ile). 
*   Diğer 49.999 istek kilitli olduğunu görür ve veritabanına gitmez; ya kilit açılana kadar beklerler (Thread.Sleep/Task.Delay ile kısa döngüler) ya da eski (stale) veriyi dönerler.
*   Kilidi alan ilk thread veritabanına gider, veriyi çeker, Redis'e yazar ve kilidi kaldırır. Böylece DB'ye 50.000 yerine sadece **1 adet** sorgu gider.

#### 2. Background Refresh (Arka Plan Yenilemesi)
Uygulama katmanı ile veritabanı arasına katı bir duvar örülür.
*   **İşleyiş:** API'ye gelen kullanıcı istekleri (HTTP Request) veri yoksa **asla** veritabanına gitmez, sadece Redis'ten okuma yapar. 
*   Sistemin arka planında çalışan ayrı bir servis (Worker Service, Cron Job), çok okunan bu popüler verileri TTL süresi dolmadan önce (örneğin her 3 dakikada bir) asenkron olarak veritabanından çeker ve Redis'i günceller.
*   Bu sayede verinin "süresinin dolması" ve anlık izdiham yaratması teknik olarak imkansız hale gelir.

#### 3. Probabilistic Early Expiration (Erken Yenileme / XFetch)
Locking mekanizmasının getirdiği bekletme (latency) maliyetinden kaçınmak için kullanılır.
*   **İşleyiş:** Verinin TTL süresinin dolmasına az bir zaman kala (örneğin son %5'lik dilimde) gelen bir isteğe anında Redis'teki mevcut (eski) veri döndürülür. Ancak aynı anda arka planda (Fire-and-forget) bir Task tetiklenerek veritabanından yeni veri istenir ve Redis güncellenir.
*   Kullanıcılar hiçbir zaman beklemez, veri henüz tam olarak ölmeden yenilenmiş olur.
