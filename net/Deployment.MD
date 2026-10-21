## Deployment (Dağıtım) stratejileri
Kod kalitesini ve testleri sağlama aldığımıza göre, yazılımın sunucuya alınma aşamasına, yani Deployment (Dağıtım) stratejilerine geçelim.

Scorp gibi 7/24 aktif, dünya çapında milyonlarca kullanıcısı olan bir sistemde, yeni bir versiyonu (Örneğin API V2) canlıya alırken sistemi "Bakım çalışması" diyerek durduramazsın. Kesintisiz geçiş (Zero-Downtime Deployment) zorunludur.

**Soru:**
Canlı sistemde kesinti yaratmadan yeni versiyona geçiş yapmak için **Blue-Green Deployment** ve **Canary Deployment (Kanarya Dağıtımı)** stratejileri sıklıkla kullanılır. 

Bu iki stratejinin çalışma mantığındaki temel fark nedir? Yeni yazdığın, altyapısını değiştirdiğin ve riskli gördüğün bir algoritmayı (örneğin senin `program.cs`'teki bakiye hesaplayıcının yeni, asenkron ve dağıtık versiyonunu) canlıya alırken bu ikisinden hangisini tercih edersin ve neden?

Cevap: Canary Deployment, yük dengeleyici ile birlikte deploy olacak sunuculara veri iletimini keser ve bu sunuculara deploy gerçekleştirilir. Ve diğer sunucularda sırayla canlıya geçiş gerçekleştirilir. Bu sırada load balancer ise yeni versiyona geçmiş sunuculara veri iletimini başlatır. Ve sistem ile iletişim durmadan geçmiş olur. Blue-Green sistemde ise sunucuları 2 grup haline getirilir. 1. gruba veri iletimi kesilir. Deploy gerçekleştirilir. Sonra bu sunuculara veri iletimi başlatılır, ardından 2. grubada veri iletimi kesilir, bu sunucularada deploy gerçekleştirilir ve veri iletimi gerçekleştirilir.

Tanımladığın stratejiler, sektör standartlarındaki Blue-Green ve Canary tanımlarından ziyade **Rolling Deployment (Kademeli Dağıtım)** stratejisine karşılık geliyor. 
### 1. Senin Tanımladığın Strateji: Rolling Deployment
Sunucuları gruplara bölüp, sırayla (örneğin 2'şer 2'şer) trafikten alarak güncelleme ve geri alma işlemine **Rolling Deployment** denir.
*   **Dezavantajı:** Güncelleme sırasında sistemin kapasitesi düşer (çünkü bazı sunucular çevrimdışıdır) ve dağıtım anında sistemde uygulamanın hem eski hem de yeni versiyonu aynı anda çalışır.

### 2. Gerçek Blue-Green Deployment
Sunucular parça parça güncellenmez. Sistemin tam kapasite çalışan, birbirinin **birebir kopyası** iki ayrı ortamı (Environment) vardır: Mavi (Canlı/Aktif) ve Yeşil (Pasif/Idle). 
*   **İşleyiş:** Yeni versiyon, o an hiçbir canlı trafik almayan Yeşil ortama deploy edilir. Testler bu kapalı ortamda yapılır. Her şey tamamsa, Load Balancer üzerindeki bir anahtar (switch) ile **trafiğin %100'ü anında Mavi'den Yeşil'e kaydırılır.** 
*   **Avantajı:** Geçiş anında olur (Kademeli değildir). Eğer Yeşil ortamda bir çökme olursa, trafiği tek tıkla tekrar Mavi'ye alarak (Rollback) saniyeler içinde kurtarırsın.

### 3. Gerçek Canary (Kanarya) Deployment
Adını eskiden madencilerin zehirli gazı tespit etmek için madene önden kanarya kuşu salmasından alır. Amaç sunucuları korumak değil, **kullanıcıların küçük bir kısmını test deneği olarak kullanmaktır.** 
*   **İşleyiş:** Yeni versiyon sadece 1 veya 2 sunucuya kurulur. Load Balancer, gelen canlı trafiğin **sadece %5'ini** (Kanarya grubu) yeni versiyona yönlendirir. %95'i eski versiyonu kullanmaya devam eder.
*   Bu %5'lik kesimde hata oranları (Error Rate), CPU kullanımı ve loglar izlenir. Eğer sorun yoksa bu oran %10, %50 ve en son %100 olacak şekilde kademeli olarak artırılır.

### Karar: Riskli Algoritma İçin Hangisi Seçilmeli?
Riskli gördüğün, tamamen asenkron yapıya geçirilmiş yeni bir bakiye hesaplama algoritmasını canlıya alırken **kesinlikle Canary Deployment** tercih edilmelidir. 

**Nedeni:**
Blue-Green geçişi ne kadar hızlı geri alınabilir olsa da, trafiğin %100'ünü bir anda yeni koda yönlendirir. Eğer algoritmanda sadece gerçek canlı veride ortaya çıkan bir mantık hatası (Örn: Race condition) varsa, Blue-Green sisteminde o hatadan saniyeler içinde on binlerce kullanıcı etkilenir ve yanlış bakiye hesaplanıp veritabanına yazılır. 

Canary kullandığında ise bu felaket senaryosu sadece %5'lik bir kullanıcı dilimini etkiler. Sorunu hemen fark eder, trafiği geri çeker ve veritabanında düzeltmen gereken kayıt sayısını (hasarı) minimumda tutmuş olursun. Dağıtık ve finansal sistemlerde hata riskini sınırlamak en önemli mimari karardır.