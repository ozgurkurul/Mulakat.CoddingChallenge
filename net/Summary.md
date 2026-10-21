# 🎯 ÖZET

## ⏱️ DAKIKA 1-3: Açılış Stratejisi ve Kim Olduğun

Mülakatta **ilk 60 saniye** kritiktir. Mülakatçı seninle ilgili çoğu kararını burada verir. Bu üç cümleyi ezberle, kendi sözcüklerinle söyle:

> *"10 yılı aşkın .NET deneyimi olan, yüksek ölçekli ve event-driven sistemlere odaklı bir Software Team Lead'im. En değerli tecrübelerim Yemeksepeti'nde quick-commerce mikroservislerini kurmak, Delivery Hero'da global entegrasyon altyapısı tasarlamak ve şu an Drivee'de günde 50 milyon telemetri verisini işleyen bir IoT ekosistemi yönetmek oldu. Unvanım Team Lead olsa da, günümün çoğunu kod yazarak ve mimari karar alarak geçiriyorum — kendimi 'inşaatçı' olarak görüyorum."*

**Senin 4 büyük kozun (kafanda her zaman bu sırayla tut):**

1. Monolit → Mikroservis dönüşüm liderliği (kaza bildirim hikayesi)
2. "You build it, you run it" — Docker, Traefik, GoCD ile kendi altyapın
3. Cross-platform ürün vizyonu (.NET + React + React Native)
4. Çevik iletişim, cross-functional team yönetimi

---

## ⏱️ DAKIKA 4-8: Big O ve Veri Yapıları — Hızlı Tazeleme

**Karmaşıklık tablosu (ezberden bilmelisin):**

| Big O | Anlamı | C# Karşılığı |
|-------|--------|--------------|
| **O(1)** | Sabit zaman | `Dictionary[key]`, `array[index]` |
| **O(log N)** | Logaritmik | Binary Search, B-Tree index |
| **O(N)** | Doğrusal | `foreach`, `List.Contains` |
| **O(N log N)** | Linearithmic | `OrderBy`, `Sort` |
| **O(N²)** | Karesel — TUZAK! | İç içe foreach |

**Mülakatın kurtarıcısı: Space-Time Trade-off**

> *"Zamanı hızlandırmak için RAM'den feda et."*

Klasik örnek: İki listede ortak elemanları bulmak. Liste.Contains = O(N²). HashSet'e atınca O(N). Mutabakat (reconciliation) sorusu gelirse direkt bu cevabı ver.

**Veri Yapısı Karar Tablosu:**

- Key-value, hızlı erişim → `Dictionary`
- Benzersiz, "var mı?" sorgusu → `HashSet`
- FIFO (kuyruk) → `Queue`
- LIFO (geri al) → `Stack`
- Sıralı, indeks ile erişim → `List`
- Thread-safe → `ConcurrentDictionary`, `ConcurrentBag`

**Masada söyle:** *"Veri yapısı seçimini her zaman erişim pattern'ine göre yaparım. List yerine Dictionary kullanmak çoğu zaman O(N)'i O(1)'e indirir, ama bunun bedeli ekstra hash hesaplaması ve memory."*

---

## ⏱️ DAKIKA 9-13: Memory Management — GC, Stack/Heap, Struct

**3 Cümlelik Anahtar:**

1. **Class** → Heap'te yaşar, GC takip eder, Gen0/1/2 evrimi
2. **Struct** → Stack'te yaşar, GC radarına girmez, scope bittiğinde silinir
3. **GC Generations** → Genç ölür, yaşlı kalır (Weak Generational Hypothesis)

**"Milyonlarca telemetri verisi için class mı struct mı?"** sorusu gelirse:

> *"Yüksek frekanslı veriler için struct'ı tercih ederim. List<SensorClass> kullansaydım GC'nin takip ettiği 1 milyon ayrı obje olurdu; List<SensorStruct> kullandığımda inline paketleme sayesinde sadece 1 obje var. Bu, Stop-the-World pause'larını dramatik düşürür. Ayrıca CPU cache locality artar — struct'lar bellekte ardışık olduğu için L1/L2 cache hit oranı yüksek."*

**Şişman struct trap sorusu:** Şişman struct'ı metoda parametre olarak geçirirsen byte-by-byte kopyalanır → performans düşer.

**Çözüm:** `in` keyword'ü (veya `ref readonly`). `ref` performansı çözer ama mutability bırakır; `in` immutable + reference geçişi sağlar. **`in` cevabını ver.**

---

## ⏱️ DAKIKA 14-19: Async/Await ve Thread Pool Starvation

**En kritik kavramlar:**

1. **Async/await = State Machine** — derleyici metodu `IAsyncStateMachine`'e dönüştürür, allocation maliyeti var
2. **`.Result` veya `.Wait()` = Thread Pool Starvation** — thread bloklanır, havuz tükenir, sistem cevap veremez
3. **Deadlock riski** — eski .NET Framework ve UI bağlamında `.Result` kullanırsan deadlock
4. **Çözüm: "Async all the way down"** — Controller'dan en alta kadar `Task` zincirini koru

**Lock + Async Kuralı (kritik):**

> *"`lock` bloğu içinde `await` kullanamazsın. Sebep: lock thread-affine'dır — alan thread bırakmalı. await'ten sonra continuation farklı thread'e düşebilir. Async senaryoda `SemaphoreSlim` kullanırım — `await semaphore.WaitAsync()` async-aware'dır."*

**`Task` vs `ValueTask`:**

> *"Task heap'te allocate edilen reference type. ValueTask struct, sonuç senkron varsa hiç allocation yapmaz. Hot path'te ValueTask'ı tercih ederim ama tek-kullanımlık tasarlanmış — birden fazla await edemezsin. Library kodu yazıyorsan ValueTask, application kodu yazıyorsan Task."*

---

## ⏱️ DAKIKA 20-26: SOLID + Strategy/Factory

**SOLID — Tek Cümlelik Tanımlar:**

- **S** (Single Responsibility): Sınıfın değişmek için bir nedeni olmalı
- **O** (Open/Closed): Genişletmeye açık, değiştirmeye kapalı
- **L** (Liskov): Alt sınıf, üst sınıfın yerine geçmeli (Penguen.Uç anti-örnek)
- **I** (Interface Segregation): Şişman arayüz yerine küçük spesifik arayüzler
- **D** (Dependency Inversion): Soyutlamalara bağımlı ol, somutlara değil

**Strategy vs Factory (KARIŞTIRILIR):**

> *"İkisi de if-else'i temizler ama niyetleri farklı. **Factory nesne yaratmakla (Creational), Strategy davranışla (Behavioral)** ilgilenir. Factory 'kim üretilecek' der, Strategy 'iş nasıl yapılacak' der. Gerçek hayatta birlikte çalışırlar — Factory doğru Strategy'yi yaratır. Modern .NET'te Dependency Injection container'ı zaten Factory görevini görür: `IEnumerable<IStrategy>` enjekte ederek runtime'da doğru stratejiyi resolve ediyoruz."*

**Senin coding challenge'da yaptığın iş tam olarak budur:** `ICurrencyHandler` (Strategy), `DefaultCurrencyService` (Registry/Factory rolü), DI ile bağlanır.

**YAGNI vurgusu (mülakatta gold):**

> *"Sırf havalı dursun diye Factory veya Strategy koymam. Over-engineering'den kaçınırım. Desenler ancak kodun gelecekteki değişim maliyeti yüksekse anlamlı."*

---

## ⏱️ DAKIKA 27-32: Veritabanı — Indexing ve CAP/ACID

### B-Tree Indexing

- Index = O(N) → O(log N) indirme
- **Clustered Index**: Fiziksel sıralama, tabloda 1 tane (genelde Primary Key)
- **Non-Clustered Index**: Pointer yapısı, birden fazla olabilir

**SARGable sorgular (TUZAK):**

```sql
-- ❌ Index'i bozar
WHERE YEAR(CreatedAt) = 2025

-- ✅ Index kullanır
WHERE CreatedAt >= '2025-01-01' AND CreatedAt < '2026-01-01'
```

**Composite Index Altın Kuralı:** En yüksek **Cardinality**'ye sahip kolonu başa koy (örn: Username > Country).

**Trade-off:** Index'ler okumayı hızlandırır ama yazmayı yavaşlatır (B-Tree güncellenmek zorunda). Write-heavy tablolarda minimum tut.

### CAP Teoremi

- **C** (Consistency), **A** (Availability), **P** (Partition Tolerance) — sadece 2 seçilebilir
- P zorunlu, gerçek soru: **CP mi AP mi?**
- **CP**: Banka, ödeme (PostgreSQL, MongoDB)
- **AP**: Sosyal medya, like/comment (Cassandra, DynamoDB)

### ACID

- **A**tomicity — Ya hep ya hiç (Outbox Pattern'ın temeli)
- **C**onsistency — Constraint'ler bozulmaz
- **I**solation — Concurrent transaction'ların birbirini ne kadar gördüğü
- **D**urability — Commit'ten sonra çökse bile veri kaybolmaz (WAL)

**Lost Update Çözümleri:**

- **Optimistic** (RowVersion): Düşük çakışma, performans önemli (Profil güncelleme)
- **Pessimistic** (`SELECT FOR UPDATE`): Yüksek çakışma, kritik (Banka havalesi)

---

## ⏱️ DAKIKA 33-39: Distributed Systems — Outbox, Saga, Sharding

### Sharding Stratejileri

- **Range-based**: Hotspot riski (yeni kayıtlar hep son shard'a)
- **Hash-based**: Eşit dağıtım, ama re-sharding zor
- **Consistent Hashing**: Sunucu eklemek/çıkarmak verilerin küçük kısmını etkiler. Redis Cluster'ın temeli.

### Like/Comment Sistemi Senaryosu (Hazır Cevap)

> *"StreamId üzerinden Consistent Hashing kullanırım — aynı yayının tüm beğenileri tek shard'da. Ama Hot Shard ('Celebrity Problem') için araya Redis counter koyarım: INCR stream:123:likes. Worker arka planda her 2 saniyede bir batch'leyerek shard'a yazar (Write-Behind Caching). CAP açısından AP olmalı — beğeninin 2 saniye geç yansıması kabul edilir, butonun çalışmaması kabul edilmez."*

### Outbox Pattern (Dual Write Problemi)

**Problem:** Veritabanı yazma + Kafka mesajı atomik olmaz.

**Çözüm:**
1. Aynı transaction'da Balance UPDATE + Outbox tablosuna INSERT
2. Background Worker veya Debezium (CDC) Outbox'tan Kafka'ya taşır
3. **Trade-off:** At-Least-Once garantisi → Consumer'lar **Idempotent** olmalı (EventId kontrolü)

### Saga Pattern (Mikroservisler Arası Transaction)

- **Choreography** (event-driven): 2-4 adımlık basit süreçler
- **Orchestration** (merkezi): 4+ adımlı karmaşık finansal akışlar — **Camunda, Temporal**

**Altın Kural:**

> *"15 adımlık akışta Choreography seçmek 'Event Spaghetti' anti-pattern'idir. Hata yönetimi, telafi (compensating transaction) ve durum izlenebilirliği için Orchestration zorunlu. Servisler birbirini bilmez, sadece orkestratörü bilir."*

---

## ⏱️ DAKIKA 40-45: Distributed Lock + Cache Stampede + Idempotency

### Cache Stampede (Önbellek İzdihamı)

10M takipçili yayıncının cache'i düştüğünde 50K istek aynı anda DB'ye düşer.

**3 Çözüm:**

1. **Distributed Lock**: İlk thread lock alır, DB'den çeker, cache'i doldurur, lock'u bırakır
2. **Background Refresh**: Worker proaktif olarak cache'i tazeler, kullanıcı isteği DB'ye hiç inmez
3. **Probabilistic Early Expiration (XFetch)**: TTL'in son %5'inde gelen istek eski veriyi alır, arka planda fire-and-forget yenileme

### Redis Distributed Lock — Kritik Detaylar

**Olmazsa olmazlar:**

1. **TTL** (5sn) → pod çökerse lock otomatik düşer
2. **Unique lockValue** (Guid) → "kim sahip?" kontrolü için
3. **Lua script ile bırak** → "ben sahip miyim?" + "sil" atomik olmalı

**Lua scriptin asıl çözdüğü:** Pod A lock alır, GC pause olur, TTL dolar, Pod B lock alır, Pod A uyanır ve "DEL" der → Pod B'nin lock'unu silmiş olur. Lua bunu engeller.

**ÖNEMLİ DÜRÜSTLÜK:** Lua bile critical section çakışmasını çözmez. TTL süresince işin bitmezse iki pod aynı anda kritik bölgeye girebilir. **Tam çözüm:** Lock + **Idempotency Key** + DB constraint.

### Idempotency (Mülakat Olasılığı YÜKSEK)

> *"Outbox Pattern At-Least-Once garantisi verir. Aynı event iki kez gelebilir. Consumer EventId'yi kendi DB'sinde kontrol eder, daha önce işlenmişse skip eder. Distributed sistemlerde 'lock yetmez, idempotent ol' kuralı geçerli — Martin Kleppmann da fencing token önerir."*

---

## ⏱️ DAKIKA 46-50: Test Stratejisi (Senin Zayıf Alanın!)

**Açılış cümlen (DÜRÜSTLÜK + BİLGİ):**

> *"Test culture'ı çok derin bir konu, kendimi 'çok güçlü' değil, 'yeterli ve gelişen' olarak konumlandırırım. Drivee'de SonarQube ve Copilot Review ile code quality gate'leri kurduk; unit test coverage'ı PR onay zorunluluğu yaptık. Test pyramid mantığıyla yazıyorum: bol unit, sınırlı integration, minimum E2E."*

**5 anahtar kavram:**

1. **Test Pyramid** — taban unit, tepe E2E. Tersi (Ice Cream Cone) anti-pattern.
2. **AAA** — Arrange / Act / Assert. **Tek metotta tek Act!**
3. **FIRST** — Fast, Independent, Repeatable, Self-Validating, Timely
4. **State vs Behavior Verification** — `Equal(state)` vs `Verify(method called)`
5. **Test Doubles** — Dummy, Stub, Spy, Mock, Fake. (Çoğu kişi her şeye "mock" der, sen ayrımı bil.)

**Hayat Kurtaran Cümleler:**

- *"Coverage'ı bir hedef değil, gösterge olarak görürüm. %100 coverage hatalı testlerle de elde edilir — kritik path'ler önemli."*
- *"Mock-heavy test'ler kötü tasarımın işaretidir. 5+ mock setup'lıyorsam SRP ihlal var demektir, sınıfı refactor ederim."*
- *"Async test'lerde `.Result` kullanmam, `async Task` test methodu + await."*
- *"Integration test'leri Testcontainers ile yazarım — production DB engine'i ile aynı."*

**Test Edilebilir Kod = İyi Tasarım:**

> *"TDD bir test yazma metodu değil, tasarım aracıdır. Test edilebilir kod yazmaya çalışmak beni zorla SOLID'e iter. Test, tasarımın aynasıdır."*

---

## ⏱️ DAKIKA 51-54: Deployment, Observability, Cross-cutting

### Deployment

- **Rolling**: Sunucular grup grup güncellenir (kapasite düşer)
- **Blue-Green**: 2 ortam, switch ile %100 trafik yönlendirilir, anında rollback
- **Canary**: Yeni versiyon %5 trafiğe açılır, metrikler izlenir, kademeli artırılır

**Riskli algoritma için → Canary** (felaket sadece %5'i etkiler).

### Observability — 3 Sütun

1. **Distributed Tracing** (Jaeger/Tempo) — Trace ID + Span ID, waterfall görsel
2. **Centralized Logging** (ELK/Loki) — Serilog ile TraceId her log'a basılır
3. **Metrics** (Prometheus/Grafana) — alarm kuralları

**Bağlayıcı standart:** OpenTelemetry. .NET 8 native destekler. **W3C Trace Context** = `traceparent` header.

### Cross-Cutting Concerns

Logging, security, validation, caching, exception handling, transaction. Her metoda manuel yazmak = code scattering + tangling. **Çözüm: AOP** (Aspect-Oriented Programming).

---

## ⏱️ DAKIKA 55-58: Behavioral / VP Culture Fit Hazırlığı

### Hikayen 1: Monolitten Mikroservise (En Büyük Kozun)

**STAR Kurgusu:**

- **Situation**: "Hızlı çıkmak için Web Forms ve monolit doğru karardı. Şirket büyüyünce yeni feature haftalar aldı."
- **Task**: "Backend'i mikroservislere bölmek + React Web + React Native + Cross-functional ekip + DevOps. Hepsi paralel."
- **Action**: "CI/CD altyapısını ve container stratejisini kurdum. Domain-Driven Design ile servis sınırları belirledim. API Gateway + BFF pattern ile mobile/web ekiplerini bağımsızlaştırdım."
- **Result**: "Release döngüleri haftadan günlere indi. Cross-functional ekipler birbirini beklemeden deploy etti. Mobil ürün başarıyla canlıya çıktı."

### Hikayen 2: "En Kötü Mimari Karar?" (Stres Sorusu)

**Tam ezberden bilmen gereken hikaye — Kaza Bildirim olayı:**

> *"En büyük hatamı dönüşümün başında yaptım: Kaza bildirim modülünü legacy Web Forms monolitinde bıraktım. SignalR ile real-time çalışan kritik bir sistemdi. Sistem büyüdükçe müşteriler büyük rapor çektiğinde IIS thread'leri bloklanıyor (Thread Starvation), tüm sistem yanıt veremez hale geliyordu. **En kötüsü: Sahadan gelen kaza bildirimleri de bu kilitlenmeye takılıyordu.**"*
>
> *"Mimariyi acilen değiştirdim — Event-Driven, Loosely Coupled yapıya geçtim. Legacy sistem sadece Kafka'ya mesaj bırakan bir Publisher oldu (fire-and-forget). Yepyeni izole bir SignalR mikroservisi Kafka'yı consume etti."*
>
> *"Çıkardığım ders: **Mikroservis sınırlarını sadece domain'e değil, modülün business criticality seviyesine göre çizerim.**"*

**Bu hikayedeki gold detaylar:**
- "Benim kararımdı" (sahiplenme)
- "Kaza bildirimi gecikiyordu" (insan/business impact)
- Buzzwords: Thread Starvation, Loosely Coupled, Event-Driven, Fire-and-Forget

### Hikayen 3: Ekip Direnci ve Teknoloji Geçişi

> *"En büyük risk teknik değil, kültüreldi. Web Forms'tan modern teknolojiye geçişi top-down dikte etmek direniş yaratırdı. Süreci 'keşif yolculuğuna' çevirdim — Angular vs React, Swift/Kotlin vs MAUI vs React Native üzerine ortak POC'ler yaptık. Ekip kendi araştırmasıyla React + React Native'de karar kıldı. **Unified Tech Stack** sayesinde öğrenme eğrisini hızlandırdık ve cross-functional geliştiriciler yarattık. Teknoloji ekibin seçimi olduğu için sıfır dirençle karşılaştık."*

### Çatışma Yönetimi (Mülakat klasiği)

> *"Ekip eski monolitiği koruma istedi çünkü güvenliydi. Ben yeni veri hacminin onu çökerteceğini biliyordum. Tartışmadım — Kafka ile küçük bir prototip yaparak performans farkını **gerçek metriklerle** gösterdim. Veriyi gördüklerinde mikroservis mimarisine katıldılar. Çatışmaları teknik kanıtlar ve doğrudan iletişimle çözüyorum."*

---

## ⏱️ DAKIKA 59-60: Tıkanma Anı Protokolü ve Açılış Stratejisi

### Live Coding'de Tıkandığında — 5 Adım

1. **Sessizliği boz**: *"Şu an darboğaz fark ettim, mantığı oturtmak için sesli düşüneceğim."*
2. **Problemi parçala**: Neyi bildiğini söyle, neyin eksik olduğunu söyle
3. **Önce kötü çözümü yaz** (Brute Force): *"O(N²) çözümle başlayıp sonra refactor edeceğim."*
4. **Bilmediğini soyutla**: *"Burada Regex kalıbını hatırlayamadım, `bool IsValidIban()` diye varsayımsal metot olarak geçiyorum."*
5. **İpucu istemekten çekinme**: Mülakatçı düşman değil, pair programmer.

### Sesli Düşünme — Mülakatın Altın Kuralı

> *"Burada List kullanıyorum çünkü işlem sırası önemli, ama performans darboğazı görürsek HashSet'e çevirebilirim."*

Sesli trade-off konuşması = **Senior+ göstergesi**.

### Edge Case Refleksleri

Onlar sormadan sen söyle:
- "Ya null gelirse?"
- "Ya format yanlışsa?"
- "Ya aynı anda iki istek gelirse (Race Condition)?"
- "Ya input boşsa?"

### Sormak İçin Sorular (Mülakat Sonu)

Sen de soru sor — bu, ilgi göstergesidir:

1. *"Scorp'ta tipik bir mikroservisin saniyedeki request hacmi nedir?"*
2. *"Mevcut test coverage hedefiniz nedir, hangi araçları kullanıyorsunuz?"*
3. *"On-call rotasyonu var mı, varsa beklentiler nelerdir?"*
4. *"Ekibin teknoloji kararlarına dahil olma seviyesi nedir? Top-down mu, bottom-up mu?"*
5. *"Sizin için bu rolde ilk 90 günde başarı neye benzer?"*

---

# 🔥 SON 60 SANIYE — SAMURAI CHECKLIST

Mülakata girmeden önce kendine bunları sor:

✅ **Kim olduğum** cümlemi 30 saniyede söyleyebiliyorum?
✅ Kaza Bildirim hikayesi STAR formatında ezberimde mi?
✅ Big O tablosu ve veri yapısı kararları kafamda hazır mı?
✅ Async/Result/Thread Pool Starvation üçlüsünü açıklayabilir miyim?
✅ Outbox Pattern + Idempotency'i bir nefeste anlatabilir miyim?
✅ Cache Stampede 3 çözümünü sayabilir miyim?
✅ SOLID'i pratik örneklerle açıklayabilir miyim?
✅ Test konusunda dürüst-ama-bilgili açılış cümlemi hatırlıyor muyum?
✅ Tıkandığımda 5 adımlı kriz protokolünü uygulayacak mıyım?
✅ Mülakat sonunda **ben de soru soracağım**?

---

# 💪 KAPANIŞ — KENDİNE HATIRLATMAN GEREKEN 3 ŞEY

1. **Sen "inşaatçısın".** Drivee'de günde 50 milyon telemetri verisi işliyorsun. Yemeksepeti'nde quick-commerce kurdun. Delivery Hero'da global entegrasyon framework'ü yazdın. **Bu masada tecrübe açısından eşitsin, hatta üstünsün.**

2. **Bilmediğini söylemek güçtür, zayıflık değil.** "Bunu Polly ile çözerdim ama spesifik retry policy konfigürasyonunu canlıda hiç uygulamadım, dökümana bakarım" demek "uydurma" yapmaktan **kat kat değerlidir.**

3. **Mülakat tek taraflı değil.** Sen de Scorp'u değerlendiriyorsun. Eğer onların kültürü, ekibi, teknolojisi sana uygun değilse, en iyi teklifi de versen kabul etmemen lazım. **Bu zihniyet, masada özgüven yaratır.**


