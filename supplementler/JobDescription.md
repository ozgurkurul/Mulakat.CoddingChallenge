İş ilanı hakkında
Biz KimizSupplementler.com, 2012 yılında kurulan Dikey Vitamin A.Ş.’nin sporcu gıdaları ve takviyeleri alanında faaliyet gösteren öncü ve lider markasıdır. Dikey Vitamin, Supplementler.com ile birlikte spor, sağlıklı yaşam ve beslenme kategorilerinde üç e-ticaret sitesinin operasyonunu sürdürmektedir.Vitaminler.com: Vitamin, mineral ve sağlıklı atıştırmalık ürünleriFitmoda.com: Spor ekipmanları, aksesuarları ve tekstil ürünleriDikey Vitamin, sektörün önde gelen global markalarının yanı sıra yerel üretimle geliştirdiği kendi markalarını da tüketicilerle buluşturmaktadır.Teknoloji Operasyonları ekibimize %100 uzaktan çalışma modeliyle çalışacak Mid Level Backend Developer (.Net) arıyoruz.
Teknik YolculuğumuzUzun yıllardır üretimde olan .Net FW tabanlı platformumuzu, sistemi canlıda çalışır tutarak modüler monolith mimariye taşıyoruz. Hedefimiz net: bounded context'leri bugünden servis sınırı olacak şekilde tasarlamak ve olgunlaşan modülleri mikroservislere ayırmak. İlk mikroservislerimiz şimdiden canlıda.Bu dönüşümde kullandıklarımız:
Domain-Driven Design — aggregate, value object, domain event, bounded context; kitaptan değil, üretimde
Clean Architecture — katman sınırları mimari testlerle (architecture tests) otomatik korunuyor
Event-driven mimari — domain event'ler ve dış servislerle adapter tabanlı entegrasyon
CQRS tarzı use case yapısı — MediatR, FluentValidation
SQL Server + Entity Framework, Elasticsearch, Hangfire
Yani burada hem yeşil alan (yeni modüller, yeni servisler) hem kahverengi alan (legacy dönüşümü) mühendisliği bir arada: ikisini de seven biriyseniz doğru yerdesiniz.
Neler Yapacaksın
Legacy proje üzerindeki iş akışlarını analiz edip modüler monolith'e kademeli olarak taşınması sürecinde aktif rol alacaksın (strangler fig yaklaşımı)
İş kurallarını domain modeline doğru yansıtarak yeni özellikleri DDD ve Clean Architecture prensipleriyle, modül sınırlarını gözeterek geliştireceksin
Modüllerin bağımsız deploy edilebilir servislere dönüşme sürecinde tasarım kararlarına katkı sağlayacaksın
ERP, pazaryeri, ödeme ve arama (Elasticsearch) entegrasyonlarını geliştirecek ve iyileştireceksin
Agentic coding araçlarını geliştirme süreçlerinde etkin biçimde kullanacak, ekip içinde bu pratiklerin geliştirilmesine katkı sağlayacaksın
Code review süreçlerine aktif olarak katılacak, mimari standartların korunmasına katkı sağlayacak, junior geliştiricilerle bilgi paylaşımında bulunacaksın
Yüksek trafik altında performans ve ölçeklenebilirlik problemlerinin çözümünde ekiple birlikte çalışacaksın

Aranan Nitelikler
Backend geliştirmede 3+ yıl deneyim; C#/.NET ekosisteminde güçlü teknik bilgi birikimi (.NET Framework ve/veya modern .NET deneyimi)
DDD kavramlarını (aggregate, value object, domain event, bounded context) iyi derecede bilmek ve gerçek projelerde uygulamış olmak; üretim ortamı deneyimi tercih sebebidir
Clean Architecture / Ports & Adapters prensiplerine dair bilgi ve deneyim; SOLID prensiplerinin ezbere değil gerekçeleriyle bilinmesi
Legacy kod tabanında refactoring deneyimi ve kademeli dönüşüm süreçlerinde yer alma isteği
SQL Server ve ORM (Entity Framework vb.) deneyimi; sorgu performansı bilinci
Event-driven mimariler ve asenkron iletişim kalıpları hakkında bilgi
Test yazma kültürü (xUnit vb.) ve test edilebilir tasarım alışkanlığı
Agentic coding araçlarıyla (Claude Code, Cursor, GitHub Copilot vb.) günlük geliştirme deneyimi; üretilen kodu eleştirel bakışla değerlendirip testlerle doğrulayabilme
Teknik kararları yazılı ve sözlü olarak net ifade edebilme; code review süreçlerine yapıcı katılım

Bunlar Varsa Harika Olur
E-ticaret domain deneyimi (sepet, kampanya, ödeme, sipariş, stok akışları)
NopCommerce veya benzeri bir e-ticaret platformu deneyimi
Monolith'ten mikroservise geçiş sürecinde yer almış olmak
CQRS / MediatR deneyimi
Elasticsearch deneyimi
Mesaj kuyruğu sistemleri (RabbitMQ, Kafka vb.)
Hangfire veya benzeri background job altyapıları
ERP / pazaryeri entegrasyon deneyimi
Agentic workflow kurgulama deneyimi (MCP, özel agent/prompt tasarımı, AI destekli code review)

Mühendislik Kültürümüz
Mimari sınırlar sözde kalmaz; architecture test'lerle CI'da otomatik doğrulanır
Teknik borç bilinçli alınır, kodda açıkça işaretlenir ve çıkış koşuluyla takip edilir
Yazılı convention'larımız var: exception ve mesaj kodları, use case versiyonlama, modül sınırı kuralları
Yeni geliştirmeleri hızlıca deneyebileceğin izole bir Playground ortamı sunuyoruz
Code review zorunludur ve standartlar herkese eşit uygulanır
Agentic coding araçlarını geliştirme süreçlerimizde aktif olarak kullanıyor, deneyimlerimizi ekip içinde paylaşıyoruz