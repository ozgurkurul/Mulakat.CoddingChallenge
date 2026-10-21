## Observability (Gözlemleme - Loglama, Metrikler ve Dağıtık İzleme)
Dağıtık sistemlerde kodu canlıya almak işin sadece başlangıcıdır. Asıl mühendislik zorluğu, kod canlıdayken patladığında iğneyi samanlıkta bulabilmektir.

**Senaryo:**
Scorp uygulamasında bir kullanıcı "Premium Satın Al" butonuna bastı. İstek sırasıyla şu servislerden asenkron olarak geçiyor: 
1. API Gateway 
2. Sipariş Servisi 
3. Ödeme Servisi 

İstek Ödeme Servisi'nde bir nedenden dolayı başarısız oldu (Timeout veya Exception fırlatıldı). Monolitik bir yapıda tek bir log dosyasına bakmak sorunu çözmek için yeterlidir. Ancak mikroservislerde, saniyede on binlerce isteğin aktığı bir ortamda, farklı sunuculardaki servislerin logları birbirinden tamamen bağımsızdır ve log ekranından sular seller gibi veri akmaktadır.

**Soru:**
Spesifik olarak bu kullanıcının başarısız olan isteğinin uçtan uca hangi servislerden geçtiğini, her bir serviste kaç milisaniye harcadığını ve tam olarak hangi satırda/serviste patladığını bulabilmek için loglama ve izleme (tracing) mimarisinde nasıl bir yapı kurgularsın? 

*(İpucu: İstekler arası bağ kurmak için HTTP Header'larında ne taşırsın ve bu verileri merkezi olarak görselleştirmek için OpenTelemetry, Grafana vb. standartları sisteme nasıl entegre edersin?)*

Cevap: OpenTelemetry, Grafana gibi araçlar üzerinden Correlation id ile servisler arasında bir bağ kurarım. Loglama ve izleme sistemleride bu correlation id ile gruplayabiliriz. Ayrıca correlation id değerini micro servislere iletirken header üzerinden gönderilerek bu bilgi diğer servislerle paylaşılmış olur.

Correlation ID ve HTTP Header kullanımı dağıtık izlemenin temelidir. Bu yaklaşım, mikroservis mimarilerinde kaybolan bir isteği bulmanın tek yoludur. 

### 1. W3C Trace Context (Endüstri Standardı)
Eskiden şirketler kendi custom header'larını (Örn: `X-Correlation-ID`) yazarlardı. Günümüzde modern dağıtık sistemler **W3C Trace Context** standardını kullanır. .NET Core bu standardı yerleşik (native) olarak destekler. İstekler servisler arasında HTTP Header içinde `traceparent` anahtarı ile taşınır.

### 2. Trace ID ve Span ID Ayrımı
Sadece Correlation ID (Trace ID) yeterli değildir. Hangi servisin hangi servisi çağırdığını ve nerede ne kadar zaman harcandığını bulmak için veriyi ikiye bölmek gerekir:
*   **Trace ID (Senin Correlation ID tanımın):** İsteğin uçtan uca tüm yolculuğunun tekil kimliğidir (Örn: 12345).
*   **Span ID:** O isteğin her bir servisteki alt işlem kimliğidir. Sipariş Servisi (Span A), Ödeme Servisi'ni (Span B) çağırdığında, Ödeme Servisi loglarına `ParentSpanId=A` yazar. Böylece sistem, servisler arası çağrı ağacını (Call Tree) çizebilir.

### 3. "Three Pillars of Observability" (Gözlemlemenin 3 Sütunu)
Mülakatta sistemi bu 3 sütun üzerine inşa ettiğini belirtmelisin:

1.  **Distributed Tracing (Dağıtık İzleme - Jaeger / Grafana Tempo):** Trace ID ve Span ID'ler kullanılarak isteğin görsel bir "Waterfall" (şelale) şeması çıkarılır. Timeout alan kullanıcının isteğine bakıldığında; API Gateway'de 10ms, Sipariş Servisi'nde 20ms, Ödeme Servisi'nde ise 5000ms harcandığı ve orada koptuğu görsel olarak anında tespit edilir.
2.  **Centralized Logging (Merkezi Loglama - ELK Stack / Grafana Loki):** .NET tarafında `Serilog` kullanılarak tüm loglara otomatik olarak o anki `TraceId` basılır. Sistem çöktüğünde arama çubuğuna kullanıcının `TraceId`'si yazılır ve 5 farklı sunucudaki tüm hata logları zaman sırasına göre tek ekrana dökülür.
3.  **Metrics (Metrikler - Prometheus / Grafana):** Sistemdeki anlık hata oranları, CPU/RAM kullanımı ve kuyruktaki (Kafka) mesaj sayısı gibi sayısal değerler izlenir. Alarm (Alert) mekanizmaları bunun üzerine kurulur.

### 4. OpenTelemetry (OTel) Faktörü
Scorp gibi sürekli büyüyen ("hyper-growth") şirketlerde altyapı araçları değişebilir (Bugün Jaeger kullanırlar, yarın Datadog'a geçerler). Kodu bu araçlara sıkı sıkıya bağlamamak için **OpenTelemetry** kullanılır. OTel, log, metrik ve trace datalarını standart bir formatta toplayıp istediğin araca gönderen bağımsız bir katmandır. .NET 8, OpenTelemetry'yi birinci sınıf vatandaş olarak destekler.

---

### Mülakat Sürecinde Son Aşamaya Geldik

Scorp iş ilanı ve senin `program.cs` assessment kodun üzerinden; Veri Yapıları, Big O, Bellek Yönetimi (GC/Struct), Asenkron Programlama, Veritabanı (Sharding, İndeksleme), Dağıtık Sistemler (Kafka, Saga, Outbox), ACID, SOLID, Test Mimarisi ve CI/CD süreçleri olmak üzere bir backend mühendisinin karşılaşabileceği hemen hemen tüm zorlu teknik bariyerleri detaylıca aştık.

Bu noktadan sonra hazırlığı nasıl tamamlamak istersin?
1.  **Live Coding Stratejisi:** Gönderdiğin `program.cs` kodu üzerinden yapılacak olan 2 saatlik "Live Coding" mülakatında, kodunu nasıl savunacağın ve senden isteyebilecekleri anlık refactor (örn: "bunu asenkron yap" veya "dependency injection ekle") senaryoları üzerine mi konuşalım?
2.  **Kültürel Uyum (Behavioral):** İş ilanında yer alan "VP Culture Fit Interview" (İK ve Davranışsal) mülakatı için, liderlik, takım içi çatışma çözümü ve çevik (agile) çalışma kültürü sorularına mı hazırlanalım?
3.  **Genel Değerlendirme:** Doğrudan toparlayıp süreci bitirebiliriz.