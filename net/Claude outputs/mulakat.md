# Full Stack Mülakat Dosyası — .NET · React · React Native · Araç Telematiği

**Senior Full Stack Software Developer pozisyonu için teknik mülakat hazırlığı · Ekim 2026**

Bu dosya iki parçadan oluşur: ayrıntılı bir **soru-cevap çalışma rehberi** (§1–§12, §14) ve **1–2 sayfalık hızlı gözden geçirme** (§13). Her konu şu kalıpla işlenir: *soru → model cevap → takip soruları → sık hatalar → İngilizce model cevap*. Metin Türkçe, teknik terimler İngilizce bırakıldı.

**İşaretler:** ⭐ = mülakatta çıkma olasılığı yüksek soru. **Q numaraları** çapraz başvuru içindir (ör. "Q4"). Bölüm başvuruları `§5.3` biçimindedir.

**Önerilen ağırlık:** .NET (temel + ileri) %30 · React Native + React/Next.js %25 · telematik %30 · sistem tasarımı + davranışsal %15. Temel sorular çok önemlidir: senior rolde bile mülakat çoğu zaman `struct` / `async` / `IEnumerable` gibi temel sorularla başlar ve bunları **neden** ve **bedeli** ile cevaplamanı bekler.


## TL;DR

- **Takvim:** .NET 8 ve .NET 9'un desteği **10 Kasım 2026**'da birlikte bitiyor; hedef **.NET 10 LTS**. React **19.3**, Next.js **16.4**, React Native 0.82'den beri yalnızca New Architecture, Kafka 4.x yalnızca KRaft. Bu tarihleri bilmek "güncel" görünmenin en ucuz yoludur.
- **En sık yapılan "yanlış cümleler":** "Struct stack'te yaşar, GC'ye girmez", "composite index'te en yüksek cardinality her zaman başa", "PostgreSQL'de clustered index", "Serializable her şeyi kilitler", "Redis Cluster CP'dir", "CQRS kilitlenmeyi kökten çözer", "DI bir GoF desenidir", "Big O en kötü durumdur". Hepsinin doğrusu ilgili bölümlerde.
- **Senior sinyali:** her pattern için "ne zaman **kullanmam**" cümlesi; hikâyelerde 1 rakam + 1 trade-off + "bugün olsa farklı ne yapardım"; bilmediğini dürüstçe söylemek.
- **Telematikte puan getirenler:** Teltonika Codec 8/8E çerçeveleme + ACK'in dayanıklı yazımdan sonra gönderilmesi, `System.IO.Pipelines` backpressure, idempotent ingestion `(device_id, ts)`, geç/sırasız veri, TimescaleDB/PostGIS, KVKK/GDPR'da konumun "yaşam alışkanlığı" verisi olması.

## İçindekiler

1. Güncellik özeti
2. **Temel .NET** ⭐ (Q1–Q42)
3. İleri .NET: reflection, GC, async, Channels, ASP.NET, EF Core, OpenTelemetry
4. Mimari ve design pattern'ler (EIP, PoEAA, DDD, SOLID/DI, GoF)
5. **Veri, cache ve mesajlaşma** (PostgreSQL, ACID, CAP, Redis, Kafka, outbox, saga)
6. **Kalite, dağıtım, gözlemlenebilirlik**
7. **React JS (web) ve Next.js**
8. React Native
9. Araç telematiği
10. AWS: niçin ve ne zaman
11. Sistem tasarımı ve davranışsal sorular (STAR, live coding)
12. **Eksik konular: senior full stack için ek alanlar** ⭐
13. **Hızlı gözden geçirme (cheat sheet)**
14. Dikkat edilecekler ve kaynaklar

---


## 1. Güncellik Özeti

| Konu | Güncel durum (Ekim 2026) | Mülakatta nasıl kullanılır |
|---|---|---|
| .NET 10 | 11 Kasım 2025'te çıktı, **LTS** (3 yıl destek). C# 14: extension blocks, `field`, `?.=` | "Yeni projeler .NET 10'da, upgrade planımız şu…" |
| .NET 8 / 9 | İkisinin de desteği **10 Kasım 2026**'da bitiyor (STS süresi 24 aya uzatıldığı için .NET 9 da aynı güne denk geldi) | Upgrade riski: DATAS, breaking change'ler. Teknik borcu EOL tarihiyle sat |
| .NET 11 | RC1 (go-live lisanslı), GA Kasım 2026 bekleniyor, STS. C# 15 union türleri. **Özellikleri değişebilir** | "Production'da LTS'te kalırım; 11'i pilot serviste denerim" |
| GC | **DATAS** .NET 8'de opt-in, .NET 9'dan beri varsayılan (yalnızca Server GC) | Container bellek davranışı |
| `HybridCache` | .NET 9+ (`Microsoft.Extensions.Caching.Hybrid`): L1+L2, stampede koruması **yalnızca aynı örnek içinde** | Cache stampede (§5.5) |
| EF Core 10 | LeftJoin/RightJoin, named query filters, complex types → JSON, JSON için `ExecuteUpdate` | Multi-tenant filtreleme |
| PostgreSQL 18 | 25 Eylül 2025: **skip scan** (öndeki kolona koşul olmadan bileşik indeks kullanımı) | Composite index sırası (§5.2) |
| Kafka 4.x | ZooKeeper kaldırıldı, **yalnızca KRaft**; yeni tüketici grup protokolü (KIP-848) | Mesajlaşma (§5.6) |
| Lisans değişimleri | **MediatR, AutoMapper** (Tem 2025, çift lisans/ticari), **MassTransit v9** (ticari; v8 OSS), **FluentAssertions v8** (Oca 2025, ticari), Moq SponsorLink tartışması (2023). Redis OSS lisansı değişti (2024), **Valkey** fork'u | "Pattern'i bilirim ama bağımlılığı **lisans ve sürdürülebilirlik** açısından sorgularım" |
| **React** | **19.3** (9 Eyl 2026): `<ViewTransition>`, Fragment ref'leri, `browser()`, Trusted Types. 19.2: `<Activity>`, `useEffectEvent`. **React Compiler 1.0** (Eki 2025). RSC'de kritik RCE (CVE-2025-55182) yamalandı. CRA kullanımdan kaldırıldı (Şub 2025). React Foundation (Linux Foundation, Şub 2026) | §7 |
| **Next.js** | **16.4** (6 Eki 2026): Cache Components yeni projelerde varsayılan, **17'de varsayılan olacak**; Turbopack varsayılan bundler; `proxy.ts`; async `params`. Sık güvenlik sürümleri | §7.5 |
| React Native | 0.82 (Eki 2025) yalnızca New Architecture. 0.84'te Hermes V1 varsayılan. 0.86 (11 Haz 2026) breaking change içermiyor | Fabric/TurboModules/JSI |
| Expo | SDK 54 Legacy Architecture'ı destekleyen son sürüm. SDK 56 = RN 0.85 + React 19.2 | Upgrade stratejisi |
| Android 14 | Konum FGS'i için `foregroundServiceType="location"` ve `FOREGROUND_SERVICE_LOCATION` zorunlu | Arka plan konum |
| AWS Lambda | .NET 10 managed runtime ve container image olarak destekli. Native AOT, SnapStart, Lambda Managed Instances | Cold start |
| Timestream | LiveAnalytics 20 Haziran 2025'ten beri yeni müşteriye kapalı. AWS, Timestream for InfluxDB'yi öneriyor | Zaman serisi seçimi |
| TimescaleDB | RDS/Aurora PostgreSQL extension listesinde yok | Tiger/Timescale Cloud, EC2 self-hosted ya da pg_partman |

---

## 2. Temel .NET ⭐

Bu bölüm "temel ama eleyici" sorular için. Senior adaydan beklenen: tanımı tek cümleyle vermek, sonra **neden önemli**, **nerede ısırır** ve **modern .NET'te ne değişti** kısmını eklemek. ⭐ işareti sorulma ihtimali çok yüksek olan sorulardır.

Çalışma yöntemi: her soruyu önce yüksek sesle 30-45 saniyede cevapla, sonra "Takip" sorusuna geç. Tanım ezberlemek yerine her soru için bir "tuzak cümlesi" hazırla (cevabını sıradan adaydan ayıran tek cümle).

### 2.1 Platform ve çalışma zamanı

#### ⭐ Q1. .NET, CLR, CTS, CLS, BCL ve IL nedir? Bir C# programı nasıl çalışır?

**Model cevap:** C# derleyicisi (Roslyn) kodu **IL (Intermediate Language) + metadata** olarak bir assembly'ye (`.dll`/`.exe`) derler. Çalışma zamanında **CLR** assembly'yi yükler, IL'i **JIT** ile makine koduna çevirir ve çalıştırır. CLR ayrıca GC, tip güvenliği, exception işleme ve thread yönetimini sağlar.
- **CTS (Common Type System):** class, struct, interface, enum, delegate gibi tiplerin ortak kuralları. Diller arası birlikte çalışmayı mümkün kılar.
- **CLS (Common Language Specification):** diller arası uyumluluk için CTS'in alt kümesi.
- **BCL (Base Class Library):** `System.*` altındaki temel sınıflar (koleksiyonlar, IO, threading, ağ).
- **JIT derleme:** Metot ilk çağrıda hızlı ve optimize edilmemiş (Tier-0) derlenir. Sık çağrılan ya da döngüde sıcak kalan metotlar (yaklaşık 30 çağrı sonrası) arka planda optimize (Tier-1) derlenir. .NET 8'den beri dynamic PGO varsayılan açıktır, yani JIT çalışma zamanı profiline göre karar verir.
- **Alternatifler:** *ReadyToRun* (önceden kısmen derlenmiş, açılış hızlı), *Native AOT* (JIT ve runtime yok, tek native binary, hızlı açılış ve düşük bellek; ama reflection ve dinamik yükleme kısıtlı).

**Takip:** "Neden JIT yerine hepsini AOT yapmıyoruz?" JIT çalışma zamanı bilgisiyle (CPU özellikleri, profil) daha iyi optimize edebilir; AOT açılış ve bellek kazandırır ama reflection ve trimming kısıtları getirir. Lambda ve CLI gibi kısa ömürlü süreçlerde AOT, uzun ömürlü sunucularda JIT çoğu zaman yeterlidir.

#### ⭐ Q2. .NET Framework, .NET Core, .NET 5+ ve .NET Standard arasındaki fark nedir?

**Model cevap:**
- **.NET Framework (4.x):** yalnızca Windows, bakım modunda, Windows ile gelir.
- **.NET Core (1.0-3.1):** açık kaynak, cross-platform, modüler, yüksek performans. ASP.NET Core burada doğdu.
- **.NET 5+:** "Core" adı düştü, tek ve birleşik platform. Yılda bir sürüm gelir: çift numaralılar **LTS**, tekler **STS**.
- **.NET Standard:** farklı runtime'lar arasında kütüphane paylaşmak için API sözleşmesiydi. Bugün yeni kütüphane için gerekmez; doğrudan `net8.0`/`net10.0` hedeflenir, gerekirse multi-target yapılır.
- **Güncel takvim:** .NET 8 ve 9'un desteği **10 Kasım 2026**'da bitiyor. Hedef sürüm **.NET 10 (LTS)**.

**Takip:** "Legacy .NET Framework uygulamasını nasıl taşırsın?" `try-convert`/.NET Upgrade Assistant ile analiz, bağımlılıkları .NET uyumlu sürümlere çek, `System.Web` bağımlılıklarını ASP.NET Core'a taşı, adım adım "strangler" yaklaşımı, yan yana çalıştır.

#### Q3. Runtime ile SDK farkı nedir? `dotnet` CLI ve publish seçenekleri?

**Model cevap:** *Runtime* uygulamayı çalıştırır, *SDK* derleme/test/publish araçlarını da içerir. `dotnet build`, `test`, `run`, `watch`, `publish`, `ef`, `user-secrets` günlük komutlardır. Publish seçenekleri:
- **Framework-dependent:** küçük çıktı, hedefte runtime gerekir.
- **Self-contained:** runtime paketin içinde.
- **Single-file, trimmed, ReadyToRun, Native AOT:** boyut/açılış optimizasyonları.
- Container'da: `mcr.microsoft.com/dotnet/aspnet` (çalıştırma) ve `sdk` (derleme) imajlarıyla multi-stage build.
- Proje yönetimi: `Directory.Build.props`, **Central Package Management** (`Directory.Packages.props`), `global.json` ile SDK sabitleme, analyzers ve `.editorconfig`.

### 2.2 Tipler, bellek ve eşitlik

#### ⭐ Q4. Value type ile reference type arasındaki fark nedir? "Value type'lar stack'te yaşar" doğru mu?

**Model cevap:** Değer tipleri (`int`, `double`, `bool`, `struct`, `enum`, `DateTime`) verisini doğrudan taşır; atama ve parametre geçişinde **kopyalanır**. Referans tipleri (`class`, `interface`, `delegate`, `string`, diziler) managed heap'teki bir nesneye **referans** taşır; atamada referans kopyalanır, nesne paylaşılır.

"Value type stack'te yaşar" **eksik ve yanıltıcı bir kısaltmadır**. Değer tipinin nerede durduğu bağlama bağlıdır:
- Metot içi yerel değişken: stack (ya da JIT onu register'a alır).
- **Bir sınıfın alanı:** o nesnenin içinde, yani heap'te.
- **Dizi elemanı** (`int[]`, `SensorStruct[]`): dizinin içinde, yani heap'te.
- **Boxing** yapılmışsa: heap'te.
- Lambda'ya yakalanmış ya da `async` metodun state machine'ine girmiş değişken: heap'te.

Üstelik referans tipli bazı nesneler bile JIT'in escape analysis'i sayesinde stack'e alınabilir (.NET 9/10'da genişliyor). Yani stack/heap bir **uygulama detayıdır**; dilin garanti ettiği **semantik farktır**: kopyalama mı, paylaşım mı.

```csharp
struct P { public int X; }
var a = new P { X = 1 };
var b = a;          // kopya
b.X = 2;            // a.X hâlâ 1

class C { public int X; }
var c1 = new C { X = 1 };
var c2 = c1;        // aynı nesne
c2.X = 2;           // c1.X de 2
```

**Takip:** "Struct ne zaman, class ne zaman?" → Q10. **Sık hata:** "struct hep daha hızlıdır" demek. Büyük struct kopyalama maliyeti getirir, boxing allocation yaratır.

#### ⭐ Q5. Boxing ve unboxing nedir? Nasıl önlenir?

**Model cevap:** Değer tipini `object`'e ya da bir interface'e atamak **boxing**'dir: heap'te yeni bir nesne oluşur ve değer oraya kopyalanır. Geri almak **unboxing**'dir ve tam doğru tipe cast gerekir (`(int)obj`). Maliyeti allocation ve GC baskısıdır.
- Tipik kaynaklar: `ArrayList` ve non-generic koleksiyonlar, `object` parametreli API'ler, değer tipinin interface üzerinden kullanımı, eski `string.Format` çağrıları.
- Önleme: generic koleksiyonlar (`List<int>`), generic constraint'ler, `IEquatable<T>`, `readonly struct`, interpolated string handler'lar.
- Tuzak: `object a = 1, b = 1; a == b` → **false** (iki ayrı kutu, referans karşılaştırması). `a.Equals(b)` → true.

#### ⭐ Q6. `string` neden immutable? Döngüde birleştirme neden kötü? Karşılaştırmada nelere dikkat edilir?

**Model cevap:** `string` referans tipidir ama **değişmezdir**; her değişiklik yeni bir string üretir. Döngüde `+=` her seferinde yeni nesne ve kopya demektir (O(n²)); `StringBuilder` kullan. `==` string için içerik karşılaştırır (operatör aşırı yüklenmiştir).
- Literal'ler **intern** edilir (aynı literal tek nesne). Runtime'da üretilen string'ler edilmez.
- **Karşılaştırma:** Kimlik, anahtar, protokol gibi kültürden bağımsız verilerde `StringComparison.Ordinal`/`OrdinalIgnoreCase` kullan. Varsayılan karşılaştırmalar kültüre duyarlıdır.
- **Türkçe I problemi:** `tr-TR` kültüründe `"I".ToLower()` → `"ı"`, `"i".ToUpper()` → `"İ"`. `"FILE".ToLower() == "file"` Türkçe kültürde yanlış çıkabilir. Çözüm: `ToLowerInvariant()` ya da `Ordinal` karşılaştırma.
- **Allocation azaltma:** `ReadOnlySpan<char>` ile dilimleme (`Substring`/`Split` yerine), `string.Create`, `StringBuilder` pooling, interpolated string handler'lar.
- .NET'te `char` UTF-16 kod birimidir; emoji gibi karakterler iki `char` tutar (`Rune` ya da `StringInfo` ile işlenir).

#### ⭐ Q7. `==`, `Equals`, `ReferenceEquals` ve `GetHashCode` ilişkisi nedir?

**Model cevap:**
- `ReferenceEquals(a, b)`: aynı nesne mi?
- `Equals(object)`: sanal metot. Sınıflar için varsayılan referans eşitliği; struct için alan bazlı (yavaş olabilir, reflection kullanabilir).
- `==`: operatör. Referans tiplerde varsayılan referans karşılaştırması; `string`, `record`, `decimal` gibi tiplerde aşırı yüklenmiştir. Struct için varsayılan `==` yoktur.
- **Sözleşme:** `a.Equals(b)` ise `a.GetHashCode() == b.GetHashCode()` olmalıdır. `Equals` override edersen `GetHashCode`'u da et ve `IEquatable<T>` uygula (boxing'siz karşılaştırma).
- Hash kodu, nesne bir `Dictionary`/`HashSet` içindeyken **değişmemelidir**. Mutable alan üzerinden hash üretirsen nesne koleksiyonda "kaybolur".
- Modern yol: `record`/`record struct` değer eşitliğini otomatik üretir; manuel hash için `HashCode.Combine(...)`.

#### Q8. `const`, `readonly` ve `static readonly` farkı?

**Model cevap:** `const` derleme zamanı sabitidir (yalnızca ilkel tipler, `string`, `null`) ve çağıran koda **gömülür**; kütüphanedeki const'u değiştirirsen bağımlı projeleri yeniden derlemek gerekir (versioning tuzağı). `readonly` alan ise çalışma zamanında bildirimde ya da constructor'da atanır. Sabit olmayan "sabitler" (ör. `TimeSpan`, `Regex`, koleksiyon) için `static readonly` kullanılır. Not: `readonly` alan referansı sabitler, nesnenin içeriğini değil (`readonly List<int>` yine de değişebilir; `ImmutableArray` ya da `FrozenSet` kullan).

#### Q9. `ref`, `out`, `in`, `params` ne işe yarar? Referans tipler "by reference" mı geçer?

**Model cevap:** C#'ta parametreler **varsayılan olarak değer ile geçer**. Referans tiplerde "değer" referansın kendisidir: metot içinde nesneyi **değiştirmek** çağırana yansır, ama parametreyi **yeniden atamak** yansımaz. Bunu değiştirmek için `ref`.
- `ref`: değişkenin kendisine takma ad (başlatılmış olmalı).
- `out`: metot mutlaka atamalı (`TryParse` deseni).
- `in`: salt okunur referans (büyük struct'ı kopyalamadan geçirmek için; C# 12'de `ref readonly` ile birlikte).
- `params`: değişken sayıda argüman. C# 13'te `params ReadOnlySpan<T>` ve koleksiyon türleriyle allocation'sız hale geldi.
- Seçenekli ve isimli argümanlar; `scoped` ve `ref struct` kuralları ileri konulardır.

#### ⭐ Q10. struct, class, record ve record struct: hangisini ne zaman seçersin?

**Model cevap:** Varsayılanım **class**. Struct'ı şu koşulların hepsi varsa seçerim (Microsoft yönergesi): tek bir değeri temsil eder, **16 bayttan küçüktür**, **immutable**'dır ve sık boxing'e girmez.
- `record` (class): değer eşitliği, `ToString`, `with` ifadesi; DTO ve immutable veri için. Dikkat: içindeki `List<T>` gibi alanlar **referans eşitliğiyle** karşılaştırılır.
- `readonly record struct`: küçük değer nesneleri (value object) için, örn. `GeoPoint(double Lat, double Lon)` (16 bayt).
- 15-20 alanlı bir telemetri kaydı için struct **önerilmez**: kopyalama maliyeti büyür. Çözüm: class kullan ve pool'la, ya da yalnızca parse için `ref struct`/`Span` kullan; analitik işlerde struct-of-arrays (kolonlu) düzen.
- Detay ve GC bağlantısı: §3.2.

#### Q11. `int?` ile nullable reference type (NRT) aynı şey mi?

**Model cevap:** Hayır. `int?` gerçek bir tiptir (`Nullable<int>` struct'ı). **NRT** (C# 8, `<Nullable>enable</Nullable>`) ise yalnızca **derleme zamanı analizidir**; çalışma zamanında `string` ile `string?` aynı tiptir ve null kontrolü eklemez. Araçlar: `?.`, `??`, `??=`, `!` (null-forgiving, dikkatli kullan), `ArgumentNullException.ThrowIfNull(x)`, `required` üyeler. API sınırlarında (controller, DTO) NRT tek başına doğrulama yerine geçmez; model doğrulama yine gerekir.

#### Q12. `var`, `dynamic` ve `object` farkı nedir?

**Model cevap:** `var` derleme zamanında tipi çıkarır; hâlâ **statik tiplidir**. `object` her şeyin temel tipidir; kullanmak için cast (ve değer tipinde boxing) gerekir. `dynamic` ise tip denetimini **çalışma zamanına** erteler (DLR), yavaştır ve derleyici yardımını kaybettirir. `dynamic`'i COM/JSON gibi çok dinamik senaryolarda sınırlı kullan; iş mantığında kaçın.

### 2.3 OOP ve dil özellikleri

#### ⭐ Q13. OOP'nin dört temeli nedir ve C#'ta karşılığı nedir?

**Model cevap:**
- **Encapsulation:** durumu gizle, erişimi kontrol et (`private` alan + property).
- **Abstraction:** ayrıntıyı saklayıp sözleşme sun (`interface`, `abstract class`).
- **Inheritance:** C#'ta tek sınıftan, birden çok interface'ten türetme. Miras bir "is-a" ilişkisidir, kod yeniden kullanımı için değil; yeniden kullanım için çoğunlukla **composition** tercih edilir.
- **Polymorphism:** derleme zamanında (overload) ve çalışma zamanında (virtual/override, interface).
- Senior notu: Liskov (LSP) ihlal eden miras kötü tasarımın işaretidir. "Kalıtım yerine kompozisyon" kuralını bir örnekle söyle (ör. `Logger : FileWriter` yerine `Logger` içinde `IWriter`).

#### ⭐ Q14. Abstract class ile interface farkı nedir?

**Model cevap:**
- **Interface:** sözleşme. Birden fazla uygulanabilir, state taşımaz. C# 8'den beri **default interface method** ve C# 11'den beri **static abstract member** (generic math) destekler.
- **Abstract class:** ortak durum (alan), constructor ve kısmi implementasyon taşıyabilir; tek kalıtım.
- Seçim: yetenek/sözleşme, DI ve test dikişi gerekiyorsa interface. Ortak durum ya da Template Method gerekiyorsa abstract class.
- Tuzak: Default interface method çoklu "durum" mirası değildir; sınıf yine de açık uygulama yapmadıkça yalnızca interface referansı üzerinden çağırılabilir.

#### ⭐ Q15. `virtual`, `override`, `new`, `sealed` ve `abstract` farkı nedir?

```csharp
class A { public virtual void M() => Console.WriteLine("A"); }
class B : A { public override void M() => Console.WriteLine("B"); }
class C : A { public new void M() => Console.WriteLine("C"); }

A x = new B(); x.M();   // B  (override: çalışma zamanı tipine göre)
A y = new C(); y.M();   // A  (new: gizleme, referans tipine göre)
```

**Model cevap:** `virtual` türetilenin değiştirebileceğini, `override` değiştirdiğini belirtir. `new` yalnızca **gizler**, polimorfizm yaratmaz. `sealed` daha fazla override/türetmeyi engeller. `abstract` gövdesiz, türetenin doldurması gereken üyedir. Tuzak: constructor içinde virtual metot çağırırsan türeyen sınıfın override'ı, türeyenin alanları daha başlatılmadan çalışır.

#### Q16. Erişim belirleyicileri nelerdir? Varsayılanlar?

**Model cevap:** `public`, `internal` (aynı assembly), `protected`, `private`, `protected internal`, `private protected` (aynı assembly **ve** türeyen), `file` (C# 11, yalnızca aynı dosya). Üyelerde varsayılan `private`, üst düzey tiplerde `internal`. Test projesine `internal` açmak için `InternalsVisibleTo`.

#### Q17. static üye ve static class nedir? Singleton nasıl yazılır?

**Model cevap:** `static` üye tipe aittir, nesneye değil. `static class` örneklenemez. Static constructor tip ilk kullanılmadan önce **bir kez** ve thread-safe çalışır. Generic sınıfta static alan **kapalı tip başına** ayrıdır (`Foo<int>` ile `Foo<string>` ayrı alan). Singleton için manuel `static Instance` yerine DI'da `AddSingleton` kullan; manuel gerekirse `Lazy<T>` ya da `static readonly` alan. Tuzak: mutable static durum test ve eşzamanlılık sorunlarının ana kaynağıdır.

#### Q18. Property ile field farkı? `init`, `required` ve C# 14 `field` anahtar kelimesi?

**Model cevap:** Property derleyicide get/set metotlarıdır; doğrulama, lazy hesaplama, bağlama ve ileride değişim imkânı verir. Alanı public açma. `init` yalnızca nesne başlatırken atama izni verir (immutability). `required` (C# 11) üyenin başlatılması zorunluluğunu **derleme zamanında** denetler. **C# 14**'te `field` anahtar kelimesi property accessor içinde derleyicinin ürettiği yedek alana erişim verir:

```csharp
public string Name
{
    get;
    set => field = value?.Trim() ?? throw new ArgumentNullException(nameof(value));
}
```

#### Q19. Extension method nedir? C# 14'te ne değişti?

**Model cevap:** `static` sınıfta `this` parametreli statik metottur, derleme zamanında çözülür, özel üyelere erişemez. LINQ ve `services.AddXxx()` bu şekildedir. **C# 14 extension blocks** ile extension **property** ve statik üye tanımlamak mümkün:

```csharp
public static class StringExtensions
{
    extension(string s)
    {
        public bool IsBlank => string.IsNullOrWhiteSpace(s);
    }
}
```

Tuzak: extension, bir nesnenin gerçek metodu yoksa devreye girer; aynı imzalı gerçek metot her zaman kazanır.

#### ⭐ Q20. Delegate, event, `Action`/`Func` ve closure nedir? Event'ler nasıl bellek sızıntısı yapar?

**Model cevap:** *Delegate* tip güvenli bir fonksiyon referansıdır; çoklu aboneliğe (multicast) izin verir. `Action<>` dönüş değersiz, `Func<>` dönüşlü, `Predicate<T>` bool dönen delegate'tir. *Event* ise delegate'in kısıtlı halidir: dışarıdan yalnızca `+=`/`-=` yapılır, **tetikleme yalnızca sahibine** aittir. *Closure*, lambda'nın dış değişkenleri **değer olarak değil, değişkenin kendisini** yakalamasıdır.
- **Sızıntı:** Yayıncı (publisher) aboneye güçlü referans tutar. Uzun ömürlü yayıncıya (static event, singleton) abone olup `-=` yapmazsan abone GC'lenemez.
- Event tetikleme: `Changed?.Invoke(this, args)` (null ve yarış koşulundan korur).
- `async void` event handler: exception yakalanamaz; yalnızca UI event'lerinde kabul edilir.

```csharp
var actions = new List<Action>();
for (int i = 0; i < 3; i++) actions.Add(() => Console.Write(i));
actions.ForEach(a => a());              // 333: tek bir i yakalandı

var fs = new List<Action>();
foreach (var x in new[] { 0, 1, 2 }) fs.Add(() => Console.Write(x));
fs.ForEach(a => a());                    // 012: C# 5'ten beri foreach değişkeni her turda ayrı
```

#### ⭐ Q21. Generics: neden kullanılır? Constraint'ler, covariance ve contravariance nedir?

**Model cevap:** Generic'ler **tip güvenliği** sağlar, cast ve boxing'i ortadan kaldırır, kod tekrarını azaltır. .NET'te generic'ler **reified**'dir (Java'daki gibi silinmez): çalışma zamanında `List<int>` ayrı bir tiptir ve değer tipi için özelleştirilmiş kod üretilir.
- **Constraint'ler:** `where T : class`, `struct`, `notnull`, `unmanaged`, `new()`, `IComparable<T>`, `Enum` vb.
- **Covariance (`out T`):** `IEnumerable<string>` değeri `IEnumerable<object>` olarak kullanılabilir (yalnızca çıktı konumunda).
- **Contravariance (`in T`):** `Action<object>` bir `Action<string>` yerine kullanılabilir; `IComparer<in T>`.
- Variance yalnızca interface ve delegate'lerde, ve yalnızca referans tipleri için geçerlidir. `List<string>` → `List<object>` **yapılamaz**. (Diziler eski bir hata olarak covariant'tır: `object[] a = new string[1]; a[0] = 1;` çalışma zamanında `ArrayTypeMismatchException`.)
- Modern: static abstract üyelerle **generic math** (`INumber<T>`).

#### ⭐ Q22. Modern C# özelliklerinden hangilerini kullanıyorsun? (C# 8 → 14)

| Sürüm (.NET) | Öne çıkanlar |
|---|---|
| C# 8 (.NET Core 3.0) | nullable reference types, switch expression, async streams (`IAsyncEnumerable`), `Index`/`Range`, default interface methods, `using` declaration |
| C# 9 (.NET 5) | `record`, `init`, top-level statements, target-typed `new()`, `and`/`or`/`not` pattern'leri |
| C# 10 (.NET 6) | `record struct`, global usings, file-scoped namespace, interpolated string handler |
| C# 11 (.NET 7) | raw string literal, `required`, list pattern, generic math, `file` tipler, `u8` literal |
| C# 12 (.NET 8) | primary constructor (her sınıf/struct), collection expression (`[1, 2, ..other]`), `ref readonly` parametre, default lambda parametreleri |
| C# 13 (.NET 9) | `params` koleksiyonları (Span dahil), `System.Threading.Lock`, partial property, `\e` kaçışı |
| C# 14 (.NET 10) | extension blocks, `field` anahtar kelimesi, null-conditional assignment (`a?.B = x`), partial constructor/event |
| C# 15 (.NET 11, RC) | union türleri (ön izleme; GA .NET 11 ile bekleniyor) |

Pratik cevap: "Primary constructor ve collection expression'ı DI ve DTO'larda, pattern matching'i karar mantığında, `required`+`init`'i immutable model için kullanıyorum."

```csharp
// pattern matching + switch expression
string Describe(object o) => o switch
{
    null                    => "boş",
    int n when n < 0        => "negatif",
    int n                   => $"sayı {n}",
    string { Length: 0 }    => "boş string",
    _                       => o.GetType().Name
};

// list pattern: dizi/liste gibi sayılabilir ve indekslenebilir tiplerde
int[] a = [1, 2, 3];
if (a is [var first, .., var last]) Console.WriteLine($"{first}..{last}");   // 1..3
```

#### Q23. `record` neden kullanılır? Tuzakları nelerdir?

**Model cevap:** `record` değer tabanlı eşitlik, `ToString`, `Deconstruct` ve `with` ile **kopyalayarak değiştirme** üretir. DTO, olay (event) verisi, value object için uygundur. Tuzaklar: (1) eşitlik **yüzeyseldir**; içinde `List<T>` varsa listeler referansla karşılaştırılır. (2) `record` bir class'tır; `record struct` ise değer tipidir. (3) `init` alanlar nesne başlatıcıdan sonra değişmez ama derin immutability sağlamaz. (4) EF Core entity'si olarak kullanırken kimlik ve takip semantiğine dikkat (value object için owned/complex type uygundur).


### 2.4 Koleksiyonlar ve LINQ

#### ⭐ Q24. Hangi koleksiyonu ne zaman seçersin? Karmaşıklıkları nedir?

| Tip | Erişim / arama | Ekleme / silme | Not |
|---|---|---|---|
| `T[]` | indeks O(1) | sabit boyut | en az yük, cache dostu |
| `List<T>` | indeks O(1), `Contains` O(n) | sona ekleme **amortize** O(1), ortaya O(n) | kapasiteyi biliyorsan `new List<T>(capacity)` |
| `Dictionary<K,V>` | **ortalama** O(1) | **ortalama** O(1) | thread-safe değil; sıraya güvenme |
| `HashSet<T>` | **ortalama** O(1) | **ortalama** O(1) | "var mı?" sorgusu, tekilleştirme, küme işlemleri |
| `SortedDictionary` / `SortedSet` | O(log n) | O(log n) | kırmızı-siyah ağaç, sıralı gezinme |
| `SortedList<K,V>` | O(log n) | ekleme O(n) | az yazılan, sıralı veri |
| `LinkedList<T>` | O(n) | düğüm elindeyse O(1) | cache dostu değil, nadiren doğru seçim |
| `Queue<T>` / `Stack<T>` | uçlara O(1) | O(1) | FIFO / LIFO |
| `PriorityQueue<TEl,TPr>` (.NET 6) | min elemana O(1) | O(log n) | binary heap; decrease-key yok, kararlı değil |
| `ImmutableArray/List` | - | her değişiklik yeni kopya | paylaşım ve thread-safety |
| `FrozenDictionary/FrozenSet` (.NET 8) | en hızlı okuma | **bir kez oluştur** | başlangıçta kurulan, sonra yalnızca okunan arama tabloları |

**Dictionary nasıl çalışır?** Anahtarın `GetHashCode()` değeri bir kovaya (bucket) eşlenir, çakışmalar zincirle (entries dizisindeki indekslerle) çözülür, doluluk artınca asal boyuta büyür ve **yeniden hash'lenir**. Bu yüzden: (1) anahtar tipinin `Equals`/`GetHashCode` sözleşmesi doğru olmalı, (2) anahtar mutable olmamalı, (3) ilk kapasiteyi biliyorsan ver, (4) kötü hash ya da kasıtlı çakışma O(n)'e iter (string için .NET randomize hash kullanır).

**Mülakat cümlesi:** "Erişim kalıbına göre seçerim: sırayla gezme `List`/dizi, anahtarla arama `Dictionary`, üyelik testi `HashSet`, sıralı gezinme `SortedSet`, kuyruk `Queue` ya da `Channel`. Başlangıçta kurulup sonra salt okunan tablolar için `FrozenDictionary`."

#### ⭐ Q25. `IEnumerable<T>`, `ICollection<T>`, `IList<T>` ve `IQueryable<T>` farkı? Deferred execution nedir?

**Model cevap:** `IEnumerable<T>` yalnızca ileri yönlü gezme sunar. `ICollection<T>` `Count/Add/Remove`, `IList<T>` indeks erişimi ekler, `IReadOnlyList<T>` yalnızca okuma sözleşmesidir. `IQueryable<T>` ise **expression tree** taşır; EF Core bunu SQL'e çevirir. LINQ sorguları **ertelenmiş (deferred)** çalışır: `Where/Select` tanımlanınca değil, **gezilince** (`foreach`, `ToList`, `Count`...) çalışır.
- **Çoklu enumerasyon tuzağı:** `var q = db.Orders.Where(...); q.Count(); foreach (var o in q)` iki ayrı SQL demektir. Bir kez `ToList()` ile materialize et.
- `yield return` bir state machine üretir ve tembel akış sağlar.
- API tasarımı: parametrede en genel tipi (`IEnumerable<T>`), dönüşte en bilgili tipi (`IReadOnlyList<T>`) kullan. Repository'den `IQueryable` döndürmek sorgu mantığını katman dışına sızdırır.

```csharp
var xs = new List<int> { 1, 2, 3 };
var q = xs.Where(x => x > 1);     // henüz çalışmadı
xs.Add(4);
Console.WriteLine(q.Count());      // 3 (2, 3, 4): sorgu şimdi çalıştı
```

#### Q26. LINQ'de sık sorulanlar: `First` / `Single`, `Any` / `Count`, `Select` / `SelectMany`

**Model cevap:**
- `First()` boşsa fırlatır, `FirstOrDefault()` varsayılan döner. `Single()` **tam olarak bir** eleman bekler (0 ya da >1 ise fırlatır): benzersizlik varsayımını kodda ifade eder.
- `Any()` ilk eşleşmede durur. `Count() > 0` koleksiyon `ICollection` değilse tamamını gezer. `Any()` tercih et.
- `Select` bire-bir eşler, `SelectMany` iç içe koleksiyonları **düzleştirir**.
- `GroupBy` ertelenmiş, `ToLookup` hemen çalışır. Yeni operatörler: `DistinctBy`, `MaxBy`, `Chunk`, `Order()`, `CountBy`/`AggregateBy` (.NET 9).
- `OrderBy` kararlıdır (stable); `Intersect/Except/Distinct` hash tabanlıdır.
- **Performans:** Sıcak yolda LINQ closure ve enumerator allocation yaratır; gerekirse `for` döngüsü ya da `Span` ile yaz. Ölçmeden "LINQ yavaş" deme.

#### ⭐ Q27. `ConcurrentDictionary` neyi garanti eder, neyi etmez?

**Model cevap:** Her **tekil işlem** (`TryAdd`, `TryGetValue`, `TryUpdate`) atomiktir. Ama:
- `GetOrAdd(key, factory)`: factory **birden fazla thread'de çalışabilir**, yalnızca biri kazanır. Pahalı ya da yan etkili ise `Lazy<T>` ile sar ya da state'li overload kullan: `GetOrAdd(key, static (k, arg) => ..., arg)`.
- `AddOrUpdate(key, add, update)`: update delegesi çakışmada **yeniden çalışır**; yan etkisiz olmalı.
- Birden çok anahtarı ilgilendiren invariant'lar (A'dan çek, B'ye ekle) için atomiklik **yoktur**; lock ya da farklı tasarım gerekir.
- `decimal` bakiyede bileşik güncelleme için anahtar başına lock kullan ya da değeri minor unit `long` tutup `Interlocked.Add` kullan.
- Sıradan `Dictionary` thread-safe değildir; eşzamanlı yazma veri bozulması ve sonsuz döngü yaratabilir.
- `ConcurrentBag` nadiren doğru seçimdir. Üretici-tüketici için `Channel<T>` (§3.4).

### 2.5 Hata yönetimi ve kaynaklar

#### ⭐ Q28. `try/catch/finally` ve exception yönetimi: nelere dikkat edersin?

**Model cevap:**
- `finally` her koşulda çalışır (süreç ölümü, `Environment.FailFast` ve StackOverflow hariç).
- `throw;` stack trace'i **korur**, `throw ex;` sıfırlar. Sarmalarken orijinali `innerException` olarak ver.
- **Exception filter:** `catch (SqlException e) when (e.Number == 1205)`. Filtre stack çözülmeden önce çalışır, bu da tanılamayı kolaylaştırır.
- Spesifik yakala; `catch (Exception)` yalnızca sınır katmanında (middleware, worker döngüsü) ve **loglayarak** yapılır. Sessizce yutma.
- Exception pahalıdır (stack trace yakalama). Normal akış için kullanma: `TryParse`/`TryGetValue` desenini ya da `Result` tipini tercih et.
- `async`'te exception `Task` içinde taşınır, `await` ile yeniden fırlatılır. `Task.WhenAll` yalnızca ilk exception'ı fırlatır; hepsi için `task.Exception.InnerExceptions`'a bak. `.Wait()`/`.Result` ise `AggregateException` sarar.
- Yakalanamayanlar: `StackOverflowException`, `OutOfMemoryException` pratikte kurtarılamaz.
- ASP.NET Core'da merkezi işleme: `IExceptionHandler` + `UseExceptionHandler` ve `ProblemDetails` (§2.7).

#### ⭐ Q29. `IDisposable`, `using` ve `await using` ne işe yarar?

**Model cevap:** Deterministik kaynak serbest bırakma için. `using var conn = ...;` kapsam bitince `Dispose()` çağırır (exception olsa bile). Asenkron kaynaklarda `await using` ve `IAsyncDisposable`. Tam Dispose deseni (finalizer + `SuppressFinalize`) yalnızca **doğrudan unmanaged kaynak** sahibi sınıfta gerekir; çoğu zaman sadece `Dispose()` içinde sahip olduğun `IDisposable`'ları dispose etmen yeter.
- **DI notu:** Container, **kendi ürettiği** `IDisposable` servisleri scope sonunda dispose eder; `AddSingleton(instance)` ile **sen verdiğin nesneyi dispose etmez**.
- `HttpClient`'ı her istekte `new`'leme (socket tükenmesi), tekil statik de tutma (DNS değişimi); `IHttpClientFactory` kullan (Q42).
- Ayrıntı ve finalization: §3.2.

#### ⭐ Q30. Garbage Collector temelde nasıl çalışır? `GC.Collect()` çağırmalı mıyım?

**Model cevap (kısa sürüm; derinlik §3.2):** GC, erişilemeyen (root'lardan ulaşılamayan) yönetilen nesneleri otomatik temizler. **Nesil tabanlıdır:** çoğu nesne genç ölür, bu yüzden yeni nesneler **Gen0**'da toplanır (ucuz ve sık), hayatta kalanlar Gen1 → Gen2'ye terfi eder; Gen2 ("full GC") pahalıdır. **85.000 byte ve üstü** nesneler **LOH**'a gider ve Gen2 ile toplanır. GC yalnızca **bellek** içindir; dosya, soket, DB bağlantısı gibi kaynaklar için `Dispose`/`using` gerekir. Gen0/Gen1 toplamaları thread'leri **kısa süreliğine durdurur**; uygulamayla eşzamanlı çalışabilen yalnızca background Gen2 GC'dir.
- `GC.Collect()` çağırma: GC kendi sezgisinden daha iyi karar veremezsin, çoğu zaman gecikmeyi artırır. İstisna: ölçülmüş, nadir senaryolar (büyük tek seferlik işlem sonrası, test).
- **Bellek sızıntısı** .NET'te "referans tutulmaya devam ediyor" demektir: static event, sınırsız cache/dictionary, çıkılmayan event aboneliği, singleton'ın yakaladığı scoped nesne.
- Finalizer (`~T`) yalnızca doğrudan unmanaged handle sahibi sınıflarda yazılır; nesneyi bir GC daha yaşatır.

### 2.6 Eşzamanlılık temelleri

#### ⭐ Q31. `Thread`, `Task`, `ThreadPool` ve `Parallel` farkı nedir? I/O-bound ile CPU-bound?

**Model cevap:** `Thread` bir işletim sistemi thread'idir (pahalı, MB'lar mertebesinde stack). `ThreadPool` thread'leri yeniden kullanır. `Task` ise bir işin **temsilidir (promise)**; bir thread'e bağlı olmak zorunda değildir. I/O tabanlı Task'lar (ağ, disk, DB) bekleme sırasında **hiç thread tutmaz**.
- **I/O-bound** iş: `async/await`. Thread bekleyerek harcanmaz, ölçeklenebilirlik artar.
- **CPU-bound** iş: gerçek paralellik gerekir: `Task.Run` (UI'ı/istek thread'ini serbest bırakmak için), `Parallel.For/ForEach/ForEachAsync`, PLINQ.
- *Concurrency* (aynı anda birden çok işle ilgilenmek) ile *parallelism* (aynı anda birden çok işi yürütmek) farklıdır.
- ASP.NET Core'da `Task.Run` ile istek içinde iş kaydırmak çoğu zaman anti-pattern'dir (aynı havuzdan thread alırsın).

#### ⭐ Q32. `async/await` kullanırken en sık yapılan hatalar nedir?

**Model cevap:**
1. **`.Result` / `.Wait()`** (sync-over-async): thread pool starvation; UI/legacy ortamda deadlock. Çözüm: "async all the way".
2. **`async void`:** exception yakalanamaz ve süreci düşürebilir; yalnızca event handler'larda.
3. **Fire-and-forget:** `_ = DoAsync();` exception'ı yutar. Arka plan işi için `Channel` + `BackgroundService` ya da bir kuyruk kullan.
4. **`CancellationToken`'ı geçirmemek:** istek iptal olsa da iş sürer.
5. **`Task.Run` ile async'i sarmak** gereksiz thread atlaması yaratır.
6. **`Thread.Sleep`** yerine `await Task.Delay`.
7. **Paralel başlatmak yerine sırayla await etmek:** bağımsız işleri `Task.WhenAll` ile birlikte bekle.
- Tuzak: `await` bir thread oluşturmaz; yalnızca devamı (continuation) kaydeder. Detay: §3.3.

#### ⭐ Q33. Race condition nedir? `lock`, `Interlocked`, `volatile`, `SemaphoreSlim`, deadlock?

**Model cevap:** *Race condition*, sonucun thread zamanlamasına bağlı olduğu durumdur. `count++` atomik değildir (oku-artır-yaz); düzeltmek için `Interlocked.Increment(ref count)` ya da `lock`.
- `lock (obj)` Monitor kullanır. **`this`, `typeof(...)` ve string üzerinde lock alma**; özel bir `readonly` nesne kullan. **.NET 9 / C# 13:** `private readonly Lock _gate = new();` (`System.Threading.Lock`) daha hızlı ve niyeti net.
- `lock` içinde `await` **yapılamaz** (Monitor'u alan thread bırakmalı, devam başka thread'de olabilir; derleyici CS1996 verir). Asenkron kritik bölge için `SemaphoreSlim(1,1)` ve `await WaitAsync()`.
- `volatile`: tek bir alanın görünürlük/sıralama garantisi; bileşik işlemleri atomik yapmaz.
- `ReaderWriterLockSlim`: okuma çoğunluklu durumlar. `Mutex`: süreçler arası.
- **Deadlock** dört koşulla oluşur (karşılıklı dışlama, tutup bekleme, kesintisiz tutma, döngüsel bekleme). Önlemek için kilitleri **hep aynı sırayla** al, kilit altında dış çağrı yapma, zaman aşımı kullan.

#### Q34. `Task.WhenAll` / `WhenAny`, `CancellationToken` ve zaman aşımı nasıl kullanılır?

**Model cevap:** `await Task.WhenAll(tasks)` bağımsız işleri birlikte bekler. `WhenAny` zaman aşımı ya da "hedging" (ilk gelen cevabı al) için. `Task.WaitAsync(timeout, ct)` (.NET 6) ile herhangi bir task'a zaman aşımı eklenir. **İptal işbirlikçidir:** `CancellationTokenSource` token üretir, kod `token.ThrowIfCancellationRequested()` ya da token'ı alt çağrılara geçirerek yanıt verir; `OperationCanceledException` normal bir sonuç sayılır. ASP.NET Core'da `HttpContext.RequestAborted` zincirin başına bağlanır. Periyodik iş için `PeriodicTimer` (.NET 6).

### 2.7 ASP.NET Core ve platform temelleri

#### ⭐ Q35. ASP.NET Core istek hattı nasıl çalışır? Middleware sırası neden önemli?

**Model cevap:** İstek **Kestrel**'e düşer, **middleware zincirinden** geçer, routing ile **endpoint** seçilir, controller'larda **filter pipeline** çalışır, cevap zincirden geri döner. Her middleware `next()` çağırarak devam eder ya da çağırmayıp kısa devre yapar (Chain of Responsibility).

Tipik sıra: `ExceptionHandler → HSTS/HTTPS → StaticFiles → Routing → CORS → Authentication → Authorization → RateLimiter → Endpoints`. Sıra önemli çünkü örneğin Authorization'dan önce Authentication çalışmazsa kimlik yoktur; CORS yanlış yerdeyse preflight reddedilir; ExceptionHandler en dışta değilse hatalar yakalanmaz.
- `WebApplication.CreateBuilder` ile minimal hosting modeli; arkada **Generic Host** (`IHostedService`, DI, config, logging).
- Üretimde Kestrel genelde bir reverse proxy (nginx, ALB, YARP) arkasındadır; `ForwardedHeaders` middleware'i unutulmamalı.

#### ⭐ Q36. Dependency Injection neden kullanılır? Lifetime'lar ve sık hatalar nedir?

**Model cevap:** Bağımlılıkları dışarıdan vermek **gevşek bağlılık, test edilebilirlik ve yaşam süresi yönetimi** sağlar; nesne grafiğini tek yerde (composition root, `Program.cs`) kurarsın.
- **Singleton:** uygulama boyunca tek, thread-safe olmalı. **Scoped:** istek başına (`DbContext`). **Transient:** her çözümlemede yeni.
- **Captive dependency:** Singleton'ın scoped servis yakalaması. `ValidateScopes`/`ValidateOnBuild` yakalar (Development'ta varsayılan açık). Arka plan servisinde `IServiceScopeFactory` ile scope aç.
- **Service locator anti-pattern:** iş mantığında `IServiceProvider.GetService` çağırmak bağımlılıkları gizler.
- Çoklu implementasyon: `IEnumerable<T>` enjekte et. **Keyed services** (.NET 8): `[FromKeyedServices("teltonika")]`. Açık generic kayıt: `AddScoped(typeof(IRepo<>), typeof(Repo<>))`.
- Yerleşik container property injection, decorator ve interception sunmaz; gerekirse Scrutor ya da Autofac.

#### Q37. Konfigürasyon ve Options deseni nasıl çalışır?

**Model cevap:** Kaynaklar sırayla üst üste biner (sonraki kazanır): `appsettings.json` < `appsettings.{Environment}.json` < **user secrets** (yalnızca Development) < **ortam değişkenleri** (`Section__Key`) < komut satırı. Tipli okuma için **Options**:
- `IOptions<T>`: singleton, başlangıçta okunur, değişikliği görmez.
- `IOptionsSnapshot<T>`: scoped, istek başına yeniden okunur.
- `IOptionsMonitor<T>`: singleton, değişiklik bildirimi verir.
- Doğrulama: `AddOptions<T>().BindConfiguration("X").ValidateDataAnnotations().ValidateOnStart()`. Hatalı konfigürasyonla uygulama **açılışta** düşer, üretimde ilk istekte değil.
- Sırlar repoya girmez: user-secrets (dev), AWS Secrets Manager/Parameter Store (prod).

#### ⭐ Q38. Authentication ile authorization farkı? JWT, OIDC ve policy tabanlı yetkilendirme?

**Model cevap:** *Authentication* "kimsin?", *authorization* "ne yapabilirsin?". ASP.NET Core'da `AddAuthentication().AddJwtBearer(...)` ile token doğrulanır: **imza, issuer, audience, süre (clock skew dahil)**. Yetki; rol, claim ya da **policy** ile verilir: `[Authorize(Policy = "FleetAdmin")]`, karmaşık kurallarda `IAuthorizationHandler`, nesneye bağlı kararda **resource-based authorization**.
- SPA ve mobilde akış: **OIDC authorization code + PKCE** (implicit flow kullanılmaz). Sağlayıcı: Cognito, Entra ID, Keycloak.
- SPA'da token'ı `localStorage`'a koyma; mümkünse **BFF + httpOnly cookie**. Mobilde Keychain/Keystore.
- Çok kiracılı sistemde `tenant_id` claim'i ve veri katmanında filtre/RLS (§9.10).
- Refresh token rotasyonu, kısa ömürlü access token.

#### ⭐ Q39. REST tasarımında nelere dikkat edersin? Idempotency, status code, sayfalama?

**Model cevap:**
- **Metotlar:** GET güvenli ve idempotent; PUT (tam değiştirme) ve DELETE idempotent; PATCH kısmi güncelleme; POST idempotent değil. Idempotent olmayan kritik işlemlerde (ödeme, **cihaza komut**) **`Idempotency-Key`** başlığı ve sunucuda tekrar kontrolü.
- **Status code:** 200, 201 (+`Location`), 202 (kabul edildi, asenkron), 204, 400 (doğrulama), 401 (kimlik yok), 403 (yetki yok), 404, 409 (çakışma), 412 (ETag/If-Match uyuşmazlığı), 422, 429 (+`Retry-After`), 5xx.
- **Hata gövdesi:** `ProblemDetails` (RFC 9457; 7807'nin yerine geçti).
- **Sayfalama:** `OFFSET` büyük sayfalarda yavaşlar ve kayar; **keyset/cursor** (`WHERE (device_id, ts) < (@d, @t) ORDER BY ... LIMIT n`) ölçeklenir. Telemetri ve loglar için keyset şarttır.
- **Eşzamanlılık:** `ETag` + `If-Match`. **Sürümleme:** URL ya da header. **OpenAPI** ile sözleşme.

#### Q40. Doğrulama, filter'lar, hata yönetimi ve health check?

**Model cevap:**
- `[ApiController]` model doğrulama hatalarında otomatik 400 üretir. DataAnnotations basit kurallar için; karmaşık kurallar için FluentValidation. **.NET 10**'da minimal API için yerleşik doğrulama geldi.
- Controller filter sırası: Authorization → Resource → Action → Exception → Result.
- Merkezi hata: `builder.Services.AddExceptionHandler<GlobalExceptionHandler>()` + `AddProblemDetails()` + `app.UseExceptionHandler()` (.NET 8).
- **Health check:** `/health/live` (süreç ayakta mı) ve `/health/ready` (bağımlılıklar hazır mı). Kubernetes/ECS probe'ları bunlara bağlanır.
- Hız sınırlama: yerleşik `RateLimiter` (fixed/sliding window, token bucket, concurrency). Çıktı önbelleği: `OutputCache`.

#### ⭐ Q41. EF Core temelleri: DbContext, change tracking, loading stratejileri, EF mi Dapper mı?

**Model cevap:** `DbContext` bir **unit of work + identity map**'tir; **scoped** olmalı ve kısa ömürlü olmalı. Change tracker değişiklikleri izler, `SaveChanges` hepsini **tek transaction**'da yazar.
- **Yükleme:** Eager (`Include`), explicit, lazy (proxy; N+1 riski nedeniyle genelde kapalı). Okuma sorgusunda `AsNoTracking()`; çoğu zaman `Select` ile DTO projeksiyonu en iyisidir.
- **Migrations:** Üretimde idempotent script ya da bundle; açılışta otomatik `Migrate()` çok instance'ta yarış yaratır.
- **Güvenlik:** `FromSql` interpolasyonu parametreleştirir; string birleştirme SQL injection'dır.
- **Concurrency:** Concurrency token (Npgsql'de `[Timestamp] uint Version` → `xmin`). Ayrıntı §5.3.
- **EF vs Dapper:** CRUD ve domain modelinde EF; sıcak okuma yolları ve toplu işlerde Dapper ya da ham SQL/`COPY`. İkisini karışık kullanmak normaldir.
- Detay: §3.6.

#### Q42. `HttpClient` doğru nasıl kullanılır? Polly ve resilience?

**Model cevap:** Her istekte `new HttpClient()` **socket tükenmesi** (TIME_WAIT) yaratır; sonsuza kadar tek statik örnek ise **DNS değişimini** göremez. Çözüm `IHttpClientFactory` (named/typed client) ya da `SocketsHttpHandler.PooledConnectionLifetime` ayarı. Dayanıklılık için `Microsoft.Extensions.Http.Resilience` (`AddStandardResilienceHandler`: rate limiter, toplam zaman aşımı, retry + jitter, circuit breaker, deneme zaman aşımı). Retry yalnızca **idempotent** çağrılarda. Streaming için `HttpCompletionOption.ResponseHeadersRead`. Her çağrıya `CancellationToken` ver.

### 2.8 "Bu kodun çıktısı nedir?" soruları

Her biri 20-30 saniyelik bir cevap gerektirir. Önce çıktıyı söyle, sonra nedenini tek cümleyle açıkla.

```csharp
// 1) Struct kopya semantiği
struct P { public int X; }
var p = new P { X = 1 }; var q = p; q.X = 2;
Console.WriteLine(p.X);                  // 1: struct kopyalandı

var list = new List<P> { p };
// list[0].X = 5;                         // derleme hatası CS1612: indeksleyici kopya döner
```

```csharp
// 2) string değişmezdir
var s = "abc"; s.ToUpper(); Console.WriteLine(s);   // abc

// 3) object üzerinden == referans karşılaştırır
string a = "hi"; object b = new string(new[] { 'h', 'i' });
Console.WriteLine(a == b);               // False (statik tip object: referans)
Console.WriteLine(a.Equals(b));          // True
```

```csharp
// 4) Taşma ve kayan nokta
int max = int.MaxValue; max++;           // unchecked: -2147483648 (checked ile OverflowException)
Console.WriteLine(0.1 + 0.2 == 0.3);     // False; para için decimal, GPS için double, eşitlik için epsilon
Console.WriteLine(Math.Round(2.5m));     // 2: varsayılan "banker's rounding" (ToEven)
Console.WriteLine(Math.Round(2.5m, MidpointRounding.AwayFromZero)); // 3
```

```csharp
// 5) async metotta exception ne zaman görünür?
async Task Boom() { await Task.Delay(1); throw new Exception("x"); }
Boom();                                  // fırlatılmaz, gözlemlenmez: fire-and-forget
await Boom();                            // burada fırlar
```

```csharp
// 6) Koleksiyonu gezerken değiştirmek
foreach (var x in xs) if (x == 2) xs.Remove(x);   // InvalidOperationException: Collection was modified
xs.RemoveAll(x => x == 2);                         // doğru yol

// 7) Mutable anahtar
var k = new Key { Id = 1 }; var d = new Dictionary<Key, int> { [k] = 1 };
k.Id = 2;                                // GetHashCode değişti
Console.WriteLine(d.ContainsKey(k));     // False: kayıt "kayboldu"
```

```csharp
// 8) Generic'te static alan, kapalı tip başına ayrıdır
class Counter<T> { public static int N; }
Counter<int>.N++; Counter<string>.N++;
Console.WriteLine(Counter<int>.N);       // 1
```

**Zaman ve saat diliminde sık sorulan:** `DateTime.Now` yerel saati, `DateTime.UtcNow` UTC'yi verir; `DateTime.Kind` serileştirmede kaybolabilir. Sunucuda ve veritabanında **UTC** (`timestamptz`) sakla, `DateTimeOffset` kullan, saat dilimini yalnızca gösterimde uygula. Test için `TimeProvider` (.NET 8) ile saati soyutla. Türkiye 2016'dan beri sabit UTC+3 kullanıyor; eski verilerde tz veritabanı kurallarına dikkat (telematikte geçmiş kayıtlar!).

### 2.9 Live coding'de sık çıkan küçük görevler

Senaryolar: LRU cache, rate limiter, iki listenin kesişimi (§5.1), tekrarlayanları bulma, grupla ve ilk N'i al, CSV ayrıştırma, bir üretici-tüketici hattı (§3.4), thread-safe sayaç. Kural: **önce basit ve doğru çözümü yaz, sonra karmaşıklığı ve eşzamanlılığı konuş**.

**LRU cache (O(1) okuma/yazma, thread-safe):**

```csharp
public sealed class LruCache<TKey, TValue> where TKey : notnull
{
    private readonly int _capacity;
    private readonly Dictionary<TKey, LinkedListNode<(TKey Key, TValue Value)>> _map;
    private readonly LinkedList<(TKey Key, TValue Value)> _order = new(); // baş = en yeni
    private readonly Lock _gate = new();                                   // .NET 9+; eski sürümde object

    public LruCache(int capacity) { _capacity = capacity; _map = new(capacity); }

    public bool TryGet(TKey key, out TValue value)
    {
        lock (_gate)                                  // okuma sırayı değiştirdiği için kilit gerekir
        {
            if (_map.TryGetValue(key, out var node))
            {
                _order.Remove(node); _order.AddFirst(node);
                value = node.Value.Value; return true;
            }
            value = default!; return false;
        }
    }

    public void Set(TKey key, TValue value)
    {
        lock (_gate)
        {
            if (_map.TryGetValue(key, out var node))
            {
                _order.Remove(node);
                node.Value = (key, value);
                _order.AddFirst(node);
                return;
            }
            if (_map.Count >= _capacity)
            {
                var last = _order.Last!;
                _map.Remove(last.Value.Key);
                _order.RemoveLast();
            }
            _map[key] = _order.AddFirst((key, value));
        }
    }
}
```

Konuşulacaklar: tek kilit sıcak anahtarlarda çekişme yaratır → parçalı (sharded) LRU ya da yaklaşık politikalar (CLOCK, TinyLFU); hazır çözümler `MemoryCache` (`SizeLimit`) ve BitFaster.Caching; TTL ve boyut sınırı; dağıtık cache için §5.5.

**Token bucket rate limiter (tek süreç):**

```csharp
public sealed class TokenBucket(int capacity, double refillPerSecond, TimeProvider? time = null)
{
    private readonly TimeProvider _time = time ?? TimeProvider.System;
    private readonly Lock _gate = new();
    private double _tokens = capacity;
    private long _last = (time ?? TimeProvider.System).GetTimestamp();

    public bool TryTake(int count = 1)
    {
        lock (_gate)
        {
            var now = _time.GetTimestamp();
            var elapsed = _time.GetElapsedTime(_last, now).TotalSeconds;
            _last = now;
            _tokens = Math.Min(capacity, _tokens + elapsed * refillPerSecond);
            if (_tokens < count) return false;
            _tokens -= count; return true;
        }
    }
}
```

Konuşulacaklar: yerleşik `System.Threading.RateLimiting` (`TokenBucketRateLimiter`) varken neden yazıyoruz; çok instance için **Redis + Lua** (atomik) ya da API gateway; anahtar başına limiter ve bellek temizliği; `TimeProvider` ile test edilebilirlik.

**Küçük görev kalıpları:**
- Tekrarlayanları bulma: `HashSet.Add` dönüş değeri, O(n). Sıralı iki-işaretçi alternatifi O(n log n) ve O(1) ek bellek.
- Grupla ve ilk N: `GroupBy(...).Select(g => new { g.Key, Total = g.Sum(...) }).OrderByDescending(...).Take(n)`; çok büyük veride `PriorityQueue` ile O(n log k).
- Akış halinde ortalama/min/max: tek geçiş, O(1) bellek; pencereli hesapta halka tampon (ring buffer).


## 3. .NET / .NET Core ve Advanced .NET

### 3.1 Reflection

**S: Reflection'ı nerede kullanırsınız, maliyeti nedir, nasıl azaltırsınız?**
Model cevap: Reflection çalışma zamanında metadata okur ve üyeleri dinamik çağırır. Kullanıldığı yerler: DI container'lar, serializer'lar, ORM'ler (EF Core), plugin yükleme, attribute tabanlı konvansiyonlar (ör. `[Authorize]`, validation), test framework'leri. Maliyetin kaynakları şunlar: `MethodInfo.Invoke` argümanları `object[]` olarak boxing ile taşır, erişim kontrolü yapar ve JIT inlining'i engeller. Ayrıca trimming ve Native AOT ile uyumsuzdur, çünkü kullanılmayan tipler kırpılabilir. Azaltmak için şu sırayla giderim:
1. **Cache:** `PropertyInfo` ve `MethodInfo` nesnelerini bir `ConcurrentDictionary<Type, …>` içinde tutarım.
2. **Delegate'e çevirme:** `MethodInfo.CreateDelegate<Func<T,TResult>>()` ile çağrı neredeyse doğrudan çağrı hızına iner.
3. **Expression trees:** `Expression.Lambda(...).Compile()` ile getter/setter üretirim. Compile maliyeti bir kez ödenir, sonrası hızlıdır.
4. **Source generators:** Kodu derleme zamanında üretirim. Runtime maliyeti sıfırdır ve AOT dostudur. Örnekler: System.Text.Json source generation (`JsonSerializerContext`), `LoggerMessage` attribute'u, Regex source generator, minimal API'lerde Request Delegate Generator.

```csharp
// Expression tree ile hızlı property getter
static Func<object, object?> BuildGetter(PropertyInfo p)
{
    var obj = Expression.Parameter(typeof(object));
    var body = Expression.Convert(
        Expression.Property(Expression.Convert(obj, p.DeclaringType!), p), typeof(object));
    return Expression.Lambda<Func<object, object?>>(body, obj).Compile();
}
```

**Takip soruları:** "Native AOT'de reflection neden sorun?" Kırpılan tipler yüzünden. `[DynamicallyAccessedMembers]` ile işaretlenir, trim uyarıları sıfırlanır. "Attribute'ları ne zaman okursunuz?" Startup'ta bir kez okunur, sonuç cache'lenir.
**Sık hata:** Hot path'te, yani her telemetri paketinde reflection ile mapping yapmak. Telemetri parser'ı reflection'sız, elle yazılmış ya da generator ile üretilmiş olmalı.

### 3.2 Garbage Collection

**S: .NET GC nasıl çalışır?**
Model cevap: Generational ve mark-compact çalışır. Varsayım şudur: çoğu nesne genç ölür.
- **Gen0:** Yeni nesneler buraya düşer. Toplanması ucuz ve sıktır.
- **Gen1:** Gen0 ile Gen2 arasında tampon görevi görür.
- **Gen2:** Uzun yaşayan nesneler. Full GC pahalıdır.
- **LOH:** 85.000 byte ve üstü nesneler buraya gider. Mantıksal olarak Gen2 ile birlikte toplanır, varsayılan olarak compact edilmez, bu yüzden fragmentation riski taşır.
- **POH (.NET 5+):** Pinned nesneler için ayrı heap. Pinning'in fragmentation etkisini azaltır.

**Modlar:**
- **Workstation GC:** Tek heap, düşük bellek kullanır. Masaüstü ve küçük container'lar için uygundur.
- **Server GC:** Çekirdek başına heap ve GC thread'i. Throughput odaklıdır, ASP.NET Core'da varsayılan.
- **Background (concurrent) GC:** Gen2'yi uygulama thread'leri çalışırken arka planda toplar. **Eşzamanlı çalışabilen yalnızca background Gen2 GC'dir;** Gen0/Gen1 toplamaları thread'leri kısa süreliğine **durdurur** ("minor GC uygulamayı hiç durdurmaz" demeyin; çok kısadır, ama blocking'dir).
- **DATAS:** .NET 9'dan beri Server GC'de varsayılan. Heap sayısını yüke göre büyütüp küçültür. Heap boyutu, makinenin çekirdek sayısına değil uzun yaşayan veri miktarına orantılı olur. Bunun bedeli daha fazla Gen0/Gen1 toplamasıdır. Throughput regresyonu görürseniz `System.GC.DynamicAdaptationMode=0` ile kapatıp yük testini tekrarlarsınız.
- Diğer ayarlar: `GCHeapHardLimitPercent`, `GCConserveMemory`, `GCSettings.LatencyMode`, `SustainedLowLatency`.

**Finalization ve IDisposable:**
- Finalizer'lı nesneler finalization queue'ya girer ve en az bir GC daha yaşar. Bu yüzden finalizer yalnızca unmanaged handle sahibi sınıflarda yazılmalıdır, o da tercihen `SafeHandle` ile.
- Dispose pattern: `Dispose()` → `Dispose(true)` → `GC.SuppressFinalize(this)`. Async kaynaklar için `IAsyncDisposable`, `await using` ile kullanılır.

**Memory leak senaryoları (telematik bağlamında):**
- Static event handler'a abone olup hiç çıkmamak, örneğin cihaz bağlantı nesnesinin global bir event'e takılı kalması.
- Sınırsız `ConcurrentDictionary<imei, state>`. Kopan cihazlar silinmezse sürekli büyür.
- `IMemoryCache`'te boyut limiti ve expiration tanımlamamak.
- Singleton servisin scoped `DbContext` tutması (captive dependency).
- `HttpClient`'ı her istekte yeniden yaratmak. Bu socket tükenmesine yol açar; `IHttpClientFactory` kullanılmalı.
- Timer'ları dispose etmemek.
- Closure'ların büyük nesneleri yakalaması.

**Allocation azaltma araçları:**
- **`Span<T>` / `ReadOnlySpan<T>`:** Stack-only `ref struct`. Kopyalamadan dilimleme yapar ve `await` sınırını geçemez.
- **`Memory<T>`:** Heap'te saklanabilir, async kodda kullanılır.
- **`ArrayPool<T>.Shared.Rent/Return`:** Buffer'ları yeniden kullanır. `Return`'ü `finally` içinde çağırın; aynı buffer'ı iki kez iade etmeyin.
- **`stackalloc`:** Küçük, sabit boyutlu buffer'lar için.
- **struct vs class:** 16 byte civarı ve altındaki, immutable, değer semantiğine sahip tipler için `readonly struct` uygundur, örneğin `GeoPoint(lat, lon)`. Büyük struct'lar kopyalama maliyeti doğurur; `in`/`ref readonly` parametre kullanın. **"Struct stack'te yaşar, GC radarına girmez" doğru değildir** (stack/heap yeri bir uygulama detayıdır, Q4): sınıfın alanıysa, dizi elemanıysa, boxed/yakalanmışsa heap'tedir; içinde referans (`string`) taşıyan struct'ı GC yine tarar. Gerçek kazanç: `List<struct>` tek bir yedek dizidir (1 milyon ayrı nesne yerine), bellek ardışıktır (CPU cache locality). Telemetri struct'ında IMEI'yi `string` yerine `long` tutun; 15-20 alanlı "şişman struct" için önce class düşünün, struct şartsa `readonly struct` + `in`. GC her seferinde bütün heap'i taramaz (nesil tabanlıdır).
- **Boxing:** Struct'ı `object`'e ya da interface'e atarken oluşur. Generic constraint'ler ve `IEquatable<T>` ile önlenir.
- Diğer: `ValueTask`, string yerine `ReadOnlySpan<char>` ve `SearchValues`, `StringBuilder` pooling.

**Profiling araçları:**
- `dotnet-counters` (GC heap, gen sayıları, allocation rate, thread pool queue)
- `dotnet-trace` ve PerfView
- `dotnet-gcdump` / `dotnet-dump` (`dumpheap -stat`, `gcroot`)
- Visual Studio Diagnostic Tools, JetBrains dotMemory/dotTrace
- BenchmarkDotNet ve `[MemoryDiagnoser]`

**Takip soruları:** "Gen2 GC sayısı artıyor, ne yaparsınız?" Önce `dotnet-counters` ile allocation rate'e bakarım. Ardından `gcdump` alıp en büyük tipleri ve onları tutan root'ları (gcroot) bulurum. Mid-life crisis'e bakarım: Gen1'e taşınıp Gen2'de ölen nesneler. Cache'lere ve LOH allocation'larına bakarım; büyük `byte[]` için ArrayPool kullanırım. "LOH'u compact edebilir misiniz?" Evet, tek seferlik `GCSettings.LargeObjectHeapCompactionMode = CompactOnce` ile.
**Sık hata:** `GC.Collect()` çağırmak. Finalizer içinde managed nesnelere erişmek. Pod'un memory limit'ini bilmeden Server GC kullanmak.

### 3.3 async/await, Task Internals, Thread Pool

**S: async/await derleyici tarafında ne üretir?**
Model cevap: Derleyici metodu bir state machine'e (`IAsyncStateMachine`) dönüştürür. Her `await` noktası bir state'tir. Awaiter tamamlanmamışsa continuation kaydedilir ve thread serbest kalır. Tamamlanınca `SynchronizationContext` ya da TaskScheduler üzerinden devam edilir. ASP.NET Core'da `SynchronizationContext` yoktur; continuation thread pool'da çalışır.
- **`ConfigureAwait(false)`:** Library kodunda yakalanan context'e dönmeyi engeller. ASP.NET Core uygulama kodunda pratik etkisi azdır, ama UI veya legacy host'larda deadlock'u önler.
- **State machine bir `struct`'tır** (Release derlemesinde); metot **gerçekten askıya alınırsa** heap'e boxlanır, senkron tamamlanırsa allocation yoktur. "async = her zaman allocation" demeyin.
- **`ValueTask`:** Çoğunlukla senkron tamamlanan sıcak yollarda allocation'ı önler. Kuralı: yalnızca bir kez await edilir. Uygulama kodunda `Task`, kütüphane/sıcak yolda `ValueTask`.
- **Thread pool:** Worker ve IOCP thread'lerinden oluşur. Hill-climbing algoritmasıyla thread ekler. `.Result` ya da `.Wait()` ile sync-over-async yapılırsa thread pool starvation oluşur: thread'ler bloklanır, yeni thread yavaş eklenir, latency patlar.
- **.NET 11 notu:** "Runtime Async" (async'in derleyici state machine'i yerine runtime tarafından yönetilmesi) .NET 11 ile geliyor; GA'da davranışını doğrulayın. Söyleyebileceğiniz: gelecekte state machine yükünün azalması hedefleniyor.

**Takip soruları:** "`async void` ne zaman kabul edilir?" Yalnızca event handler'larda, çünkü exception'lar yakalanamaz. "`Task.Run` ASP.NET Core'da?" CPU-bound işi offload etmek dışında genelde anti-pattern'dir. "CancellationToken'ı nasıl yayarsınız?" `HttpContext.RequestAborted` → servis → EF/HttpClient zinciriyle.

### 3.4 Channels ve Producer/Consumer

**S: TCP gateway'den gelen paketleri işleme katmanına nasıl aktarırsınız?**
Model cevap: `System.Threading.Channels` ile bounded bir channel kurarım. `BoundedChannelOptions(capacity) { FullMode = Wait }` ayarı doğal backpressure sağlar: channel dolarsa producer'ın `WriteAsync`'i bekler, bu da socket okumayı yavaşlatır ve TCP flow control devreye girer. Ölçülebilir veri kaybı kabul edilen durumlarda `DropOldest` seçilebilir, örneğin canlı harita konumunda yalnızca son konum önemliyse. Consumer tarafında `ReadAllAsync` ile batch'leme yaparım (ör. 500 kayıt ya da 200 ms), sonra `COPY` ile toplu yazarım.

```csharp
var ch = Channel.CreateBounded<AvlRecord>(new BoundedChannelOptions(50_000)
{ FullMode = BoundedChannelFullMode.Wait, SingleReader = false });

// consumer: batch + flush
await foreach (var rec in ch.Reader.ReadAllAsync(ct))
{
    batch.Add(rec);
    if (batch.Count >= 500 || sw.ElapsedMilliseconds > 200) { await FlushAsync(batch, ct); batch.Clear(); sw.Restart(); }
}
```

**Sık hata:** Unbounded channel kullanmak. Bu, bellekte sessizce birikip OOM'a giden yoldur. Bir başka hata, per-device sırayı korumadan birden fazla consumer çalıştırmak. Çözüm: IMEI hash'ine göre partition'lanmış N channel.

### 3.5 ASP.NET Core: Minimal API vs Controllers, Middleware, DI

*(Temel sorular — pipeline, DI lifetime'ları, Options, auth, REST, EF temelleri — §2.7'de; burası ileri düzey özettir.)*

- **Minimal API:** Daha az seremoni gerektirir, AOT ve Request Delegate Generator ile uyumludur, route group'lar ve endpoint filter'lar sunar. .NET 10'da OpenAPI desteği ve minimal API validation geliştirildi.
- **Controllers:** Filter'lar, model binding konvansiyonları ve büyük ekiplerde yerleşik yapı açısından hâlâ geçerli.
- Benim tercihim: yeni ve dar servislerde minimal API, büyük legacy API'de controllers. Karışık kullanmak da mümkün.
- **Middleware pipeline:** Bir Chain of Responsibility'dir. Sıra önemlidir: ExceptionHandler → HSTS/HTTPS → Static → Routing → CORS → Authentication → Authorization → RateLimiter → Endpoint. Her middleware `next()`'i çağırıp çağırmamaya karar verir (short-circuit).
- **DI lifetime'ları:**
  - **Singleton:** Uygulama boyunca tek instance. Thread-safe olmalı.
  - **Scoped:** HTTP isteği başına bir instance. `DbContext` scoped olmalı.
  - **Transient:** Her resolve'da yeni instance.
  - **Captive dependency:** Singleton içine scoped enjekte etmek. `ValidateScopes` bunu yakalar. Background service içinde `IServiceScopeFactory.CreateScope()` kullanılır.
  - .NET 8'den beri keyed services var: `[FromKeyedServices("teltonika")]` ile protokol parser stratejisi seçilebilir.

**Takip sorusu:** "`IHostedService` içinde DbContext nasıl kullanılır?" Her iş birimi için yeni bir scope açılır.

### 3.6 EF Core ve PostgreSQL / Npgsql

**S: EF Core performans sorunlarını nasıl çözersiniz?**
- **Change tracking:** Okuma sorgularında `AsNoTracking()` ya da `AsNoTrackingWithIdentityResolution()` kullanırım. Büyük toplu işlerde `ChangeTracker.AutoDetectChangesEnabled=false`.
- **N+1:** Lazy loading kapalı tutulur. `Include`/`ThenInclude` ya da projection (`Select`) kullanılır. Kartezyen patlamaya karşı `AsSplitQuery()` (EF 10'da split query sıralaması daha tutarlı hale geldi).
- **Bulk işlemler:** `ExecuteUpdateAsync`/`ExecuteDeleteAsync` (EF 7+). EF 10'da delegate tabanlı setter'lar ve JSON kolonlarında ExecuteUpdate desteği geldi. Telemetri gibi çok yüksek hacimde Npgsql binary `COPY` (`BeginBinaryImport`) kullanırım. EF'in SaveChanges'ı bu iş için uygun değil.
- **Compiled queries:** `EF.CompileAsyncQuery` sıcak yollarda LINQ çeviri maliyetini atlar. DbContext pooling için `AddDbContextPool`.
- **Migrations:** Production'da `dotnet ef migrations script --idempotent` ya da migration bundle kullanılır. Uygulama açılışında otomatik `Migrate()` çalıştırmak çok instance'lı ortamda yarış durumu yaratır. Uzun süren index'ler `CREATE INDEX CONCURRENTLY` ile raw SQL migration olarak yazılır.
- **Npgsql/PostgreSQL özellikleri:** `jsonb` mapping, array tipleri, `NodaTime` entegrasyonu, enum mapping, PostGIS için `NetTopologySuite` (`Point`, `ST_DWithin`), `xmin` ile optimistic concurrency, `Multiplexing`, connection pooling (PgBouncer ile transaction mode'da prepared statement'lara dikkat).
- **EF Core 10 yenilikleri:** `LeftJoin`/`RightJoin` LINQ operatörleri, adlandırılmış (named) global query filter'lar (ör. "Tenant" ve "SoftDelete" ayrı ayrı kapatılabilir; multi-tenant için ideal), complex type'ların JSON kolona map'lenmesi (struct desteği dahil), vector search.

**Sık hata:** `ToList()` çağrısını filtreden önce yapmak (client evaluation). Uzun ömürlü DbContext kullanmak. Telemetriyi EF entity'leri üzerinden satır satır insert etmek.

### 3.7 Performans ve Gözlemlenebilirlik (OpenTelemetry)

- **Logging:** `ILogger`, structured logging (message template), yüksek frekansta `LoggerMessage` source generator, Serilog sink'leri. Hot path'te string interpolation yapmayın.
- **Metrics:** `System.Diagnostics.Metrics` (`Meter`, `Counter`, `Histogram`). Örnek: `telemetry.packets.received{protocol}`, `ingest.lag.seconds`.
- **Tracing:** `ActivitySource`. W3C `traceparent` ile servisler arası propagation (varsayılan format .NET 5'ten beri W3C). Kuyruk mesajlarına trace context'i header olarak koyun. `Activity`/`Meter`/`ILogger` API'leri çatıda yerleşiktir; **OpenTelemetry SDK ve exporter'lar NuGet paketidir.** Ayrıntı ve soru-cevap: §6.3.
- **OpenTelemetry:** `AddOpenTelemetry().WithTracing().WithMetrics()`, OTLP exporter → Collector → (CloudWatch/X-Ray, Prometheus/Grafana, Tempo). .NET Aspire dashboard local geliştirmede işe yarar.
- **Senior bakış:** RED (Rate, Errors, Duration) ve USE metrikleri. SLO'lar: örneğin "cihazdan canlı haritaya p95 < 3 sn". Bu SLO ingest lag metriğiyle ölçülür.

### 3.8 .NET 8 / 9 / 10 / 11'deki Önemli Yenilikler

- **.NET 8 (LTS, desteği 10 Kasım 2026'da bitiyor):** Native AOT'nin ASP.NET Core'da kullanılabilir hale gelmesi, keyed DI, `TimeProvider`, `FrozenDictionary`, DATAS (opt-in).
- **.NET 9 (STS):** DATAS varsayılan oldu. C# 13: `System.Threading.Lock`, `params` koleksiyonlar. LINQ'e `CountBy`/`AggregateBy`/`Index` geldi. `HybridCache`. BinaryFormatter'ın kutu içi implementasyonu kaldırıldı.
- **.NET 10 (LTS, Kasım 2025):**
  - JIT: inlining, devirtualization ve stack allocation iyileştirmeleri, AVX10.2, Native AOT iyileştirmeleri.
  - C# 14: extension blocks (extension property'ler), `field` keyword, null-conditional assignment (`a?.B = x`), partial constructor'lar.
  - Kütüphaneler: post-quantum cryptography (ML-KEM, ML-DSA, SLH-DSA), `WebSocketStream`.
  - ASP.NET Core 10: OpenAPI iyileştirmeleri, passkey desteği.
  - SDK: `dotnet run app.cs` ile proje dosyası olmadan tek dosyalı C# çalıştırma.
- **.NET 11 (STS, GA Kasım 2026'da bekleniyor):** C# 15 union türleri, Runtime Async, Zstandard sıkıştırma, Android'de CoreCLR'ın varsayılan olması.

### 3.9 İngilizce Model Cevaplar
- *"How do you reduce allocations in a high-throughput parser?"* — "I parse directly over `ReadOnlySequence<byte>` from System.IO.Pipelines using `SequenceReader<byte>`, rent buffers from `ArrayPool`, avoid LINQ and boxing on the hot path, and verify with BenchmarkDotNet's MemoryDiagnoser and dotnet-counters' allocation rate."
- *"Workstation vs Server GC in containers?"* — "Server GC with DATAS, the default since .NET 9, adapts heap count to load, so it's usually fine. If a small pod regresses, I set a heap hard limit or test with DATAS disabled, and decide based on load-test data rather than gut feeling."
- *"Why is sync-over-async dangerous?"* — "Blocking on `.Result` ties up thread-pool threads. The pool injects threads slowly, so under load you get starvation and latency spikes that look like a database problem but aren't."


## 4. Software Architecture ve Design Patterns

### 4.1 Mimari Stiller — Ne Zaman Hangisi?

| Stil | Ne zaman | Ne zaman değil |
|---|---|---|
| Layered (N-tier) | Basit CRUD, küçük ekip | Domain karmaşıksa (katmanlar arası sızıntı olur) |
| Clean / Onion / Hexagonal | Domain'i framework'ten izole etmek, test edilebilirlik. Port'lar = interface, adapter'lar = altyapı | 5 endpoint'lik bir servis için (aşırı soyutlama) |
| Modular monolith | Başlangıç için varsayılan önerim. Bounded context başına modül, modüller arası kontrat ve ayrı schema | Bağımsız ölçeklenme ve deploy zorunluysa |
| Microservices | Bağımsız ölçeklenen alanlar (ör. ingestion vs raporlama), ayrı ekipler | Ekip küçükse. Dağıtık monolit riski |
| Event-driven | Telemetri, alarm, entegrasyon. Gevşek bağlılık | Güçlü tutarlılık gereken senkron akışlar |
| CQRS | Okuma/yazma asimetrisi (telemetri yazma ağır, dashboard okuma ağır) | Basit CRUD'da iki model maliyeti |
| Event sourcing | Denetim izi zorunlu alanlar (cihaz komut geçmişi, faturalama) | Telemetri için gereksiz; telemetri zaten append-only zaman serisi |
| Serverless | Düzensiz yük, glue kod, rapor üretimi | Uzun ömürlü TCP bağlantıları (Lambda'da tutulamaz) |

**Telematik için savunacağım mimari:** Modular monolith (cihaz yönetimi, filo/tenant, alarm, raporlama modülleri) + ayrı ölçeklenen ingestion gateway'leri + event backbone (Kafka/Kinesis).

### 4.2 Integration Patterns (EIP)

- **Message broker ve pub/sub:** Gateway parse ettiği mesajı yayınlar. Alarm, storage ve live-map servisleri bağımsız consumer group'larla tüketir.
- **Transactional outbox:** DB değişikliği ve event aynı transaction'da `outbox` tablosuna yazılır. Bir relay (polling ya da Debezium CDC) bunları broker'a iletir. Dual-write problemini çözer.
- **Inbox / idempotent consumer:** `processed_messages(message_id)` unique constraint'i ya da doğal anahtar upsert'i (`ON CONFLICT DO NOTHING`). At-least-once teslimatı fiilen "effectively once"a çevirir.
- **Saga:** Uzun süren dağıtık işler için. Orchestration (merkezi koordinatör) ya da choreography (event'lerle). Telafi edici (compensating) adımlar tanımlanır. Örnek: cihaz aktivasyonu → SIM aktivasyonu → abonelik; biri başarısız olursa geri alınır.
  - **Orkestrasyon mu koreografi mi?** Adım sayısı ve telafi karmaşıklığı arttıkça eğilim orkestrasyona doğrudur (durum izlenebilir, yeni adım akış tanımına eklenir); az adımda koreografi basit ve gevşek bağlıdır. **"Kesinlikle orkestrasyon" demeyin:** orkestratör de bağımlılık, darboğaz ve SPOF olabilir, hibrit yaygındır; iki tarafın argümanını söyleyip kararı koşula bağlayın. Ayrıntı ve kod: §5.6 Q63.
- **CQRS:** Okuma ve yazma modelini ayırır (aynı veritabanında ayrı model de CQRS'tir; ayrı okuma deposu zorunlu değildir). **Kilitlenmeleri ve indeks trade-off'larını "kökten çözmez"**, yenilerini getirir: eventual consistency, projeksiyon gecikmesi, iki modelin bakımı.
- **Retry, exponential backoff + jitter, circuit breaker, timeout, bulkhead:** .NET'te Polly v8 ya da `Microsoft.Extensions.Http.Resilience` (`AddStandardResilienceHandler`). Retry yalnızca idempotent işlemlerde yapılır.
- **API Gateway / BFF:** Tek giriş noktası: auth, rate limiting, caching, SSL sonlandırma, routing (istemci 50 servisin adresini bilmez). Mobil ve web için ayrı BFF (Backend for Frontend). Gateway, mikroservis dünyasının *Facade* desenidir.
- **Anti-corruption layer:** Teltonika, Queclink ve ham MQTT formatlarını kendi kanonik `TelemetryEvent` modelinize çeviren adapter katmanı. Vendor modeli domain'e sızmaz.
- **Eventual consistency:** UI'da "son güncelleme 3 sn önce" gibi göstergelerle yönetilir.
- Diğerleri: dead-letter queue, poison message, competing consumers, content-based router, message translator, claim check (büyük payload S3'te, mesajda yalnızca referans).

### 4.3 PoEAA (Martin Fowler) Pattern'leri

- **Domain Model vs Transaction Script:** Karmaşık kurallar (alarm kuralları, abonelik) için domain model. Basit akışlar için transaction script yeterli.
- **Service Layer:** Use case orkestrasyonu, transaction sınırı.
- **Repository:** Aggregate başına koleksiyon benzeri bir soyutlama. EF Core'un `DbSet`'i zaten bir repository/unit of work'tür. Generic `IRepository<T>` sarmalayıcısı çoğu zaman sızdıran bir soyutlamadır (IQueryable'ı dışarı açar ya da özellik kaybettirir). Aggregate'e özel repository kullanın.
- **Unit of Work:** `DbContext.SaveChanges` tek transaction'dır.
- **Data Mapper:** EF Core bir Data Mapper'dır. Active Record ise nesnenin kendi kendini kaydetmesidir.
- **Specification:** Sorgu kriterlerini nesneleştirir (ör. `ActiveVehiclesInTenantSpec`). Ardalis.Specification bilinen bir örnektir.
- Diğerleri: Identity Map (EF change tracker), Lazy Load, Optimistic Offline Lock (row version / `xmin`), Gateway, Value Object.

### 4.4 DDD

- **Ubiquitous language:** "Trip", "Stop", "Idle", "Geofence breach", "Ignition on/off" kelimeleri kodda da aynen kullanılır.
- **Bounded context'ler:** Device Management, Telemetry Ingestion, Fleet & Tenant, Alarming, Reporting, Billing.
- **Context mapping:** Ingestion → Alarming ilişkisi Published Language üzerinden (kanonik event). Vendor protokolleri ACL arkasında. Billing ile Customer/Supplier ilişkisi.
- **Aggregate:** Tutarlılık sınırıdır. Örneğin `Geofence` (poligon + kurallar) ya da `Device` (komut kuyruğu ve durum). Aggregate'ler küçük tutulur ve birbirine id ile referans verir. Bir transaction'da tek aggregate değişir.
- **Entity vs Value Object:** `Vehicle` kimliği olan bir entity'dir. `Coordinate`, `Speed`, `Odometer` ise immutable ve eşitliği değer üzerinden tanımlanan value object'lerdir. C#'ta `record struct` ya da EF 10 complex type ile modellenir.
- **Domain events:** `GeofenceEntered`, `HarshBrakingDetected`. In-process dispatch (SaveChanges sonrası) ile integration event'i (outbox) ayırın.
- **Tuzak:** Telemetri noktalarını aggregate olarak modellemek. Bunlar domain nesnesi değil, veri akışıdır.

### 4.5 SOLID, DI ve Strategy–Factory

#### ⭐ Q43. SOLID'i tek cümlelik tanımlarla ve telematik örnekleriyle anlat.

- **S (SRP):** Bir sınıfın değişmek için **tek bir nedeni** olmalı. Parser, validator ve persister ayrı sınıflardır (veri formatı değişince ve hesap kuralı değişince aynı sınıfı açmak zorunda kalmazsın).
- **O (OCP):** Genişlemeye açık, değişime kapalı. Yeni cihaz protokolü için yeni bir `IProtocolDecoder` yazılır; mevcut `if/else` zinciri değişmez.
- **L (LSP):** Alt tip, üst tipin yerine davranışı bozmadan geçebilmeli. Klasik anti-örnek: `Kus.Uç()` içinde `Penguen`'in `NotImplementedException` fırlatması. Çözüm: arayüzleri böl (`IUçabilir`).
- **I (ISP):** İstemci kullanmadığı metotlara bağımlı olmasın. Şişman `IDeviceService` yerine `ICommandSender` ve `IDeviceQuery`.
- **D (DIP):** Üst seviye modül alt seviyeye değil, **soyutlamaya** bağımlı olsun; altyapı soyutlamayı uygular. `new SqlConnection()` yerine yapıcıdan `IWalletRepository`.
- **Senior notu:** SOLID bir ölçüt değil bir sezgi aracıdır; her sınıfa interface açmak DIP değildir. **YAGNI:** sırf havalı dursun diye Factory/Strategy koymam; değişim maliyeti yüksekse koyarım.

#### ⭐ Q44. DIP, IoC, DI ve IoC container aynı şey mi? DI bir GoF deseni mi?

**Model cevap:** Hayır, dört ayrı kavram: **DIP** bir *prensiptir* (soyutlamaya bağımlı ol); **IoC** (kontrolün tersine çevrilmesi) genel bir *tasarım ilkesidir* (akışı framework yönetir, sen çağrılırsın: Hollywood prensibi); **DI** bu ilkeyi uygulayan bir *tekniktir* (bağımlılıkları dışarıdan ver: constructor injection); **IoC container** (`Microsoft.Extensions.DependencyInjection`) bu işi otomatikleştiren *araçtır*. **DI, GoF kataloğundaki bir desen değildir** (terimi Fowler 2004'te yaygınlaştırdı); mülakatta GoF listesinde saymayın. Constructor injection tercih edilir (bağımlılıklar görünür, nesne her zaman geçerli); property injection isteğe bağlı bağımlılıklar için; service locator (`IServiceProvider.GetService`) anti-pattern'dir.

#### ⭐ Q45. Bu logger kodundaki SOLID ihlalleri nedir? Nasıl düzeltirsin?

```csharp
public class ErrorLogger {
    public void LogError(string message, string logType) {
        if (logType == "File") File.WriteAllText("error.txt", message);
        else if (logType == "Database") { var db = new SqlConnection("..."); db.Execute("INSERT INTO Logs ...", new { message }); }
        else if (logType == "ElasticSearch") { var c = new ElasticClient(); c.IndexDocument(message); }
    }
}
```

**Model cevap:** **OCP** (yeni log türü için metodu değiştirmek gerekir), **DIP** (`new SqlConnection`/`new ElasticClient` ile somut altyapıya doğrudan bağımlı), **SRP** (hem yönlendirir hem dosya/DB/HTTP ayrıntısını bilir). Çözüm: soyutlama + implementasyonlar + constructor injection.

**Notundaki refactor'daki tuzaklar (düzeltilmiş hali aşağıda):** (1) `ILogger` adı `Microsoft.Extensions.Logging.ILogger` ile çakışır (`ILogSink`/`IErrorSink` de); (2) `DatabaseLogger` içinde yine `new SqlConnection` var ve **dispose edilmiyor** (bağlantı sızıntısı); DIP hâlâ kırık; (3) `File.WriteAllText` her log'da dosyayı **ezer**, `AppendAllText` olmalı; (4) I/O senkron.

```csharp
public interface IErrorSink { Task WriteAsync(string message, CancellationToken ct = default); }

public sealed class FileSink(string path) : IErrorSink
{
    public Task WriteAsync(string m, CancellationToken ct = default) =>
        File.AppendAllTextAsync(path, m + Environment.NewLine, ct);
}

public sealed class DatabaseSink(NpgsqlDataSource ds) : IErrorSink     // bağlantı havuzu DI'dan gelir
{
    public async Task WriteAsync(string m, CancellationToken ct = default)
    {
        await using var cmd = ds.CreateCommand("INSERT INTO logs(message) VALUES ($1)");
        cmd.Parameters.AddWithValue(m);
        await cmd.ExecuteNonQueryAsync(ct);                          // bağlantı komutla birlikte dispose edilir
    }
}

public sealed class ErrorService(IEnumerable<IErrorSink> sinks)       // OCP: yeni sink = yeni sınıf + DI kaydı
{
    public Task ProcessErrorAsync(string m, CancellationToken ct = default) =>
        Task.WhenAll(sinks.Select(s => s.WriteAsync(m, ct)));
}
```

(Gerçek projede zaten `ILogger<T>` + Serilog/OTel sink'leri kullanılır: tekerleği yeniden icat etme; bu soru prensip sorusudur.)

#### ⭐ Q46. Strategy ile Factory farkı nedir? Hangi design pattern'leri biliyorsun?

**Model cevap:** İkisi de `if/else`/`switch` yığınını temizler ve arayüz üzerinden çalışır, ama **niyetleri farklıdır:** **Factory nesne yaratmayla (Creational), Strategy davranışla (Behavioral)** ilgilenir. Factory "**kim** üretilecek?", Strategy "iş **nasıl** yapılacak?" sorusunu yanıtlar. Ödeme örneği: `CreditCardStrategy`, `CryptoStrategy` `IPaymentStrategy`'yi uygular (Strategy); kullanıcı "Kripto" seçince `if (type=="Crypto") new CryptoStrategy()` yazmak Strategy'nin faydasını yok eder, `PaymentFactory.Create(type)` bunu yapar (Factory). Gerçekte **birlikte çalışırlar**; modern .NET'te DI container factory rolünü üstlenir: `IEnumerable<IPaymentStrategy>` enjekte edip anahtara göre seçersin (ya da **keyed services**, .NET 8). Telematik karşılığı: `IAvlDecoder` (Strategy) + codec id'ye göre seçim (Factory/registry).

**"Hangi pattern'leri biliyorsun?" cevap şablonu:** (1) Giriş: "Desenleri isim olarak saymak yerine çözdüğü problemlere göre anlatırım; araçtırlar, amaç değil." (2) Kod seviyesi: Strategy/Factory (OCP), Decorator, Observer, Adapter, Singleton'ın DI sürümü. (3) Mimari seviye: Pub/Sub, Outbox, CQRS (bedeliyle), API Gateway/BFF, ACL. (4) Kapanış: YAGNI, "gelecekteki değişim maliyeti yüksekse".

### 4.6 GoF Pattern'leri — C# ve .NET Karşılıkları

**Creational**
- **Factory Method / Abstract Factory:** Codec ID'ye göre decoder üretmek. .NET karşılıkları: `IHttpClientFactory`, `ILoggerFactory`. DI container da bir factory + registry'dir.
```csharp
public sealed class DecoderFactory(IEnumerable<IAvlDecoder> decoders)
{
    public IAvlDecoder For(byte codecId) => decoders.First(d => d.CodecId == codecId);
}
```
- **Builder:** Adım adım kurulum. Karşılıkları: `WebApplication.CreateBuilder`, `StringBuilder`, `NpgsqlConnectionStringBuilder`, test data builder'ları.
- **Singleton:** Klasik `static Instance` test edilemez ve gizli bağımlılık yaratır. Doğrusu DI'da `AddSingleton` kullanmaktır: yaşam süresini container yönetir. Thread-safety yine sizin sorumluluğunuzdadır.

**Structural**
- **Adapter:** Queclink ASCII ya da Teltonika binary formatını `TelemetryEvent`'e çevirmek (ACL'nin uygulaması).
- **Decorator:** Davranışı sarmalayarak eklemek: cache'leyen repository, retry'lı `ICommandSender`. Scrutor `Decorate<>`, `DelegatingHandler` (HttpClient pipeline) örnektir.
- **Facade:** Karmaşık alt sistemi basit bir API arkasına koymak, örneğin `ITripReportService`.
- **Proxy:** Erişimi kontrol etmek. EF lazy-loading proxy'leri, gRPC client stub'ları, `DispatchProxy`.

**Behavioral**
- **Strategy:** Sürüş skoru algoritması ya da araç tipine göre yakıt hesabı. Keyed services ile seçilir.
- **Observer:** `IObservable<T>`, C# event'leri, SignalR, pub/sub.
- **Command:** Cihaza giden komutlar nesne olarak tutulur (`SetOutputCommand` gibi): kuyruklanır, loglanır, yeniden denenir. CQRS'teki command'lar da bu fikre dayanır.
- **Mediator:** MediatR'ın `ISender`/`IPublisher`'ı. **Dikkat:** MediatR 2025'te ticari çift lisansa geçti. Basit bir handler arayüzü ve DI ile aynı etki elde edilebilir. Mediator, controller'ları inceltir ama "go to definition"ı kırar ve gereksiz dolaylılık yaratabilir.
- **State:** Cihaz komut yaşam döngüsü (Pending → Sent → Acked / TimedOut / Failed) ya da trip durum makinesi (Idle → Driving → Stopped). Stateless kütüphanesi bir seçenek.
- **Chain of Responsibility:** ASP.NET Core middleware, `DelegatingHandler`, MediatR pipeline behavior'ları, alarm kural zinciri.
- **Template Method:** `BackgroundService.ExecuteAsync`. Ortak bir `DecoderBase` (header ve CRC kontrolü sabit, body parse abstract).
- **Iterator:** `IEnumerable`/`IAsyncEnumerable` (`yield`).

### 4.7 Ne Zaman Kullanılmamalı: Over-engineering Tuzakları
- EF Core üzerine generic repository + unit of work sarmalayıcısı koymak.
- Tek implementasyonu olan her sınıfa interface açmak. Test için gerekmiyorsa açmayın.
- Erken microservice: dağıtık transaction'lar, ağ hataları, gözlemlenebilirlik maliyeti.
- Telemetriye event sourcing uygulamak.
- Her yerde MediatR + AutoMapper (AutoMapper da ticari lisansa geçti). Açık mapping kodu daha okunur ve daha hızlıdır.
- Basit kurallar için strategy/factory hiyerarşisi. Bir `switch` expression çoğu zaman yeterlidir.
- Kural: "Rule of three". Soyutlamayı üçüncü tekrarda çıkarın. Her pattern'i bir değişim ekseni gerekçesiyle savunun.
- **Anti-pattern isimleri (bil):** *God Object* (her şeyi yapan sınıf), *Spaghetti Code* (dolaşık akış), *Golden Hammer* (elindeki tek aracı her probleme uygulamak), *Cargo Cult Programming* (neden yaptığını bilmeden kopyalamak), *Big Ball of Mud*, *Distributed Monolith* (ağ üzerinden birbirine sıkı bağlı "mikroservisler"), *Event Spaghetti* (kontrolsüz event zinciri).

### 4.8 İngilizce Model Cevaplar
- *"When would you choose a modular monolith over microservices?"* — "When the team is small and the domain boundaries are still moving. I enforce module boundaries with separate schemas and internal contracts, and extract a service only when it has a distinct scaling or deployment need. In telematics that's usually the ingestion gateway."
- *"Explain the outbox pattern."* — "The business change and the outgoing message are written in the same database transaction. A relay publishes outbox rows to the broker, and consumers are idempotent. That removes the dual-write problem while keeping at-least-once delivery."


## 5. Veri, Cache ve Mesajlaşma ⭐

Bu bölüm senin Scorp notlarındaki (indeksleme, ACID, CAP, sharding, cache stampede, outbox, saga) konuların **PostgreSQL ve .NET odaklı, düzeltilmiş** halidir. Telematikte aynı sorular başka kılıkla gelir: "10 bin cihaz aynı anda yazıyor, veritabanı kilitleniyor", "komut iki kez gönderildi", "cache düştü, DB'ye 50 bin istek geldi".

### 5.1 Karmaşıklık, veri yapıları ve mutabakat sorusu

#### ⭐ Q47. Big O nedir? "En kötü senaryo" mu?

**Model cevap:** Big O, girdi büyüklüğü n arttıkça maliyetin **büyüme hızının üst sınırıdır**; "en kötü senaryo" ile aynı şey değildir. Üç ayrı eksen vardır: **en kötü / ortalama / amortize**. Örnekler: `Dictionary` araması **ortalama** O(1), çakışma yığılırsa O(n); `List.Add` **amortize** O(1) (kapasite dolunca dizi iki katına büyür, o ekleme O(n) ama ortalaması sabit); quicksort ortalama O(n log n), en kötü O(n²). Zaman kadar **bellek** (space complexity) de ölçülür. Pratik kural: "zamanı hızlandırmak için RAM'den feda et" (`HashSet`, cache, indeks); ama bedelini söyle (hash hesabı, bellek, GC baskısı).

| Sınıf | Örnek |
|---|---|
| O(1) | dizi indeksi, `Dictionary` okuma (ort.) |
| O(log n) | binary search, B-tree araması, `SortedDictionary` |
| O(n) | `foreach`, `List.Contains` |
| O(n log n) | `Sort`, `OrderBy`, merge sort |
| O(n²) | iç içe döngü ile iki listeyi karşılaştırma |

#### ⭐ Q48. Mutabakat: iki listedeki (1'er milyon kayıt) ortak yayıncı/cihaz id'lerini bul. Sonra?

**Model cevap (adım adım):**
1. **Naif:** `foreach` içinde `list.Contains` = **O(n·m)**; 1M × 1M = 10¹² karşılaştırma.
2. **Çözüm:** küçük listeyi `HashSet`'e at (O(m) süre, O(m) bellek), diğerini gez ve `Contains` ile bak: **O(n + m)**. LINQ karşılığı `a.Intersect(b)`: içeride hash tabanlıdır, tekrarları da eler (küme semantiği).
3. **Bellek kısıtı varsa:** iki listeyi sıralayıp **iki işaretçiyle** gez: O(n log n + m log m) süre, ek bellek O(1). Veri RAM'e sığmıyorsa veritabanında `JOIN`/`INTERSECT`, ya da dış sıralama (external sort).
4. **Yaklaşık yanıt yeterliyse:** Bloom filter (yanlış pozitif olabilir, yanlış negatif olmaz) ile ön eleme.
5. **Dikkat edilecekler:** tekrarlar (küme mi, multiset mi?), büyük/küçük harf ve boşluk normalizasyonu (`StringComparer.OrdinalIgnoreCase`), `null`'lar, id'nin `string` yerine `long` olması (bellek ve hız).

```csharp
public static List<string> Ortaklar(IEnumerable<string> a, IEnumerable<string> b)
{
    var set = new HashSet<string>(b, StringComparer.Ordinal);
    var seen = new HashSet<string>(StringComparer.Ordinal);       // sonuçta tekrar olmasın
    var result = new List<string>();
    foreach (var id in a)
        if (set.Contains(id) && seen.Add(id)) result.Add(id);
    return result;
}
```

**Edge-case refleksi (sormadan söyle):** null/boş girdi, tekrarlar, çok büyük veri, aynı anda iki istek (race).

### 5.2 PostgreSQL: indeksler ve sorgu optimizasyonu

#### ⭐ Q49. Index nedir, B-tree nasıl çalışır? "Clustered index" PostgreSQL'de var mı?

**Model cevap:** Index, aramayı tablo taraması O(n) yerine ağaç üzerinde **O(log n)**'e indirir. PostgreSQL'de varsayılan **B-tree**'dir (eşitlik, aralık, sıralama). Diğer tipler: **Hash** (yalnızca eşitlik), **GIN** (`jsonb`, dizi, tam metin), **GiST/SP-GiST** (PostGIS geometrisi, aralıklar), **BRIN** (fiziksel sıralı çok büyük tablolar: zaman serisi; çok küçük).

**Tuzak:** "Clustered index" kavramı **SQL Server ve MySQL InnoDB** içindir (veri satırları PK sırasına göre ağaçta durur). **PostgreSQL'de clustered index yoktur**: tablo bir *heap*'tir, bütün index'ler ikincildir ve heap'teki satırı `ctid` ile gösterir. `CLUSTER` komutu tabloyu bir indekse göre **bir kez** fiziksel sıralar ve sonraki yazmalarda korunmaz (üstelik tabloyu kilitler). Bu yüzden "fiziksel sıra" ihtiyacı (zaman serisi) için partitioning + BRIN ya da TimescaleDB kullanılır.

Bilmen gerekenler:
- **Index scan / index-only scan / bitmap scan / seq scan:** index-only scan için gereken kolonlar indekste olmalı ve *visibility map* güncel olmalı (VACUUM). Kapsayan indeks: `CREATE INDEX ... ON t (a) INCLUDE (b)`.
- **Seq scan her zaman kötü değildir:** tablonun büyük kısmı okunacaksa ya da tablo küçükse planlayıcı doğru olarak seq scan seçer.
- **Partial index:** `CREATE INDEX ON outbox (id) WHERE processed_at IS NULL;` (yalnızca işlenmemiş satırlar: küçük ve hızlı). **Expression index:** `CREATE INDEX ON users (lower(email));`
- **Yazma maliyeti:** her indeks INSERT/UPDATE'i yavaşlatır, disk ve WAL üretir. PostgreSQL'de güncelleme yeni satır sürümü yazar; indekslenmiş kolon değişmiyorsa **HOT update** ile indeks yazımından kaçınılır (fillfactor düşürmek buna yardım eder).
- Kullanılmayan indeksler: `pg_stat_user_indexes.idx_scan = 0`.
- Üretimde büyük tabloya: `CREATE INDEX CONCURRENTLY` (tabloyu yazmaya kapatmaz; transaction içinde çalışmaz).

#### ⭐ Q50. Composite index'te kolon sırasını nasıl belirlersin? (`Country, Username` mi, `Username, Country` mi?)

**Düzeltilmiş cevap:** "Cardinality'si en yüksek kolon her zaman başa" diye bir kural **yoktur**. Karar şu sorularla verilir:
1. **Sorgular hangi kolon alt kümesini kullanıyor?** B-tree'de indeks, soldan sağa sıralıdır; sorgu **soldaki kolonların bir önekini (leftmost prefix)** kullanabiliyorsa indeks verimlidir.
2. **Eşitlik kolonları önce, aralık/sıralama kolonu sonda.** PostgreSQL belgesine göre baştaki eşitlik kolonları ile ilk eşitlik-olmayan kolondaki koşul taranacak aralığı belirler; o kolondan sonraki kolonlar yalnızca indeks içinde filtre olarak kullanılır.
3. **Benzersizlik / kısıt:** gerekiyorsa `UNIQUE`.

`WHERE country = 'TR' AND username = 'ozgur'` için **iki sıra da aynı daralmayı sağlar**: `(country, username)` B-tree'sinde 'TR' bloğu `username`'e göre sıralıdır; doğrusal tarama olmaz, tek ağaç inişiyle `(TR, ozgur)` bulunur. Yani "TR dalındaki 3 milyon kaydı tarar" cümlesi yanlıştır. Seçimi asıl belirleyen **başka hangi sorguların olduğu**dur:
- Yalnızca `WHERE country = 'TR'` sorguları da varsa `(country, username)` ikisini de karşılar.
- Yalnızca `username` ile arama varsa `(username, country)` ya da zaten **tek başına `UNIQUE(username)`** yeter; ikinci kolon gereksizdir.
- **PostgreSQL 18 (Eylül 2025) skip scan** getirdi: öndeki kolonun farklı değer sayısı azsa, ona koşul olmasa bile bileşik indeks kullanılabilir. "Öndeki kolon yoksa indeks kullanılamaz" artık mutlak değildir.

**Mülakat cümlesi:** "Sıra, sorgu kalıplarına göre belirlenir: eşitlik kolonları önde, aralık ve sıralama sonda. İki kolon da eşitlikse sıra tek sorgu için fark etmez; diğer sorguları ve kısıtları düşünürüm. `EXPLAIN (ANALYZE, BUFFERS)` ile doğrularım."

#### ⭐ Q51. SARGable sorgu nedir?

**Model cevap:** Index'i kullanabilen koşul. İndeksli kolonu fonksiyona ya da işleme sokarsan planlayıcı indeksi kullanamaz.

```sql
-- Kötü: created_at indeksi kullanılamaz
SELECT id FROM users WHERE extract(year from created_at) = 2025;
-- İyi: yalın kolon, yarı açık aralık
SELECT id FROM users WHERE created_at >= '2025-01-01' AND created_at < '2026-01-01';
-- Fonksiyon şart ise expression index
CREATE INDEX ON users (lower(email));  --  WHERE lower(email) = 'a@b.com'
```

Diğer tuzaklar: `LIKE '%abc'` (baştaki joker; trigram/GIN gerekir), `LIKE 'abc%'` için ya C collation ya da `text_pattern_ops`, tip uyuşmazlığı (implicit cast), `OR` zincirleri, `SELECT *`. Zaman serisinde **keyset pagination**: `WHERE (device_id, ts) < (@d, @t) ORDER BY device_id DESC, ts DESC LIMIT 50` (OFFSET büyüdükçe yavaşlar ve sayfalar kayar).

#### Q52. Yavaş bir sorguyu nasıl teşhis edersin?

**Model cevap:** (1) `pg_stat_statements` ile en pahalı sorguları bul. (2) `EXPLAIN (ANALYZE, BUFFERS)`: tahmin edilen ve gerçek satır sayısı arasındaki sapma (istatistik bayat → `ANALYZE`), seq scan, sort/hash'in diske taşması (`work_mem`), nested loop'un yanlış seçimi. (3) Uygun indeks, sorgu yeniden yazımı ya da şema değişikliği. (4) Uygulama tarafı: N+1, gereksiz kolon, tek tek insert (→ `COPY`), uzun transaction. (5) Operasyonel: autovacuum ayarı ve **bloat**, bağlantı sayısı (PgBouncer ile havuzlama), kilit beklemeleri (`pg_locks`, `pg_stat_activity`), yavaş sorgu logu. Her değişiklikten sonra ölç.

### 5.3 ACID, izolasyon seviyeleri ve Lost Update

#### ⭐ Q53. ACID'in her harfini bir örnekle anlat. CAP'teki "C" ile aynı mı?

**Model cevap:**
- **Atomicity:** ya hep ya hiç. Bakiye güncelleme ile `outbox` satırı aynı `BEGIN…COMMIT` içindedir.
- **Consistency:** transaction veritabanını bir geçerli durumdan diğerine taşır: `CHECK (amount >= 0)`, FK, `UNIQUE`, `NOT NULL`. Bu kuralları ihlal eden transaction reddedilir.
- **Isolation:** eşzamanlı transaction'ların birbirini ne kadar gördüğü (aşağıda).
- **Durability:** `COMMIT` sonrası çökme olsa bile veri kaybolmaz: değişiklik önce **WAL**'a (write-ahead log) flush edilir; yeniden başlangıçta WAL'dan toparlanır (`synchronous_commit=off` bu garantiyi gevşetir; bilinçli takas).
- **CAP'teki C ≠ ACID'deki C.** CAP'te C, **linearizability** (tek kopya varmış gibi davranma) demektir; ACID'de C ise veri bütünlüğü kurallarıdır.

#### ⭐ Q54. İzolasyon seviyeleri ve PostgreSQL'deki gerçek davranış nedir?

| Seviye (SQL standardı) | PostgreSQL'de gerçek davranış |
|---|---|
| Read Uncommitted | **Read Committed gibi davranır**; dirty read hiçbir zaman olmaz |
| **Read Committed** (varsayılan) | Her *ifade* kendi snapshot'ını görür; non-repeatable read ve phantom mümkün |
| **Repeatable Read** | Transaction başına tek snapshot (snapshot isolation); phantom da olmaz. Aynı satırı eşzamanlı değiştirmeye çalışırsan `40001 could not serialize access` alırsın: **yeniden dene** |
| **Serializable** | **SSI** (Serializable Snapshot Isolation): ek bloklama getirmez; riskli bağımlılık döngüsü görürse bir transaction'ı `40001` ile iptal eder. **Uygulama retry yazmak zorundadır** |

**Sık yanlış:** "Serializable, tüm transaction'ları kilitler, eşzamanlılığı bitirir" anlatımı kilit tabanlı motorlara (SQL Server) daha yakındır. PostgreSQL'de predicate lock'lar (`SIReadLock`) bloklamaz; maliyet iptal ve yeniden denemedir.

**Write skew örneği:** iki doktor aynı anda "nöbetten çıkıyorum" der; her biri "en az bir doktor nöbette" kuralını kontrol edip kendi satırını günceller. Satırlar farklı olduğu için Repeatable Read engellemez; Serializable (ya da kural satırını `FOR UPDATE` ile kilitlemek) engeller.

#### ⭐ Q55. "Lost Update": iki kullanıcı aynı anda yayıncı bakiyesine bağış yapıyor (100 → 110 ve 150, sonuç 150 kaldı). Nasıl çözersin?

**Cevabını sıraya koy (en basitten en ağıra):**

1. **Atomik güncelleme (çoğu zaman en iyi çözüm):** oku-değiştir-yaz yerine veritabanına hesaplat. Kilit, versiyon, retry gerekmez.
   ```sql
   UPDATE balances SET amount = amount + @x WHERE id = @id;
   ```
   Read Committed'da ikinci transaction satır kilidini bekler, ilk COMMIT'ten sonra güncel değerle ifadeyi yeniden değerlendirir; toplam doğru çıkar.
2. **Koşullu atomik güncelleme (çekim/harcama):** kuralı `WHERE`'e koy ve etkilenen satır sayısına bak.
   ```sql
   UPDATE balances SET amount = amount - @amt WHERE id = @id AND amount >= @amt;   -- 0 satır => yetersiz bakiye
   ```
3. **Optimistic locking (row version):** okurken versiyonu al, `UPDATE … WHERE id=@id AND version=@v` yaz; 0 satır etkilendiyse çakışma vardır: yeniden dene ya da hata dön. Npgsql/EF Core'da `[Timestamp] public uint Version { get; set; }` PostgreSQL'in `xmin` sistem kolonuna eşlenir; çakışmada `DbUpdateConcurrencyException`. Okuma ağırlıklı, çakışması nadir veride (profil güncelleme) uygundur.
4. **Pessimistic locking:** `SELECT … FOR UPDATE` ile satırı kilitle, hesapla, yaz. Yüksek çekişmede (bilet rezervasyonu) mantıklı; bedeli bekleme ve deadlock riski (kilitleri hep aynı sırayla al).
5. **Serializable + retry:** kural karmaşıksa ve tek satır değilse.

```csharp
// EF Core: atomik, tek SQL
var rows = await db.Balances
    .Where(b => b.Id == id && b.Amount >= amount)
    .ExecuteUpdateAsync(s => s.SetProperty(b => b.Amount, b => b.Amount - amount), ct);
if (rows == 0) throw new InsufficientFundsException();

// EF Core: optimistic concurrency + retry
for (var attempt = 0; ; attempt++)
{
    try { /* oku, değiştir */ await db.SaveChangesAsync(ct); break; }
    catch (DbUpdateConcurrencyException) when (attempt < 3) { db.ChangeTracker.Clear(); }
}
```

**`WalletService` bağlantısı (§6.1):** `GetBalance` → kontrol → `UpdateBalance` sırası bir *check-then-act* yarışıdır; iki eşzamanlı çekim bakiyeyi eksiye düşürebilir. Unit test bunu yakalamaz, **kendin söyle**: çözüm 2 numaralı koşullu atomik güncelleme (+ `CHECK (amount >= 0)` kısıtı son savunma).

**Telematik karşılığı:** "son konum" güncellemesi `WHERE ts > last_ts`, komut durum geçişi `WHERE status = 'Sent'`, sayaç `SET n = n + 1`.

### 5.4 Partitioning, sharding ve CAP

#### ⭐ Q56. Partitioning ile sharding farkı nedir? Hangi shard key'i seçersin?

**Model cevap:** **Partitioning** veriyi **aynı sunucu içinde** bölmektir (PostgreSQL declarative partitioning: aralık/liste/hash; amaç sorgu performansı ve bakım: eski partition'ı `DROP` etmek `DELETE`'ten çok ucuzdur). **Sharding** veriyi **bağımsız fiziksel sunuculara** dağıtmaktır (shared-nothing; amaç donanım limitini aşmak). Bedeli: shard'lar arası `JOIN` ve dağıtık transaction (2PC) zorlaşır, yeniden dengeleme (re-sharding) maliyetlidir. **Önce** dikey ölçekleme, okuma replikaları, cache, partitioning ve doğru indeksleri tüket; sharding son çaredir.

Stratejiler:
- **Range:** basit, aralık sorgusu iyi; ama yeni kayıtlar hep son shard'a gider (**hotspot**).
- **Hash (`hash(key) % N`):** dengeli; ama N değişince neredeyse her şey yer değiştirir.
- **Consistent hashing:** sunucular ve anahtarlar bir halkaya yerleşir; anahtar saat yönünde ilk sunucuya düşer. Sunucu eklenince yalnızca komşu aralık taşınır; **sanal düğümler (virtual nodes)** dağılımı dengeler. Dağıtık cache ve Dynamo benzeri veritabanlarında kullanılır.
- **Directory (lookup) tabanlı:** esnek, ama lookup servisi bağımlılık ve darboğaz olur.

**Shard key kararı = erişim kalıbı kararı.** Telematikte `device_id` ya da `tenant_id` + `device_id`: cihazın tüm verisi bir arada, sıra korunur. Tehlike: tek bir dev müşteri (tenant) tek shard'ı boğar → bileşik anahtar ya da büyük tenant'ı ayrı shard'a al.

**Senin "Likes servisi" sorun:** `UserId` ile shard'larsan bir yayının toplam beğenisi için scatter-gather gerekir; `LikeId` rastgele dağılır, aynı sorun; **`StreamId`** ile aynı yayının verisi bir arada kalır ama **celebrity / hot shard** problemi çıkar. Çözüm: beğenileri önce **Redis sayacına** (`INCR stream:123:likes`) topla, bir worker 1-2 saniyede bir toplu yazsın (**write-behind / batching**). Daha ileri: sıcak yayını birden fazla alt-sayaca böl (`stream:123:likes:{0..k}`) ve okurken topla (**sharded counter**). Dürüst bedel: Redis çökerse son birkaç saniyelik sayaç kaybolabilir; para değil istatistik olduğu için kabul edilebilir (**AP**).

#### ⭐ Q57. CAP teoremi nedir? "Bu sistem CP mi AP mi?" sorusuna nasıl cevap verirsin?

**Düzeltilmiş cevap:** Ağ bölünmesi (partition) sırasında bir sistem **linearizable tutarlılık (C)** ile **erişilebilirlik (A)** arasında seçim yapmak zorundadır. Bölünme *olmadığında* ikisi birlikte sağlanabilir. Gerçek ağda P'den vazgeçilemediği için seçim "bölünme anında ne yapacağız?"dır:
- **CP davranışı:** tutarlılığı koru, erişilebilirliği feda et (ağ kopuksa işlemi reddet): bakiye düşme, ödeme.
- **AP davranışı:** isteği kabul et, eventual consistency ile birleştir: beğeni sayacı, telemetri, "son görülme".

Dikkat edilecekler:
- **"C" = linearizability**; "veri hemen her yere kopyalanır" değil, ve ACID'deki C değil.
- **Ürüne etiket yapıştırma.** "PostgreSQL CP, Redis CP, Cassandra AP" gibi kalıplar yanıltıcıdır (Kleppmann'ın "Please stop calling databases CP or AP" yazısı). **Redis Cluster strong consistency vermez:** replikasyon asenkrondur, onaylanmış yazmalar kaybolabilir. Tek düğüm PostgreSQL ise dağıtık sistem bile değildir. DynamoDB varsayılan olarak eventually consistent okur ama istek bazında *strongly consistent read* seçilebilir.
- Cevabı **işlem bazında** ver: aynı üründe "bağış kaydı" CP gibi, "beğeni sayacı" AP gibi davranabilir.
- **PACELC:** bölünme (P) varsa A mı C mi; *yoksa* (E, else) **gecikme (L) mi tutarlılık (C) mi**? Dinamik cevap: normal zamanda da senkron replikasyon gecikme maliyeti getirir.

**Mülakat cümlesi:** "Like ve yorum sayaçlarında AP seçerim: buton her zaman çalışmalı, sayı birkaç saniye geç yansıyabilir. Bakiye ve ödeme yazmalarında tutarlılık önceliklidir; bölünmede reddederim ve idempotency anahtarıyla güvenli yeniden denemeye izin veririm."

### 5.5 Cache

#### ⭐ Q58. Cache stratejileri ve cache stampede nedir? Nasıl önlersin?

**Model cevap:** Desenler: **cache-aside** (uygulama okur; yoksa DB'den çekip yazar; en yaygın), **read-through**, **write-through** (yazma hem cache hem DB), **write-behind** (önce cache, sonra asenkron DB; hız ↑, kayıp riski ↑). Her cache için TTL, boyut sınırı ve invalidation planı olmalı: "bilgisayar biliminin iki zor işinden biri".

**Stampede:** sıcak bir anahtarın TTL'i dolduğunda aynı anda gelen N isteğin hepsi cache miss alıp DB'ye gider. Önlemler (birlikte kullanılır):
1. **Jitter:** TTL'e rastgele süre ekle; anahtarlar aynı anda düşmesin. Tek başına yetmez: tek bir sıcak anahtar yine tek anda düşer.
2. **Tek uçuş (single-flight) / kilit:** miss'te yalnızca **bir** istek DB'ye gider, diğerleri bekler ya da bayat veri döner. Process içinde `SemaphoreSlim`/`Lazy<Task>`; çok instance'ta Redis kilidi.
3. **Arka plan yenileme (refresh-ahead):** worker sıcak anahtarları TTL dolmadan yeniler; kullanıcı yolu DB'ye hiç inmez.
4. **Olasılıksal erken yenileme (XFetch):** TTL'e yaklaştıkça yenileme olasılığı artar; ya da son %5'te gelen istek bayat veriyi alır, arka planda yenileme tetiklenir (stale-while-revalidate).
5. **Yük atma:** DB önünde bulkhead/rate limit, circuit breaker.
Akraba sorunlar: **penetration** (olmayan anahtar sürekli sorulur → *negative caching* ya da Bloom filter), **avalanche** (çok anahtarın aynı anda düşmesi → jitter, kademeli ısıtma).

```csharp
// .NET 9+ HybridCache: L1 (bellek) + L2 (Redis/IDistributedCache), stampede korumalı
builder.Services.AddStackExchangeRedisCache(o => o.Configuration = cfg["Redis"]);
builder.Services.AddHybridCache(o => o.DefaultEntryOptions = new HybridCacheEntryOptions
{
    Expiration = TimeSpan.FromMinutes(10),            // L2
    LocalCacheExpiration = TimeSpan.FromMinutes(1)    // L1
});

var profile = await cache.GetOrCreateAsync(
    $"profile:{id}",
    async ct => await repo.GetProfileAsync(id, ct),
    cancellationToken: ct);
```

**HybridCache'in sınırı:** stampede koruması **yalnızca aynı `HybridCache` örneği (aynı process) içindeki** eşzamanlı çağrılar için birleştirmedir. 20 pod varsa, aynı anahtar için en fazla 20 factory çalışabilir; sıcak anahtarlarda bu hâlâ sorun olabilir (bayat veri döndürme ya da dağıtık kilit gerekir).

#### ⭐ Q59. Redis ile dağıtık kilit nasıl yazılır? Neden "yetmez"?

**Model cevap:** Doğru temel: `SET key token NX PX 5000` (yoksa yaz, 5 sn TTL). Üç şart: **(1) TTL** (pod çökerse kilit kalmasın), **(2) benzersiz token** (kim sahip?), **(3) Lua ile atomik bırakma**: "token benim mi?" + "sil" tek adım olmalı. Lua olmazsa: A kilidi alır, GC duraksaması/TTL dolar, B kilidi alır, A uyanıp `DEL` der ve **B'nin kilidini siler**.

```csharp
var token = Guid.NewGuid().ToString("N");
if (await db.StringSetAsync(key, token, TimeSpan.FromSeconds(5), When.NotExists))
{
    try { await DoWorkAsync(ct); }
    finally
    {
        await db.ScriptEvaluateAsync(
            "if redis.call('get', KEYS[1]) == ARGV[1] then return redis.call('del', KEYS[1]) else return 0 end",
            new RedisKey[] { key }, new RedisValue[] { token });
    }
}
```

**Dürüst sınır:** Lua yalnızca *yanlış kişinin silmesini* önler, **kritik bölgenin çakışmasını** önlemez: iş TTL'den uzun sürerse iki süreç aynı anda içeridedir. Asıl koruma: **kilit + idempotency anahtarı + veritabanı kısıtı**; korunan kaynak varsa **fencing token** (kilit alındıkça artan sayı; kaynak eski token'ı reddeder). Redlock'un (çok düğümlü Redis kilidi) güvenliği zamanlama varsayımlarına dayandığı için tartışmalıdır (Kleppmann–Antirez); doğruluk (correctness) isteyen yerde kilide değil, DB kısıtına ve idempotency'ye güven; kilit **verimlilik** içindir. Kısa kural: "lock yetmez, idempotent ol".

#### Q60. Redis'i hangi amaçlarla kullanırsın? Dikkat edilecekler?

**Model cevap:** Cache, oturum, rate limit (sayaç + TTL ya da Lua token bucket), kilit, kuyruk/stream (`XADD`/consumer group), pub/sub (kayıp olabilir, kalıcı değil), sıralı küme (liderlik tablosu), `GEO` komutları, SignalR backplane. Dikkat: **bellek** (eviction politikası `allkeys-lru`/`volatile-ttl`, `maxmemory`), **büyük anahtar** ve **sıcak anahtar** (tek thread), `KEYS *` yerine `SCAN`, kalıcılık (RDB/AOF) ayarı, **replikasyon asenkron** (yazma kaybı), Cluster'da çok anahtarlı işlemlerin aynı slot'ta olması (`{hash tag}`). Lisans notu: Redis OSS lisansını 2024'te değiştirdi; AWS ElastiCache **Valkey** (açık kaynak fork) sunuyor; istemci API'si uyumlu.

### 5.6 Mesajlaşma, Outbox, Saga ve idempotency

#### ⭐ Q61. Servisler arası neden doğrudan HTTP/gRPC yerine Kafka gibi bir broker?

**Model cevap (üç avantaj ve bedeli):**
1. **Gevşek bağlılık (zaman ve konum):** Yayıncı mesajı bırakıp devam eder; tüketici o an ayakta olmak zorunda değildir, yeni tüketici eklemek yayıncıyı değiştirmez.
2. **Ölçeklenebilirlik:** Topic **partition**'lara bölünür, anahtara (`device_id`) göre dağılır; **consumer group** içindeki instance sayısı artırılarak okuma yatayda ölçeklenir. Sıra **yalnızca partition içinde** garantilidir.
3. **Dayanıklılık:** Mesajlar diskte **replikalı log**'da tutulur; tüketici çökerse kaldığı **offset**'ten devam eder (offset'i tüketici/consumer group yönetir; log retention süresince silinmez).
**Bedeli:** eventual consistency, ekstra operasyon, hata ayıklama zorluğu, mesaj sırası/tekrar yönetimi. İstek-cevap gereken yerde (kullanıcı cevabı bekliyor) senkron çağrı hâlâ doğrudur.

Kafka'da bilmen gerekenler:
- **Üretici güvenliği:** `acks=all` + `min.insync.replicas=2` + replikasyon faktörü 3; **idempotent producer** (`enable.idempotence`, yeni sürümlerde varsayılan) yeniden denemede duplicate'i engeller.
- **Teslimat:** varsayılan **at-least-once**. "Exactly-once" yalnızca Kafka içinde (idempotent producer + transaction + `read_committed`) geçerlidir; **dış sisteme** (DB, e-posta) yan etki varsa tüketici yine **idempotent** olmalıdır. Offset'i işlem *bittikten sonra* commit et.
- **Rebalance, poison message, DLQ:** bozuk mesaj tüm partition'ı tıkamasın; N denemeden sonra DLQ'ya.
- **Compaction:** anahtar başına son değer (changelog/state topic'leri); **retention:** süre/boyut.
- **Kafka 4.x:** ZooKeeper kaldırıldı, yalnızca **KRaft** (4.0, Mart 2025). Yeni tüketici grup protokolü (KIP-848) ile rebalance daha hafif.
- **.NET istemcisi:** `Confluent.Kafka` (librdkafka); `EnableAutoCommit=false`, `EnableAutoOffsetStore=false` ile elle kontrol.
- **RabbitMQ / SQS / Kinesis ile fark:** RabbitMQ akıllı yönlendirme + kuyruk semantiği (tüketilince silinir), Kafka/Kinesis tekrar okunabilir log, SQS yönetilen iş kuyruğu (§10.1).

#### ⭐ Q62. Dual write problemi nedir? Transactional Outbox nasıl çalışır?

**Model cevap:** Bir iş akışında hem **veritabanına yazıp** hem **broker'a mesaj** atmak iki bağımsız sistemdir, tek transaction'a girmez. Önce DB sonra Kafka'da ağ koparsa: DB güncel, kimsenin haberi yok. Önce Kafka sonra DB'de hata olursa: herkes "para yattı" sanır, yatmamıştır.

**Outbox:** İş verisi ve **gönderilecek mesaj aynı yerel transaction'da** yazılır; ayrı bir *relay* mesajı broker'a taşır.

```csharp
await using var tx = await db.Database.BeginTransactionAsync(ct);
var rows = await db.Balances.Where(b => b.Id == id)
    .ExecuteUpdateAsync(s => s.SetProperty(b => b.Amount, b => b.Amount + amount), ct);
db.Outbox.Add(new OutboxMessage
{
    Id = Guid.CreateVersion7(),                        // .NET 9: zaman sıralı, indeks dostu
    Type = "BalanceUpdated",
    Payload = JsonSerializer.Serialize(new BalanceUpdated(id, amount)),
    OccurredAt = timeProvider.GetUtcNow()
});
await db.SaveChangesAsync(ct);
await tx.CommitAsync(ct);
```

**Relay seçenekleri:**
- **Polling publisher:** worker tabloyu tarar; birden çok relay instance'ı çakışmasın diye `FOR UPDATE SKIP LOCKED`:
  ```sql
  SELECT id, type, payload FROM outbox
  WHERE processed_at IS NULL ORDER BY id LIMIT 100
  FOR UPDATE SKIP LOCKED;
  ```
  (Kısmi indeks: `WHERE processed_at IS NULL`.) Yayınlanınca `processed_at` işaretlenir ya da satır silinir.
- **CDC (Debezium):** PostgreSQL WAL'ını (logical decoding) dinler; polling yükü yok, gecikme düşük, ama ek altyapı (Kafka Connect, replication slot'u izleme: slot şişerse disk dolar).
- **Sıra:** `SKIP LOCKED` ile birden çok relay **global sırayı bozar**. Aggregate bazında sıra gerekiyorsa mesajı aggregate id'yi partition anahtarı yaparak yayınla ve aynı anahtarı tek relay'e yönlendir.

**Bedeli (sınav sorusu):** Outbox **at-least-once** verir; relay yayınladıktan sonra "işlendi" işaretlemeden çökerse mesaj iki kez gider. Bu yüzden tüketici **idempotent** olmalı: **inbox** tablosu (`processed_messages(message_id PRIMARY KEY)`) ya da doğal anahtarla `INSERT … ON CONFLICT DO NOTHING`; aynı transaction içinde "mesajı işledim" kaydı ve iş etkisi birlikte yazılır.

**Telematik:** komut "gönderildi" durumu ile gateway'e iletilecek mesaj (outbox); alarm üretildi kaydı ile bildirim mesajı (outbox).

#### ⭐ Q63. Saga nedir? Orkestrasyon mu koreografi mi?

**Model cevap:** Birden çok servise yayılan iş akışını, her adımı **yerel transaction** olan ve hata olunca **telafi (compensating) işlemleri** çalıştıran bir dizi olarak yönetmektir (örnek: sipariş → cüzdandan düş → abonelik aktif et; abonelik başarısızsa parayı iade et).
- **Koreografi:** servisler birbirinin event'ine tepki verir; merkezi yönetici yok. Az adımda (2–4) basit ve gevşek bağlıdır. Adım artınca akışı kimse görmez ("event spaghetti"), telafi zincirini takip etmek zorlaşır, event sözleşmeleri üzerinden dolaylı sıkı bağlılık doğar.
- **Orkestrasyon:** merkezi bir durum makinesi servislere komut verir, hata olunca telafi komutlarını sırayla yollar; durum izlenebilir, yeni adım eklemek akış tanımına bir satırdır. Araçlar: **Temporal**, **Camunda**, **AWS Step Functions**, **MassTransit saga state machine** (MassTransit v8 açık kaynak; **v9 ticari lisanslı**).
- **Düzeltme (senin notundaki "kesinlikle"):** Adım sayısı ve telafi karmaşıklığı arttıkça **eğilim** orkestrasyona doğrudur; ama orkestratör de bir bağımlılık, darboğaz ve olası SPOF'tur ve hibrit yapılar yaygındır. Mülakatta iki tarafın argümanını ver, kararı koşula bağla: adım sayısı, telafi karmaşıklığı, izlenebilirlik ihtiyacı, ekip sınırları.
- Her adım **idempotent**, her telafi **tekrar çalıştırılabilir** olmalı; zaman aşımı ve "takılı kalmış saga" için süpürücü (reaper) gerekir. Telafi her zaman mümkün değildir (gönderilmiş e-posta geri alınamaz): son adımlar geri alınamazları içermeli.

#### Q64. Idempotency'yi API ve tüketici tarafında nasıl sağlarsın?

**Model cevap:**
- **HTTP API:** istemci `Idempotency-Key` başlığı gönderir; sunucu `(key, istek özeti) → cevap` kaydını benzersiz kısıtla saklar. Aynı anahtar tekrar gelirse **aynı cevabı** döner; aynı anahtar farklı gövdeyle gelirse 422/409. Kayıt TTL'li olur.
- **Tüketici:** mesaj/event kimliği ile `INSERT … ON CONFLICT DO NOTHING`; ya da işi doğal olarak idempotent yap (`SET status='Acked' WHERE status='Sent'`, `UPSERT`).
- **Telematik:** cihaz retransmit eder → `(device_id, ts)` doğal anahtar; komutlarda `command_id` ve durum makinesi.
- Idempotency kilidin yerine geçmez ama onu **tamamlar**: kilit gereksiz çakışmayı azaltır, idempotency doğruluğu garanti eder.

### 5.7 İngilizce Model Cevaplar
- *"How do you choose the column order of a composite index?"* — "By the query patterns: equality columns first, range or sort column last, and I check the plan with EXPLAIN. If both columns are filtered with equality, either order narrows the scan equally, so I look at what other queries and constraints need the index. I don't pick by cardinality alone."
- *"How do you prevent lost updates?"* — "First choice is an atomic update, `SET amount = amount + @x`, or a conditional one with a `WHERE amount >= @x` and a row-count check. If I must read-modify-write, I use optimistic concurrency with a row version, or `SELECT … FOR UPDATE` under high contention, and I retry on conflict."
- *"CP or AP?"* — "I answer per operation. Counters like likes are AP: the button must work and the count can lag. Balance writes favor consistency, so during a partition I reject and rely on idempotency keys for safe retries. And C in CAP means linearizability, not the C in ACID."
- *"How do you handle a cache stampede?"* — "Jittered TTLs, single-flight so only one request refills the key, and refresh-ahead or stale-while-revalidate for hot keys. HybridCache gives per-process protection, but across pods I still need stale serving or a distributed lock, and the lock only improves efficiency; correctness comes from idempotency."
- *"Explain the outbox pattern."* — "I write the business change and the outgoing message in one local transaction, and a relay, polling with SKIP LOCKED or Debezium CDC, publishes to the broker. Delivery is at-least-once, so consumers deduplicate with an inbox table or idempotent upserts."


## 6. Kalite, Dağıtım ve Gözlemlenebilirlik ⭐

### 6.1 Test stratejisi

#### ⭐ Q65. Test piramidi, AAA ve FIRST nedir? Hangi testi ne kadar yazarsın?

**Model cevap:** **Piramit:** tabanda çok sayıda hızlı **unit test**, ortada daha az **integration test** (gerçek DB, kuyruk, HTTP pipeline), tepede az sayıda **E2E**. Tersi ("dondurma külahı") yavaş, kırılgan ve pahalıdır. **AAA:** Arrange (hazırlık), Act (tek eylem), Assert (doğrulama). **FIRST:** Fast, Independent, Repeatable, Self-validating, Timely.
- Unit test saf mantığı izole eder (ağ, disk, saat yok); **integration test** sözleşmeleri doğrular: EF sorguları, SQL, serileştirme, middleware, auth. EF Core InMemory sağlayıcısı gerçek PostgreSQL davranışını taklit etmez; **Testcontainers** ile gerçek PostgreSQL kullan.
- **ASP.NET Core:** `WebApplicationFactory<Program>` ile süreç içi uçtan uca API testi.
- **Araçlar:** xUnit (v3 Aralık 2024'te yayımlandı), NUnit; **NSubstitute** ya da Moq; **Bogus/AutoFixture** (test verisi); **Verify** (snapshot); **Stryker.NET** (mutation testing: testler kodu gerçekten yakalıyor mu?).
- **Coverage bir hedef değil göstergedir.** %100 coverage hatalı testlerle de elde edilir; kritik yollar ve davranış önemli.
- **Mock-heavy test kötü tasarım işaretidir:** bir testte 5+ mock kuruyorsan sınıf çok şey biliyordur (SRP ihlali).
- **Test double ayrımı:** *Dummy* (doldurma), *Stub* (hazır cevap verir), *Spy* (çağrıları kaydeder), *Mock* (beklenen çağrıyı doğrular), *Fake* (basit çalışan uygulama, ör. bellekte repo). Çoğu kişi hepsine "mock" der; ayrımı bil.
- **State vs behavior verification:** dönen değer/durum mu (`Assert.Equal`), yoksa çağrı mı (`Verify`)? Mümkünse durum doğrula, yalnızca yan etki (`void`) olduğunda davranış doğrula.
- **Async test:** `async Task` döndür ve `await` et; `.Result` kullanma. **Zaman:** `TimeProvider` / `FakeTimeProvider` (`Microsoft.Extensions.TimeProvider.Testing`) ile saati kontrol et; `Thread.Sleep` ile test yazma.
- **Lisans notu (senior bilgisi):** **FluentAssertions v8 (Ocak 2025) ticari lisansa geçti** (v7 ve öncesi Apache); açık kaynak fork'u **AwesomeAssertions**, alternatif **Shouldly**. **Moq**, 2023'te SponsorLink ile tartışma yarattı (sonra kaldırıldı); **NSubstitute** güvenli bir alternatiftir.

#### ⭐ Q66. `WalletService.DeductBalance` için "yetersiz bakiye" testini nasıl yazarsın?

**Model cevap:** Her test **tek mantıksal durumu** doğrular: üç senaryoyu tek metoda koymam, ilk assert başarısız olursa diğerleri hiç koşmaz ve hata mesajı neyin bozulduğunu söylemez. Üç ayrı test: `Throws_When_Amount_Is_Not_Positive`, `Throws_When_Funds_Are_Insufficient`, `Succeeds_When_Funds_Are_Sufficient`. Yetersiz bakiye testi: **repository'yi mock'larım** (`GetBalance` 50 dönsün), `DeductBalance(userId, 100)` çağırırım, **exception tipini** ve **`UpdateBalance`'ın hiç çağrılmadığını** (davranış doğrulaması) doğrularım. Metot `void` olduğu için "dönen değeri kontrol ederim" demem; durum ve davranış ayrımını söylerim.

```csharp
[Fact]
public void DeductBalance_Throws_WhenFundsAreInsufficient()
{
    // Arrange
    var repo = Substitute.For<IWalletRepository>();          // Moq ile: new Mock<IWalletRepository>()
    repo.GetBalance("u1").Returns(50m);
    var sut = new WalletService(repo);

    // Act + Assert (exception beklentisinde birleşir)
    var ex = Assert.Throws<InvalidOperationException>(() => sut.DeductBalance("u1", 100m));

    Assert.Equal("Insufficient funds.", ex.Message);
    repo.DidNotReceive().UpdateBalance(Arg.Any<string>(), Arg.Any<decimal>());   // yan etki yok
}

[Theory]
[InlineData(0)]
[InlineData(-5)]
public void DeductBalance_Throws_WhenAmountIsNotPositive(decimal amount) { /* ArgumentException */ }
```

**Ardından kendin söyle (seni "senior" yapan hamle):** "Bu unit test mantığı doğrular ama servis **oku-kontrol et-yaz (check-then-act)** yapıyor: iki eşzamanlı çekim aynı bakiyeyi okuyup ikisi de geçerse bakiye eksiye düşebilir. Unit test bunu yakalayamaz. Çözüm veritabanında koşullu atomik güncelleme (`UPDATE … SET amount = amount - @a WHERE id = @id AND amount >= @a`, §5.3); bunun için **Testcontainers + gerçek PostgreSQL ile paralel çekim integration testi** yazarım:"

```csharp
[Fact]
public async Task ParallelWithdrawals_NeverGoNegative()
{
    await Seed(userId: "u1", balance: 100m);
    var results = await Task.WhenAll(Enumerable.Range(0, 20)
        .Select(_ => Try(() => service.DeductAsync("u1", 10m))));

    Assert.Equal(10, results.Count(r => r));        // tam 10 başarılı
    Assert.Equal(0m, await GetBalance("u1"));       // asla eksiye düşmedi
}
```

#### Q67. TDD nedir? Gerçekten uyguluyor musun?

**Model cevap:** Red → Green → Refactor döngüsü: önce başarısız test, sonra testi geçirecek en basit kod, sonra testler yeşilken iyileştirme. TDD bir test yazma tekniğinden çok bir **tasarım aracıdır**: test edilebilir kod yazmaya çalışmak seni küçük sınıflara, bağımlılıkları dışarıdan almaya (DIP) iter. Dürüst konumlandırma: iş mantığı ve algoritmalarda (geofence, trip tespiti, CRC, sürüş skoru) test-first çok verimlidir; UI ve keşif çalışmasında her zaman değil. Bu soruda abartma, deneyimini doğru ifade et: "Piramit mantığıyla yazarım, kalite kapılarını PR sürecine bağlarım (SonarQube, zorunlu review)."

### 6.2 Dağıtım (deployment)

#### ⭐ Q68. Rolling, Blue-Green ve Canary farkı nedir? Riskli bir değişiklik için hangisi?

**Düzeltilmiş tanımlar:**
- **Rolling:** sunucular gruplar hâlinde sırayla trafikten alınıp güncellenir. Ek ortam gerekmez; ama güncelleme sırasında **kapasite düşer** ve **eski+yeni sürüm birlikte çalışır** (sürüm uyumluluğu şart).
- **Blue-Green:** tam kapasiteli, birebir iki ortam (mavi canlı, yeşil boşta). Yeni sürüm yeşile kurulur, doğrulanır, yük dengeleyicide **trafik tek seferde** yeşile çevrilir. Geri dönüş saniyeler sürer; maliyet iki kat altyapıdır. Dezavantaj: tüm trafik bir anda yeni koda gider.
- **Canary:** yeni sürüm küçük bir kullanıcı dilimine (%1–5) açılır, hata oranı/latency/iş metrikleri izlenir, sorun yoksa %10 → %50 → %100 artırılır; sorun varsa trafik geri çekilir. Amaç sunucuyu değil **kullanıcı riskini** sınırlamaktır.

**Karar:** Yeni, asenkron ve dağıtık bir bakiye hesaplayıcı gibi **sadece gerçek veride ortaya çıkabilecek hataları** (race condition) olan riskli değişiklikte **canary** seçerim: hasar küçük kalır, etkilenen kayıt sayısı azdır. Yanına **feature flag** koyarım (kod dağıtımı ile özellik açmayı ayırır, anında kapatma sağlar) ve gölge (shadow) modda eski/yeni sonucu karşılaştırabilirim.

Eksik kalmaması gereken ayrıntılar:
- **Canary'de gerçek %5:** 1–2 sunucuyla tam %5 olmaz; ağırlıklı yönlendirme (ALB weighted target groups, service mesh, Argo Rollouts/Flagger, API Gateway canary, Lambda alias ağırlığı) gerekir.
- **Veritabanı şema geçişi: expand → migrate → contract.** Önce geriye uyumlu ekle (yeni kolon nullable), kod ikisini de okur/yazar, veriyi taşı, sonra eskiyi kaldır. Eski ve yeni sürüm aynı şemayla çalışabilmeli; yoksa canary/rolling/blue-green hiçbiri güvenli değildir. Geri alınamaz migration'dan kaçın.
- **Connection draining / graceful shutdown:** `SIGTERM`'de yeni istek alma, sürmekte olanı bitir (`IHostApplicationLifetime`, `HostOptions.ShutdownTimeout`), ALB deregistration delay. **Sağlık kontrolleri:** `/health/live` ve `/health/ready`.
- **Telematik tuzağı:** gateway deploy'unda tüm bağlantılar bir anda kopar → cihazlar aynı anda yeniden bağlanır (**reconnect storm**). Bağlantıları kademeli boşalt (drain), cihaz tarafında jitter, yeni instance'ı ısındırmadan trafik verme (§9.3, §11).
- **Mobil:** store'a kademeli rollout (%1 → %100), EAS Update kanalları; rollback JS tarafında kolay, native'de yeni sürüm gerekir (§8.2).
- **Pipeline:** build → test → image tarama → ortam başına promosyon → canary → otomatik rollback eşikleri (hata oranı, p95).

### 6.3 Gözlemlenebilirlik (observability)

#### ⭐ Q69. Mikroservislerde başarısız bir isteği uçtan uca nasıl izlersin?

**Model cevap:** Üç sütun: **dağıtık izleme (trace)**, **merkezi log**, **metrik**. İstekler arasında bağ **W3C Trace Context** ile kurulur: HTTP başlığı `traceparent: 00-<trace-id>-<span-id>-<flags>`. **Trace ID** isteğin uçtan uca kimliğidir (senin "correlation id"n); **Span ID** her servisteki alt işin kimliğidir ve çağrı ağacını (waterfall) çizer. Böylece "API gateway 10 ms, sipariş 20 ms, ödeme 5000 ms ve orada zaman aşımı" görsel olarak görülür. Loglara `TraceId`/`SpanId` otomatik yazılır; arama kutusuna trace id'yi yazınca 5 sunucudaki loglar zaman sırasıyla gelir.

**.NET'te nasıl:**
- `System.Diagnostics.Activity` (trace), `ActivitySource`, `Meter` (metrik) ve `ILogger` API'leri **çatıda yerleşiktir**; `traceparent` varsayılan olarak yayılır (W3C varsayılan format .NET 5'ten beri). **OpenTelemetry SDK'sı ve exporter'lar ise NuGet paketidir.** "OpenTelemetry birinci sınıf destekleniyor" demek yerine bunu doğru söyle.
- OTel: satıcıdan bağımsız standart; OTLP ile Collector'a gönder, oradan Jaeger/Tempo/Grafana, Datadog, CloudWatch/X-Ray'e yönlendir. Araç değişse kod değişmez.
- **Asenkron sınırlar:** Kafka/SQS mesajına `traceparent`'ı **mesaj başlığı** olarak koy; tüketici onu parent olarak alıp yeni span açar (yoksa iz kopar). Outbox satırına trace context'i de yaz.

```csharp
builder.Services.AddOpenTelemetry()
    .ConfigureResource(r => r.AddService("fleet-api"))
    .WithTracing(t => t.AddAspNetCoreInstrumentation().AddHttpClientInstrumentation()
                       .AddNpgsql().AddSource("Fleet.Ingest").AddOtlpExporter())
    .WithMetrics(m => m.AddAspNetCoreInstrumentation().AddMeter("Fleet.Ingest").AddOtlpExporter());
builder.Logging.AddOpenTelemetry(o => o.IncludeScopes = true);   // log + trace korelasyonu
```

**Senior ayrıntıları:**
- **Metrik tasarımı:** RED (Rate, Errors, Duration) servisler için, USE (Utilization, Saturation, Errors) kaynaklar için. **Yüksek kardinalite etiketlerden kaçın** (`device_id` ya da `user_id` metrik etiketi olmaz; metrik sistemini ve faturayı patlatır).
- **Sampling:** her trace saklanmaz; *head* sampling (başta karar, ucuz) ve *tail* sampling (hata/yavaş olanları sakla, Collector'da).
- **Alarm:** semptoma (SLO ihlali, hata oranı) alarm kur, nedene değil. SLO örneği: "cihazdan canlı haritaya p95 < 3 sn".
- **Log:** yapılandırılmış (message template), PII ve konum verisini maskele (KVKK), log seviyesi ve örnekleme, `LoggerMessage` kaynak üreteci.

### 6.4 Cross-cutting concerns ve "AOP"

#### Q70. Cross-cutting concern nedir? .NET'te AOP var mı?

**Model cevap:** İş mantığına ait olmayıp birçok katmanı kesen ilgilerdir: logging, authn/authz, validation, caching, exception handling, transaction, rate limiting, telemetri. Her metoda elle yazarsan **code scattering** (dağınıklık) ve **code tangling** (iç içe geçme) oluşur.

**Düzeltme:** .NET'te **yerleşik AOP yoktur.** Pratikte şunlarla çözülür:
- **ASP.NET Core middleware ve filter'lar** (HTTP katmanı): hata, auth, loglama, doğrulama.
- **Decorator** (Scrutor `Decorate<>`, `DelegatingHandler`): `CachedRepository`, `LoggingCommandSender`, retry sarmalayıcı.
- **Pipeline behavior / mediator pipeline** (handler öncesi-sonrası): validation, transaction, logging. MediatR artık ticari lisanslıdır; seçenekler: kendi basit dispatcher'ın, `Mediator` (kaynak üreteçli), **Wolverine**.
- **Polly / `Microsoft.Extensions.Http.Resilience`:** retry, timeout, circuit breaker, bulkhead.
- **Kaynak üreteçleri** ve (deneysel) *interceptor*'lar: derleme zamanında kod ekler, AOT dostudur.
- **IL weaving** (Fody, PostSharp) ve **dynamic proxy** (Castle DynamicProxy) mümkündür ama Native AOT/trimming ile uyumsuz olabilir ve hata ayıklamayı zorlaştırır.
**Mülakat cümlesi:** "Cross-cutting ilgileri bir zincir olarak (middleware, decorator, pipeline) modellerim; sihirli enjeksiyon yerine açık ve test edilebilir yapıyı tercih ederim."

### 6.5 Kod kalitesi ve tedarik zinciri

#### Q71. Kod kalitesini ve güvenliğini CI'da nasıl güvence altına alırsın?

**Model cevap:**
- **Derleyici seviyesi:** `<Nullable>enable</Nullable>`, `<TreatWarningsAsErrors>`, `.editorconfig`, Roslyn analyzer'ları (`Microsoft.CodeAnalysis.NetAnalyzers`), SonarQube/SonarCloud kalite kapısı.
- **PR süreci:** küçük PR, zorunlu review, review önceliği (doğruluk → güvenlik → okunabilirlik → performans), stil tartışmasını formatter'a bırakma (`dotnet format`).
- **Güvenlik:** `dotnet list package --vulnerable`, Dependabot/Renovate, SBOM, container image taraması, sır tarama (gitleaks). OWASP Top 10: injection (parametreli sorgu), kırık erişim kontrolü, SSRF, güvensiz deserialization (`BinaryFormatter` kaldırıldı), güvensiz konfigürasyon.
- **Bağımlılık ve lisans yönetimi (güncel):** MediatR, AutoMapper (Temmuz 2025'ten itibaren çift lisans/ticari), MassTransit v9, FluentAssertions v8 ticari ya da ücretli hale geldi. Yaklaşım: bağımlılığın **lisans ve sürdürülebilirlik** riskini değerlendir; açık mapping kodu ya da **Mapperly** (kaynak üreteçli) gibi alternatifleri bil; "bırak her şeyi kütüphane yapsın" yerine ihtiyaç duyulan kadarını kendin yaz.
- **Yapı:** `Directory.Packages.props` ile merkezi paket sürümü, `global.json` ile SDK sabitleme, tekrarlanabilir build, `dotnet publish` + container.

### 6.6 İngilizce Model Cevaplar
- *"Why is a test with several scenarios an anti-pattern?"* — "If the first assertion fails the test stops and the others never run, and the failure message doesn't tell you which behavior broke. One logical case per test, named after the behavior."
- *"Blue-green or canary for a risky change?"* — "Canary, with a feature flag. Blue-green flips all traffic at once, so a race condition that only shows up on real data hits everyone immediately. A canary limits the blast radius to a few percent and lets me watch error rate and business metrics before widening."
- *"How do you trace a failed request across microservices?"* — "W3C Trace Context: a `traceparent` header carries the trace ID, and each service creates spans with parent IDs. Logs carry the trace ID, and I propagate it through message headers across Kafka so the trace doesn't break. OpenTelemetry keeps it vendor-neutral."
- *"Is there AOP in .NET?"* — "Not built in. I use middleware, filters, decorators and pipeline behaviors; IL weaving exists but conflicts with trimming and AOT."


## 7. React JS (Web) ve Next.js ⭐

Bu rol "Full Stack" olduğu için React'in **web** tarafı (yönetim paneli, filo haritası, raporlar) React Native kadar sorulabilir. React Native bölümüyle (§8) ortak çekirdek aynıdır: bileşen modeli, hook'lar, state ve performans. Farklar: DOM yerine native view'lar, CSS yerine `StyleSheet`, Next.js'in sunucu tarafı özellikleri.

**Güncel durum (Ekim 2026):** React **19.3** (9 Eylül 2026), Next.js **16.4** (6 Ekim 2026). Create React App Şubat 2025'te kullanımdan kaldırıldı; React belgeleri yeni projeler için bir framework (Next.js, React Router framework modu) ya da SPA için Vite önerir. React, Şubat 2026'da Linux Foundation altındaki **React Foundation**'a taşındı.

### 7.1 Temel React soruları

#### ⭐ Q72. Virtual DOM nedir? Reconciliation ve `key` nasıl çalışır?

**Model cevap:** React, arayüzü bir **bileşen ağacının çıktısı (React element ağacı)** olarak tarif eder. State değişince bileşen yeniden **render** edilir (fonksiyon çağrılır, yeni element ağacı üretilir); React yeni ağacı öncekiyle **karşılaştırır (reconciliation / diffing)** ve gerçek DOM'a yalnızca **gereken farkı** uygular (**commit** aşaması). İki fazı ayır: **render fazı** saf olmalıdır (yan etki yok, iptal edilebilir, Strict Mode'da iki kez çağrılabilir); **commit fazı** DOM'u günceller ve effect'ler çalışır.
- Diff sezgileri: farklı tip → alt ağacı at ve yeniden kur; aynı tip → güncelle; listelerde **`key`** ile eleman kimliği eşlenir.
- **`key` kuralları:** kararlı ve benzersiz (genellikle veritabanı id'si). **Dizi indeksi key olarak** liste yeniden sıralanır/araya eleman girerse state'i yanlış elemana bağlar (input içeriği kayar). `key` aynı zamanda **state'i sıfırlama** aracıdır: `<Form key={userId} />` kullanıcı değişince formu tamamen yeniler.
- Mimari: **Fiber** (kesilebilir, öncelikli iş birimi) sayesinde **concurrent rendering**: React uzun render'ı bölebilir, acil güncellemeyi (yazma) önceleyebilir (`startTransition`, `useDeferredValue`).
- "Virtual DOM hızlıdır" yerine: "Tasarım amacı deklaratif modeli makul maliyetle sunmaktır; hızı belirleyen, gereksiz render'ları ve DOM yazımlarını azaltmandır."

#### ⭐ Q73. Props ve state farkı? Controlled/uncontrolled bileşen? State nerede tutulur?

**Model cevap:** **Props** üst bileşenden gelen, alt bileşence **salt okunur** girdidir; **state** bileşenin kendi, zamanla değişen verisidir. Veri akışı **tek yönlüdür** (yukarıdan aşağı). Kural: state'i, onu kullanan **en yakın ortak üst bileşende** tut (**lifting state up**), ihtiyaç olmadan global yapma (**colocation**). **Türetilebilen veriyi state'e koyma** (örneğin `fullName`'i `first`/`last`'tan render sırasında hesapla); aynı veriyi iki yerde tutma.
- **Controlled input:** değer React state'inde (`value` + `onChange`); doğrulama ve anlık tepki kolay. **Uncontrolled:** değer DOM'da (`ref` ya da `FormData` ile okunur); çok alanlı basit formlarda daha ucuz. React 19'da form **Action**'ları (`<form action={fn}>`) uncontrolled yaklaşımı güçlendirdi.
- **Kompozisyon > kalıtım:** `children` ve render prop'larıyla; **prop drilling** gerçek sorunsa Context ya da state kütüphanesi.
- **State sınıfları:** *server state* (API verisi; TanStack Query), *client/UI state* (modal açık mı), *URL state* (filtre, sayfa; paylaşılabilir ve geri tuşu çalışır), *form state*. Hepsini tek global store'a doldurma.

#### ⭐ Q74. Hook'lar: kurallar ve `useEffect` doğru nasıl kullanılır?

**Model cevap:** **Kurallar:** hook'ları yalnızca üst düzeyde (koşul, döngü, iç fonksiyon içinde değil) ve yalnızca bileşen ya da başka hook içinde çağır; çünkü React hook'ları **çağrı sırasıyla** eşler. ESLint `react-hooks` eklentisi bunu zorlar.
- **`useState`:** güncelleme **asenkron/batch**'tir; önceki değere bağlıysa **fonksiyonel biçim** kullan: `setCount(c => c + 1)`. Nesne/dizi state'ini **değiştirme (mutate)**, yeni kopya ver; yoksa referans aynı kaldığı için render olmaz.
- **`useEffect`:** render'dan sonra çalışan **dış sistemle senkronizasyon** aracıdır (abonelik, timer, ağ, DOM ölçümü). **Her şeyi effect'e yazma:** "You Might Not Need an Effect": türetilmiş veriyi render sırasında hesapla, kullanıcı olayına (click) tepkiyi **event handler**'da yap, veri çekmeyi mümkünse effect yerine kütüphane/framework'e (TanStack Query, Server Components) bırak.
- **Bağımlılık dizisi:** effect'in kullandığı her reaktif değer listelenmeli (linter doğru söyler); `[]` "bir kez" demek değildir, "hiçbir reaktif değer kullanmıyorum" demektir. Nesne/fonksiyon bağımlılıkları her render'da yeni referans olursa effect her seferinde çalışır.
- **Cleanup:** abonelik ve timer'ı geri al, istekleri `AbortController` ile iptal et. **Strict Mode (dev):** bileşenler mount → unmount → mount edilir; cleanup'ı eksik effect burada hemen ortaya çıkar. (19.3'te hydration sırasında da effect'ler Strict Mode'da çift çağrılır.)
- **`useLayoutEffect`:** DOM değişimlerinden sonra, boyamadan önce senkron (ölçüm/konumlama); nadir kullan, SSR'da uyarı verir.
- **`useRef`:** render tetiklemeyen değişken kutu (DOM referansı, timer id, önceki değer). **`useReducer`:** karmaşık/ilişkili state geçişleri. **`useContext`:** bağımlılık enjeksiyonu (tema, kullanıcı); sık değişen veri için değil (tüm tüketicileri render eder).
- **Custom hook:** mantığı yeniden kullanılabilir hale getirir (`useDebouncedValue`, `useOnlineStatus`); "use" ile başlar, kendi state'ini taşır.

```tsx
// Doğru: abonelik + cleanup + iptal
useEffect(() => {
  const ac = new AbortController();
  fetch(`/api/vehicles/${id}`, { signal: ac.signal })
    .then(r => r.json()).then(setVehicle)
    .catch(e => { if (e.name !== 'AbortError') setError(e); });
  return () => ac.abort();
}, [id]);
```

#### ⭐ Q75. Stale closure nedir? `useEffectEvent` ne işe yarar?

**Model cevap:** Her render kendi `props` ve `state` değerlerini **yakalayan (closure)** yeni fonksiyonlar üretir. Bağımlılığı eksik bırakılmış bir effect/handler eski render'ın değerini görmeye devam eder.

```tsx
function Counter() {
  const [n, setN] = useState(0);
  useEffect(() => {
    const t = setInterval(() => setN(n + 1), 1000);   // n hep 0 kalır: sayaç 1'de takılır
    return () => clearInterval(t);
  }, []);                                              // linter uyarır: 'n' eksik
}
// Düzeltme: setN(c => c + 1)  (bağımlılık gerekmez)
```

Başka bir kalıp: effect'in yalnızca *bazı* değerlere tepki vermesi ama *güncel* başka bir değeri okuması gerektiğinde (ör. oda değişince bağlan, bağlantı olunca güncel tema ile bildirim göster). **React 19.2'nin `useEffectEvent`'i** bunu çözer: effect içinden çağrılan, her zaman güncel değerleri gören ama bağımlılık olmayan bir olay fonksiyonu tanımlar.

#### ⭐ Q76. Gereksiz render'ları nasıl azaltırsın? `memo`, `useMemo`, `useCallback` ve React Compiler?

**Model cevap:** Önce **ölç** (React DevTools Profiler, "why did this render"), sonra düzelt. Sıra: (1) **state'i aşağı it / ayır** (colocation): sık değişen state küçük bir alt bileşende kalsın; (2) `children` ile kompozisyon (yeniden render edilmeyen alt ağaç), (3) pahalı alt ağaçta `React.memo`, (4) prop'lara verilen nesne/fonksiyon referansını sabitle (`useMemo`/`useCallback`) **yalnızca** memo'lu çocuğa ya da effect bağımlılığına gidiyorsa, (5) büyük listelerde **sanallaştırma** (TanStack Virtual, react-window), (6) `useDeferredValue`/`startTransition` ile ağır güncellemeyi ertele, (7) kod bölme: `React.lazy` + `Suspense`, dinamik `import()`.
- **React Compiler 1.0 (Ekim 2025, kararlı):** derleme zamanında bileşenleri analiz edip **otomatik memoization** ekler; elle `useMemo/useCallback/memo` ihtiyacını büyük ölçüde azaltır. "React kurallarına" (render saf, state mutate edilmez) uyan kod gerekir; Next.js 16'da `reactCompiler: true` ile entegre (stabil), 16.4'te Rust sürümü deneysel. Yeni kodda önce derleyici, sorun olursa elle optimizasyon.
- `useMemo`/`useCallback` **performans garantisi değil, ipucudur**; yanlış yerde kullanmak maliyet ekler.
- **Context performansı:** değer her değiştiğinde tüm tüketiciler render olur; değeri parçala ya da seçici abonelik veren bir store (Zustand) kullan.

#### ⭐ Q77. React 19'da formlar, Actions ve `use` nedir?

**Model cevap:** **Actions:** `<form action={fn}>` ya da `startTransition(async () => …)` ile asenkron mutasyonlar; React bekleme (pending), hata ve **optimistic** durumu yönetir.
- **`useActionState(action, initialState)`:** action'ın sonucunu/hatasını state olarak verir (eski adı `useFormState`).
- **`useFormStatus()`** (`react-dom`): form içindeki alt bileşende pending durumunu okur.
- **`useOptimistic`:** istek sürerken geçici iyimser arayüz (komut gönderildi → "gönderiliyor…").
- **`use(promise | context)`:** render sırasında Promise'i okur (Suspense ile birlikte) ya da koşullu context okur; hook kuralının istisnası olarak koşul içinde çağrılabilir. Promise'in **render dışında oluşturulup** geçirilmesi gerekir (her render'da yeni promise döngü yaratır).
- Diğer 19.0 değişiklikleri: **`ref` artık normal prop** (`forwardRef` gereksiz), `<Context value>` doğrudan provider, `<title>/<meta>/<link>` bileşen içinde yazılıp head'e taşınır, ref callback'inde cleanup fonksiyonu, daha iyi hydration hata mesajları, custom element desteği.

### 7.2 React 19.x sürüm notları

| Sürüm | Öne çıkanlar |
|---|---|
| **19.0** (Aralık 2024) | Actions, `use`, `useActionState`, `useOptimistic`, `ref` prop, belge metadata'sı, Server Components/Actions kararlı |
| **19.2** (1 Ekim 2025) | `<Activity>` (gizli UI'ın durumunu koruyarak önceliğini düşürme), `useEffectEvent`, `cacheSignal` (RSC), Partial Pre-rendering desteği, React Performance Tracks (Chrome profiler) |
| **React Compiler 1.0** (7 Ekim 2025) | Kararlı otomatik memoization |
| **19.3** (9 Eylül 2026) | `<ViewTransition>` ve `addTransitionType` kararlı (Transition ile tetiklenen giriş/çıkış/paylaşılan geçiş animasyonları, Suspense geçişleri; yalnızca DOM), **Fragment ref'leri** (`<Fragment ref>` çocuk DOM'u grup olarak yönetir: `focus`, `addEventListener`, `observeUsing`, `scrollIntoView`…), **`browser()`** (`use(browser())` ile bileşeni sunucu render'ından çıkarır; sunucuda Suspense fallback'i, istemcide hydration sonrası askıya almaz), **Trusted Types** desteği, Server Component'te doğrudan `<Context value>` render etme, bağımsız Transition render'ı |

**Güvenlik notu (Aralık 2025):** React Server Components'ta **kritik bir uzaktan kod çalıştırma açığı** (CVE-2025-55182) duyuruldu; 19.0.1, 19.1.2 ve 19.2.1'de düzeltildi, ardından iki ek açık (DoS ve kaynak kodu ifşası) bildirildi. Next.js tarafında da 2026'da sık güvenlik sürümleri çıkıyor. **Mülakat cümlesi:** "RSC kullanan uygulamalarda framework ve React sürümlerini güvenlik bültenleriyle takip eder, Dependabot/Renovate ile hızlı yama alırım."

### 7.3 State yönetimi ve veri çekme

#### ⭐ Q78. Redux, Zustand, Context, TanStack Query: ne zaman hangisi?

**Model cevap:**
- **Server state → TanStack Query (v5):** önbellek, stale-while-revalidate, yeniden deneme, arka plan yenileme, mutation + `invalidateQueries`, optimistic update, `useSuspenseQuery`. API verisini Redux'a **kopyalama**.
- **Küçük/orta client state → Zustand** (az kod, seçici abonelik) ya da Jotai; **büyük ekip, katı yapı, DevTools zaman yolculuğu → Redux Toolkit** (RTK Query ile birleşik). **Context** yalnızca seyrek değişen değerler (tema, auth kullanıcısı).
- **URL state:** filtre, sayfa, seçili araç → `searchParams` (Next.js `useSearchParams`/nuqs).
- **Canlı veri (WebSocket/SSE/SignalR):** akışı `queryClient.setQueryData` ya da dar bir store'a yaz; 500 araçlık güncellemeyi her mesajda React state'ine basma, **throttle/batch** et (§7.7).
- **Formlar:** React Hook Form + Zod (şema doğrulama; aynı şema sunucuda da kullanılır), tip güvenliği için `z.infer`.

#### Q79. TypeScript ile React: nelere dikkat edersin?

**Model cevap:** Props için `type`/`interface`; `React.FC` zorunlu değil; **discriminated union** ile geçersiz durumları imkânsız kıl (`{status:'loading'} | {status:'ok', data:T} | {status:'error', error:Error}`); `any` yerine `unknown` + daraltma; generic bileşenler (`<T,>` ile tablo/liste); olay tipleri (`React.ChangeEvent<HTMLInputElement>`); `strict` modu, `noUncheckedIndexedAccess`; API sözleşmesinden tip üretimi (OpenAPI → `openapi-typescript`) ve çalışma zamanı doğrulaması (Zod) çünkü TS tipleri çalışma zamanında silinir. Bu rolde .NET tarafındaki OpenAPI üretimini frontend tip üretimine bağlamak güçlü bir "full stack" cümlesidir.

### 7.4 Web performansı

#### ⭐ Q80. Core Web Vitals nedir? Bir React sayfasını nasıl hızlandırırsın?

**Model cevap:** **LCP** (en büyük içerik boyaması; hedef < 2,5 sn), **INP** (etkileşimden sonraki yanıt; hedef < 200 ms; Mart 2024'te FID'in yerini aldı), **CLS** (düzen kayması; < 0,1). Teşhis: Lighthouse, Chrome Performance paneli, React Profiler, gerçek kullanıcı ölçümü (RUM, `web-vitals`).
- **LCP:** sunucuda render/akış (SSR, streaming, RSC), kritik kaynağı `preload`, görseller (`next/image`: boyut, modern format, lazy, `priority`), CDN/önbellek, font stratejisi (`next/font`).
- **INP:** ana thread'i bloklama: uzun görevleri böl, ağır hesabı Web Worker'a ya da `startTransition`'a al, gereksiz render'ı azalt, büyük listeleri sanallaştır, event handler'ı hafif tut.
- **CLS:** görsel/iframe boyutlarını önceden ver, dinamik içerik için yer ayır.
- **Bundle:** kod bölme (rota bazlı otomatik; bileşen bazlı `lazy`), ağır kütüphaneyi dinamik yükle, tree-shaking, bundle analyzer (Next.js 16.4'te Turbopack Bundle Analyzer), bağımlılık diyeti.
- **Hydration maliyeti:** Server Components istemciye giden JS'i azaltır; etkileşimsiz kısımları sunucuda bırak.

### 7.5 Next.js

#### ⭐ Q81. Next.js App Router ve React Server Components nedir? Client ile farkı?

**Model cevap:** App Router'da (`app/` dizini) bileşenler varsayılan olarak **Server Component**'tir: yalnızca sunucuda çalışır, **istemciye JS göndermez**, doğrudan veritabanı/sır erişimi olabilir, `async/await` ile veri çeker. `'use client'` ile işaretlenen dosya **Client Component** olur: state, effect, tarayıcı API'leri, olay yöneticileri burada çalışır (yine ilk HTML sunucuda üretilir, sonra hydrate edilir). Kural: **`'use client'`'ı ağacın yapraklarına it**; Server Component bir Client Component'i içerebilir, tersi doğrudan değil (`children` olarak geçirilebilir). Server → Client'a geçen props **serileştirilebilir** olmalıdır.
- **Dosya konvansiyonları:** `layout.tsx` (kalıcı, durumu korur), `page.tsx`, `loading.tsx` (Suspense), `error.tsx`, `not-found.tsx`, `route.ts` (Route Handler/API), `proxy.ts` (eskiden `middleware.ts`), `[id]`, `(group)`, `@slot` (parallel routes).
- **Streaming:** `Suspense` sınırlarıyla sayfa parça parça gelir.

#### ⭐ Q82. Next.js'te render stratejileri ve cache modeli nedir? (16.x)

**Model cevap:** Seçenekler: **statik üretim (SSG)**, **ISR / revalidate** (arka planda yenileme), **dinamik render (SSR, istek anında)**, **PPR** (aynı sayfada statik kabuk + dinamik delikler, streaming). Next.js 16 ile yeni model **Cache Components**: önbellekleme **açıkça ve bileşen düzeyinde** `'use cache'` ile yapılır (bileşenin ya da fonksiyonun çıktısını önbelleğe al); `cacheLife('hours')` ile süre profili, `cacheTag` + `revalidateTag`/`updateTag` ile geçersiz kılma; dinamik veri `Suspense` içinde istek anında akar. Eski "örtük" fetch önbelleği modelinin yerini alır.

```tsx
// next.config.ts → cacheComponents: true
import { cacheLife, cacheTag } from 'next/cache';

async function FleetSummary({ tenantId }: { tenantId: string }) {
  'use cache';
  cacheLife('minutes');
  cacheTag(`fleet:${tenantId}`);
  const s = await db.fleetSummary(tenantId);   // sunucuda
  return <SummaryCard data={s} />;
}
// Mutasyondan sonra: updateTag(`fleet:${tenantId}`)  (Server Action içinde, anında okuma tutarlılığı)
```

- **Durum:** Next.js 16.4 (6 Ekim 2026) Cache Components'ı **her yeni uygulama için önerir**; `create-next-app` ile açık gelir; **Next.js 17'de varsayılan** olacak. 16.4'te `ensureStatic` (rotanın statik kalmasını build'de zorunlu kılar), `navigation()`/`prefetch()` (prefetch'ten içerik hariç tutma) geldi.
- **Next.js 16 ile gelenler (Ekim 2025):** Turbopack varsayılan bundler, `middleware` → **`proxy.ts`** (Node.js runtime), **`params`/`searchParams` asenkron** (`await params`), React Compiler entegrasyonu (stabil), React 19.2, `updateTag`/`refresh`, Node.js 20.9+ gereksinimi. Eski bir projeyi yükseltirken bunlar breaking change'lerdir; `next upgrade --agent` ve codemod'lar yardımcı olur.
- **Mülakat cümlesi:** "Önbelleği varsayılan değil, **bilinçli** seçerim: kullanıcıya özel veri önbelleğe girmesin, paylaşılan veri için `'use cache'` + etiketle geçersiz kılma."

#### ⭐ Q83. Server Actions nedir? Güvenlik açısından nelere dikkat edersin?

**Model cevap:** `'use server'` ile işaretlenen asenkron fonksiyon sunucuda çalışır ve form `action`'ı ya da istemciden doğrudan çağrılabilir; form gönderimi için JS'siz çalışabilir (progressive enhancement). **Kritik nokta: her Server Action, dışarıdan çağrılabilen herkese açık bir HTTP uç noktasıdır.** Bu yüzden:
- Her action içinde **kimlik doğrulama ve yetkilendirme** (kullanıcı bu araca erişebilir mi?) yeniden kontrol edilir; arayüzde butonu gizlemek güvenlik değildir.
- Girdiyi **sunucuda Zod ile doğrula**; istemciye güvenme.
- Sırları ve iç nesneleri istemciye döndürme (serileştirilen her şey ağdan geçer); `server-only` paketi ile sunucu modüllerini istemciye sızmaktan koru.
- CSRF: Next.js aynı kaynak (Origin) kontrolü yapar; yine de çerez tabanlı oturumda `SameSite=Lax/Strict` ve hassas işlemlerde ek doğrulama.

```tsx
'use server';
import { z } from 'zod';
const Cmd = z.object({ vehicleId: z.string().uuid(), command: z.enum(['immobilize', 'unlock']) });

export async function sendCommand(_: unknown, form: FormData) {
  const session = await requireSession();                       // authn
  const input = Cmd.parse(Object.fromEntries(form));            // doğrulama
  if (!await can(session.user, 'command', input.vehicleId))     // authz (nesne bazlı)
    throw new Error('forbidden');
  await api.sendCommand(input, { idempotencyKey: crypto.randomUUID() });
  updateTag(`vehicle:${input.vehicleId}`);
}
```

#### Q84. Auth ve oturum Next.js'te nasıl yapılır? Token nerede saklanır?

**Model cevap:** Tarayıcıda **access token'ı `localStorage`'a koyma** (XSS ile çalınır). Tercih: **BFF deseni**: Next.js sunucusu OIDC (authorization code + PKCE) akışını yürütür, token'ları sunucuda tutar ya da **şifreli, `HttpOnly; Secure; SameSite` çerez** ile oturum verir; tarayıcıdan .NET API'ye çağrıları Route Handler/Server Action üzerinden proxy'ler ya da kısa ömürlü token ile yapar. Kütüphane: Auth.js (NextAuth), Cognito/Entra ID/Keycloak ile OIDC. Koruma katmanları: `proxy.ts` (iyimser, hızlı yönlendirme), ama **asıl yetki kontrolü veri erişim katmanında** (DAL) yapılır; yalnızca proxy'ye güvenme. Rol/kiracı `tenant_id` claim'inden gelir; .NET API ayrıca doğrular (§2.7 Q38).

#### Q85. `NEXT_PUBLIC_` ve ortam değişkenleri; görsel/font; metadata; SEO?

**Model cevap:** `NEXT_PUBLIC_` önekli değişkenler **build sırasında istemci bundle'ına gömülür** (sır koyma!); diğerleri yalnızca sunucuda. Görseller `next/image` (boyutlandırma, lazy, modern format), fontlar `next/font` (self-host, CLS yok). SEO: `generateMetadata`, `sitemap.ts`, `robots.ts`, yapılandırılmış veri; iç yönetim panelinde (giriş arkasında) SEO önemsizdir, SSR'ın sebebi ilk yükleme hızı ve güvenlik (sırların sunucuda kalması) olabilir.

#### Q86. Next.js'i AWS'te nasıl yayınlarsın?

**Model cevap:** Seçenekler: (1) **Container**: `output: 'standalone'` ile Docker imajı → **ECS Fargate** + ALB + CloudFront (tam Node.js desteği, öngörülebilir). (2) **Amplify Hosting** (yönetilen, hızlı kurulum). (3) **OpenNext / SST** ile Lambda + CloudFront (sunucusuz; cold start ve servis sınırlarını bil). (4) Tamamen statik ihtiyaç: S3 + CloudFront. Önbellek: CloudFront önünde, ISR/Cache Components çıktısının paylaşımı için çok instance'ta paylaşımlı cache handler (Redis/S3) gerekebilir. Görsel optimizasyonu CPU yoğundur; CloudFront ve ayrı servis düşün. Sırlar Secrets Manager/Parameter Store, loglar CloudWatch, trace OpenTelemetry.

### 7.6 Telematik web arayüzü

#### ⭐ Q87. Tarayıcıda 2.000 aracın canlı konumunu nasıl çizersin?

**Model cevap:** React'in render döngüsü bu işin yeri değildir; **haritayı imperatif, veriyi akış olarak** ele al.
- **Harita:** MapLibre GL JS ya da Mapbox GL JS (WebGL, GPU). Araçları **GeoJSON source + symbol/circle layer** olarak çiz; her araç için React bileşeni/DOM marker oluşturma. 2.000+ için **cluster** ve/veya **deck.gl** katmanları.
- **Veri akışı:** SignalR (`@microsoft/signalr`), WebSocket ya da SSE. Mesaj başına React state güncelleme; mesajları bir `Map<vehicleId, state>`'te biriktir, **1 sn'de bir toplu** `source.setData(...)` (ya da delta) ile haritayı güncelle. Hareket yumuşatma: iki konum arası interpolasyon (`requestAnimationFrame`).
- **Abonelik daraltma:** yalnızca **görünür bbox + yakınlaştırma düzeyi** için abone ol; harita hareket edince debounce ile abonelik güncelle.
- **Liste/tablo:** yan paneldeki araç listesi **sanallaştırılmış** (TanStack Virtual), filtre/arama `useDeferredValue` ile.
- **Başlangıç:** REST ile snapshot al, sonra akışa bağlan; bağlantı kopunca yeniden bağlan + snapshot'ı yenile (kaçan güncellemeler için). Sekme arka plandayken akışı durdur (`visibilitychange`).
- **Geçmiş yolculuk (replay):** büyük rota için sunucuda Douglas–Peucker ile sadeleştir, çizgiyi tek bir `LineString` olarak ver; zaman çizelgesi kaydırıcısı.
- **Grafikler:** yüksek noktalı zaman serisi için canvas tabanlı (uPlot, ECharts); SVG tabanlı kütüphaneler binlerce noktada yavaşlar. Sunucuda **downsampling** (continuous aggregate, LTTB).

```tsx
// Akışı React'ten ayır: tampon + toplu harita güncellemesi
const buffer = useRef(new Map<string, Vehicle>());
useEffect(() => {
  const onPositions = (batch: Vehicle[]) => batch.forEach(v => buffer.current.set(v.id, v));
  hub.on('positions', onPositions);                       // @microsoft/signalr HubConnection
  const t = setInterval(() => {
    map.getSource<GeoJSONSource>('vehicles')?.setData(toFeatureCollection(buffer.current));
  }, 1000);
  return () => { hub.off('positions', onPositions); clearInterval(t); };
}, [hub, map]);
```

#### Q88. Komut gönderme ekranını (immobilize, kilit aç) nasıl tasarlarsın?

**Model cevap:** Komut **asenkron** bir iştir (cihaz offline olabilir): API `202 Accepted` + `commandId` döner; arayüz durum makinesini gösterir (Oluşturuldu → Kuyrukta → Gönderildi → Teslim edildi → Uygulandı | Zaman aşımı | Başarısız), durum güncellemeleri SignalR ile gelir. **`Idempotency-Key`** ile çift tıklama/yeniden deneme çift komut üretmez; kritik komutta **onay adımı** (tehlikeli işlem), rol bazlı yetki, denetim kaydı ve arayüzde güvenlik koşulu (araç hareket halindeyken engelle). `useOptimistic` ile "gönderiliyor" durumunu hemen göster, sunucu gerçeğiyle uzlaştır.

### 7.7 Test, güvenlik ve erişilebilirlik

#### Q89. React uygulamasını nasıl test edersin?

**Model cevap:** **Vitest/Jest + React Testing Library** (kullanıcının gördüğüne ve yaptığına göre test: `getByRole`, `userEvent`; implementasyon detayını (state, instance) test etme), **MSW** ile ağ sözleşmesini taklit et, **Playwright** ile birkaç kritik uçtan uca akış (giriş, komut gönderme), **Storybook** ile bileşen vitrini ve görsel regresyon, `axe` ile erişilebilirlik testi. Server Component'ler için doğrudan birim test sınırlıdır; veri katmanını ayrı test et, sayfa davranışını Playwright ile doğrula. Test piramidi burada da geçerlidir (§6.1).

#### ⭐ Q90. Web güvenliği: XSS, CSRF, CORS, CSP ve token saklama?

**Model cevap:**
- **XSS:** React çıktıyı varsayılan olarak **kaçışlar**; risk `dangerouslySetInnerHTML`, `href="javascript:"`, üçüncü taraf script. Kullanıcı HTML'i gerekiyorsa DOMPurify. **CSP** (nonce tabanlı, `script-src`) ve **Trusted Types** (React 19.3 desteği) ile savunmayı katmanla.
- **CSRF:** Çerez tabanlı oturumda önemli: `SameSite=Lax/Strict`, durum değiştiren istekleri `GET` yapma, CSRF token ya da Origin doğrulaması. `Authorization: Bearer` başlığı kullanan SPA bu saldırıya daha az açıktır ama XSS'e karşı token'ı çalınabilir.
- **CORS:** tarayıcı politikasıdır, sunucuyu korumaz; yalnızca gerekli kökenleri, `credentials` ile `*` kullanma; ASP.NET Core `UseCors` ile politikayı açık tanımla; preflight (`OPTIONS`) çalışmalı.
- **Token:** access token kısa ömürlü, refresh token rotasyonu, tercihen `HttpOnly` çerez + BFF (Q84).
- **Tedarik zinciri:** `npm audit`, lockfile, ölçülü bağımlılık, `postinstall` betiklerine dikkat, Dependabot; 2025'te npm paketlerine yönelik büyük saldırılar yaşandı.
- **Erişilebilirlik (a11y):** semantik HTML, klavye erişimi, odak yönetimi (modal'da focus trap), `aria-*` yalnızca gerektiğinde, renk kontrastı; harita gibi görsel bileşenlerin metin alternatifi (liste görünümü).

### 7.8 Kod okuma soruları (React)

```tsx
// 1) Batch ve fonksiyonel güncelleme
const [c, setC] = useState(0);
const add3 = () => { setC(c + 1); setC(c + 1); setC(c + 1); };         // +1 (üçü de aynı 'c' değerini görür)
const add3ok = () => { setC(x => x + 1); setC(x => x + 1); setC(x => x + 1); };   // +3

// 2) Koşullu render tuzağı
{items.length && <List items={items} />}         // length 0 ise ekranda "0" görünür; düzeltme: items.length > 0 &&

// 3) State'i mutate etmek
setUser(u => { u.name = 'X'; return u; });       // aynı referans: render olmaz. Doğrusu: ({ ...u, name: 'X' })

// 4) Her render'da yeni nesne bağımlılığı
useEffect(() => { fetchData(opts); }, [opts]);   // opts = { a: 1 } her render'da yeni referans: sonsuz effect.  Çözüm: primitive bağımlılık ya da useMemo

// 5) Key olarak indeks
{todos.map((t, i) => <TodoInput key={i} ... />)} // sıra değişince input içerikleri yanlış satıra kayar; key={t.id}

// 6) Strict Mode (dev)
useEffect(() => { console.log('mount'); return () => console.log('cleanup'); }, []);
// dev'de: mount, cleanup, mount (üretimde bir kez). Cleanup'ı doğru yazmadığını hemen gösterir.
```

### 7.9 Live-coding: sık çıkan React görevleri

```tsx
// Debounce'lu arama hook'u (+ iptal edilebilir istek)
export function useDebouncedValue<T>(value: T, ms = 300) {
  const [v, setV] = useState(value);
  useEffect(() => { const t = setTimeout(() => setV(value), ms); return () => clearTimeout(t); }, [value, ms]);
  return v;
}

function Search() {
  const [q, setQ] = useState('');
  const dq = useDebouncedValue(q);
  const { data, isPending, error } = useQuery({
    queryKey: ['search', dq],
    queryFn: ({ signal }) => fetch(`/api/search?q=${encodeURIComponent(dq)}`, { signal }).then(r => r.json()),
    enabled: dq.length > 1,
  });
  return (<>
    <input value={q} onChange={e => setQ(e.target.value)} aria-label="Ara" />
    {isPending ? <Spinner /> : error ? <ErrorMsg error={error} /> : <Results items={data} />}
  </>);
}
```

Konuşulacaklar: neden TanStack Query (iptal, önbellek, yarış koşulu: eski yanıtın yenisini ezmesi), yarış koşulu elle çözülecekse `AbortController`/`ignore` bayrağı, boş/yükleniyor/hata durumları, erişilebilirlik (`aria-live`). Diğer görevler: sonsuz kaydırma (`IntersectionObserver` + `useInfiniteQuery`), modal (`createPortal`, focus trap, ESC), todo listesi (key, immutability), `useFetch` hook'u, basit tablo sıralama/filtreleme (türetilmiş veri, `useMemo`).

### 7.10 React web ile React Native arasında kod paylaşımı

**Model cevap:** Paylaşılabilir: **iş mantığı, tipler, API istemcisi (OpenAPI'den üretilmiş), doğrulama şemaları (Zod), TanStack Query hook'ları, state store'ları**. Paylaşılamayan/uyarlanan: UI primitive'leri (`div` ↔ `View`), stil, navigasyon, haritalar. Yaklaşım: **monorepo** (pnpm/Nx/Turborepo) ile `packages/core` (mantık) ve `packages/ui` (platforma özel bileşenler); Expo Router + Next.js için Solito gibi çözümler ve react-native-web mümkündür ama "tüm UI'ı paylaş" hedefi çoğu zaman pahalıdır. Senin hikâyen: "Unified tech stack (.NET + React + React Native)" öğrenme eğrisini ve ekipler arası geçişi kısalttı (§11.5).

### 7.11 İngilizce Model Cevaplar
- *"When do you use useEffect?"* — "To synchronize with something outside React: subscriptions, timers, network, DOM measurements. Derived data belongs in render, user-triggered logic in event handlers, and data fetching in a library like TanStack Query or in Server Components."
- *"What are React Server Components?"* — "Components that render only on the server, ship no JavaScript to the client and can access data directly. Client Components, marked with 'use client', handle interactivity. I push the client boundary to the leaves and treat every Server Action as a public endpoint that needs its own authorization."
- *"How would you render thousands of live vehicles in the browser?"* — "I keep React out of the hot path: WebSocket updates go into a buffer, and once per second I update a GeoJSON source on a WebGL map with clustering. I subscribe only to the visible viewport and virtualize the side list."
- *"Where do you store auth tokens in a web app?"* — "Not in localStorage. I use a backend-for-frontend that runs the OIDC code flow with PKCE and keeps the session in an HttpOnly, Secure, SameSite cookie, and I enforce authorization again at the data layer."
- *"What changed in Next.js 16?"* — "Turbopack by default, `proxy.ts` replacing middleware, async `params`, the React Compiler integration, and Cache Components with explicit `'use cache'` replacing implicit caching. 16.4 recommends Cache Components for every app and it becomes the default in 17."


## 8. React Native

### 8.1 New Architecture (Fabric, TurboModules, JSI) ve Hermes

**S: New Architecture neyi değiştirdi?**
Model cevap: Eski mimaride JS ile native arasındaki iletişim asenkron, JSON serileştirmeli bir **bridge** üzerinden yürüyordu. Bu, büyük listelerde ve gesture'larda darboğaz yaratıyordu.
- **JSI:** JS motorunun C++ nesnelerine doğrudan referans tutmasını sağlar. Senkron çağrı mümkündür ve serileştirme yoktur.
- **TurboModules:** Native modüllerin lazy yüklenmesi ve tip güvenli olması. Codegen ile TS spec'ten C++/Kotlin/ObjC arayüzü üretilir.
- **Fabric:** C++ çekirdekli renderer. Senkron layout ölçümü ve concurrent React desteği (`startTransition`, `useDeferredValue`) sağlar.
- **Bridgeless mode:** Bridge nesnesinin tamamen kaldırılması.
- **Durum:**
  - 0.76'da New Architecture varsayılan oldu.
  - 0.82'de (Ekim 2025) Legacy Architecture kaldırıldı; `newArchEnabled=false` artık yok sayılıyor.
  - 0.84'te Hermes V1 varsayılan oldu.
  - 0.85'te yeni animasyon backend'i geldi.
  - 0.86'da (Haziran 2026) Android 15+ edge-to-edge düzeltmeleri geldi ve kullanıcıya dönük breaking change yok.
  - 0.87 yayımlandı: strict TypeScript API, Swift Package Manager, AGP 9.
- **Migration stratejisi:** Önce RN 0.81 / Expo SDK 54'te New Architecture'ı açıp test edin, sonra yükseltin. Interop katmanları hâlâ var, ama Reanimated v4 ve FlashList v4 yalnızca New Architecture'da çalışıyor.
- **Hermes:** AOT bytecode derlemesi sayesinde açılış hızlıdır ve bellek kullanımı düşüktür. **Güncel risk:** Expo SDK 56 changelog'una göre Hermes V1 ile `react-native-worklets`/Reanimated birlikte kullanıldığında bilinen bir bellek regresyonu var.

**Takip sorusu:** "JSI ile senkron çağrı her zaman iyi midir?" Hayır. UI thread'ini ya da JS thread'ini bloklayan ağır senkron işler jank yaratır. Ağır işler native thread'e taşınmalı ve sonuç Promise ile dönmeli.

### 8.2 Expo

- **Managed vs bare:** Bugün ayrım "prebuild (CNG) + config plugin'ler" ile "elle yönetilen native klasörler" arasındadır. Native kodu `npx expo prebuild` ile üretip `app.config.ts` + config plugin'lerle yönetmek upgrade'leri ucuzlatır.
- **EAS Build:** Bulutta imzalı build. Credentials yönetimi, build profilleri (development/preview/production).
- **EAS Submit:** Store'a gönderim.
- **EAS Update:** JS bundle ve asset için OTA güncelleme. `runtimeVersion` politikası (ör. `fingerprint`) native uyumsuzluğu önler. Kanal ve branch'lerle kademeli dağıtım ve rollback yapılır.
- **Config plugins:** `AndroidManifest`/`Info.plist`'i deklaratif değiştirir, örneğin arka plan konum izni ve FGS tipi.
- **SDK 56 (Mayıs 2026):** RN 0.85, React 19.2, Android 7+ / iOS 16.4+. Expo UI stabil hale geldi. Expo Router, React Navigation'dan fork'landı (`@react-navigation/*` importları kırılabilir; codemod mevcut). iOS build hızlandırması için precompiled paketler. `expo/fetch` global fetch'in yerini aldı.
- Bir üçüncü taraf rehbere göre SDK 57, RN 0.86 ile geliyor. Bunu changelog'dan teyit edin.

### 8.3 Performans

- **Listeler:** `FlatList` için `keyExtractor`, `getItemLayout` (sabit yükseklikte), `windowSize`, `maxToRenderPerBatch`, `removeClippedSubviews`. Uzun ve heterojen listelerde FlashList (cell recycling; v4 New Architecture gerektirir).
- **Re-render yönetimi:** `React.memo`, `useMemo`/`useCallback` (yalnızca referans eşitliği gerçekten önemliyse). Zustand selector'ları ya da Redux `useSelector` + `shallowEqual` ile dar abonelik. Context'i sık değişen veri için kullanmayın.
- **Canlı harita:** 1 sn'de bir gelen 500 araç konumunu doğrudan state'e basmayın. Throttle edin (ör. 1 sn'de batch), görünür bölgeye göre filtreleyin, marker'ları cluster'layın, `tracksViewChanges={false}` kullanın. Mapbox'ta ShapeSource + SymbolLayer (GPU tarafında render), React marker bileşeninden çok daha ölçeklenebilirdir.
- **Animasyon:** Reanimated worklet'leri UI thread'de çalışır.
- **Ölçüm:** React Native DevTools profiler, Perfetto/Android Studio profiler, Flashlight.

### 8.4 State Yönetimi

- **Server state:** TanStack Query. Cache, stale-while-revalidate, retry, `onlineManager` ile offline farkındalığı, query persist.
- **Client/global state:** Zustand (az boilerplate, selector'lar) ya da Redux Toolkit (büyük ekip, DevTools, RTK Query, middleware).
- **Kural:** Server state'i Redux'a kopyalamayın. Canlı konum akışını WebSocket'ten alıp `queryClient.setQueryData` ile güncelleyin.
- **Navigation:** React Navigation (native-stack) ya da Expo Router (dosya tabanlı, deep link'ler hazır). Auth akışında koşullu stack kullanılır.

### 8.5 Native Modüller (Kotlin)

- Expo Modules API: Kotlin/Swift DSL, en ergonomik seçenek. SDK 56'da inline modüller geldi.
- Saf RN'de TurboModule: TS spec → codegen → Kotlin implementasyonu.
```kotlin
class BatteryModule(ctx: ReactApplicationContext) : NativeBatterySpec(ctx) {
  override fun getLevel(promise: Promise) {
    val bm = reactApplicationContext.getSystemService(Context.BATTERY_SERVICE) as BatteryManager
    promise.resolve(bm.getIntProperty(BatteryManager.BATTERY_PROPERTY_CAPACITY))
  }
}
```
- Event'ler native'den JS'e emitter ile gönderilir. Uzun işler coroutine ile `Dispatchers.IO`'da çalıştırılır.

### 8.6 Arka Plan Görevleri ve Konum Takibi

**S: Sürücü uygulamasında arka plan konum takibini nasıl kurarsınız?**
Model cevap:
- **Android:**
  - Kullanıcının başlattığı bir "vardiya" için **foreground service** kullanırım. Kalıcı bir bildirim gösterilir.
  - Android 14+ (API 34) hedefleniyorsa manifest'te `android:foregroundServiceType="location"` ve `FOREGROUND_SERVICE_LOCATION` izni zorunludur. Aksi halde `startForeground` `SecurityException` fırlatır.
  - Konum FGS'i, `ACCESS_BACKGROUND_LOCATION` izni yoksa uygulama arka plandayken başlatılamaz. Bu yüzden servisi uygulama ön plandayken başlatırım.
  - İzin akışı kademelidir: önce fine/coarse, sonra ayrı bir ekranda "Her zaman izin ver". Android 11+ bu izin için kullanıcıyı ayarlara yönlendirir.
  - Play Console'da FGS tipi beyanı ve arka plan konum gerekçesi gerekir ("vehicle activity tracking" kabul edilen bir örnektir).
- **iOS:**
  - `UIBackgroundModes: location`, "Always" izni, `allowsBackgroundLocationUpdates`.
  - Pil için significant-change ya da region monitoring.
- **Pil optimizasyonu:** Hareket algılamaya göre adaptif örnekleme (durağanken GPS kapalı), `distanceFilter`, batch'li upload, Doze ve OEM pil yöneticileri (Xiaomi, Huawei) için kullanıcı rehberi.
- **Kütüphaneler:** `expo-location` + `expo-task-manager` ya da daha kapsamlı ihtiyaçlar için `react-native-background-geolocation` (ticari).
- **Telematik bağlamı:** Mobil konum ikincil kaynaktır. Araçtaki tracker birincil kaynaktır. İkisi arasında veri birleştirme ve çakışma kuralları tanımlayın.

### 8.7 Offline-first ve Senkronizasyon

- Yerel DB: SQLite (expo-sqlite, op-sqlite), WatermelonDB ya da MMKV (key-value).
- **Outbox kuyruğu:** Konum ve form kayıtları yerel tabloya yazılır. Ağ geldiğinde batch POST yapılır; client-generated UUID idempotency key olarak kullanılır.
- Çakışma çözümü: last-write-wins, alan bazlı birleştirme ya da sunucu otoritesi. Zaman damgası için cihaz saati yerine monotonic sayaç + sunucu saati.
- NetInfo ile ağ durumu izlenir. TanStack Query'de `networkMode: 'offlineFirst'`.

### 8.8 Push, Güvenlik, Test, CI/CD, Crash Raporlama

- **Push:** FCM/APNs, `expo-notifications`, Expo Push servisi ya da doğrudan FCM HTTP v1. Data-only mesajlar ve Android notification channel'ları. Alarm bildirimlerinde öncelik ve tekrar bastırma (dedup) önemlidir.
- **Güvenlik:**
  - Token'ları `expo-secure-store` / Keychain / Android Keystore'da tutun, AsyncStorage'da değil.
  - Certificate pinning (`react-native-ssl-pinning` ya da OkHttp `CertificatePinner`). Pin rotasyon planı (yedek pin) olmazsa uygulamayı kendiniz kilitlersiniz.
  - Kısa ömürlü access token + refresh token, OAuth PKCE (Cognito Hosted UI).
  - Root/jailbreak tespiti ve obfuscation (R8/ProGuard) yalnızca ek savunma katmanıdır.
  - Gizli anahtarlar bundle'a konmaz; JS bundle okunabilir.
- **Test:**
  - Jest + React Native Testing Library (davranış odaklı).
  - MSW ile API mock.
  - E2E için Detox (gray-box, senkronizasyon avantajı) ya da Maestro (YAML, hızlı kurulum, flakiness daha düşük).
- **CI/CD:**
  - GitHub Actions: lint, typecheck, test → EAS Build → EAS Submit (iç test kanalı) → kademeli rollout.
  - Fastlane alternatifi.
  - Versiyonlama: `versionCode`/`buildNumber` otomatik artırılır.
  - Store süreçleri: TestFlight, Play internal/closed testing, review notları (konum izni gerekçesi ve demo hesabı).
- **OTA kuralları:** Yalnızca JS ve asset değişiklikleri OTA ile gönderilebilir. Native değişiklik gerektiren güncelleme store'dan geçmelidir. Apple yönergeleri uygulamanın amacını değiştiren OTA'ya izin vermez.
- **Crash raporlama:** Sentry ya da Crashlytics. Hermes source map'leri ve dSYM yükleyin. Release health ve ANR takibi yapın.

### 8.9 İngilizce Model Cevaplar
- *"What does JSI give you over the bridge?"* — "Direct, synchronous references between JS and C++ host objects, with no JSON serialization. That enables TurboModules and Fabric. Since 0.82 it's the only architecture, so migration is no longer optional."
- *"How do you track location in the background on Android 14?"* — "A user-initiated foreground service of type location, with FOREGROUND_SERVICE_LOCATION declared, started while the app is visible, plus a staged permission flow. I add adaptive sampling and batched uploads to protect the battery."
- *"How would you render 2,000 live vehicles on a map?"* — "Throttle updates into one-second batches, cull to the viewport, cluster, and render through native GeoJSON layers instead of React marker components."


## 9. Araç Telematiği

### 9.1 Cihaz ve Veri Akışı Mimarisi

**Kaynaklar:**
- GPS tracker (GNSS + GSM/LTE-M/NB-IoT modemi, dijital/analog I/O, ivmeölçer)
- OBD-II (binek araçlar; PID'ler hız, RPM ve yakıt seviyesi verir)
- CAN bus (doğrudan ya da CAN adaptörü / temassız okuyucu ile)
- **FMS Standard:** Ağır ticari araçlar için. ACEA kökenli, J1939-71 tabanlı bir alt kümedir. Fiziksel katman ISO 11898-2, 250 kbit/s.

**Sık kullanılan J1939 parametreleri (FMS):**

| Parametre | PGN | SPN | Çözünürlük |
|---|---|---|---|
| Engine speed | EEC1, 61444 (0xF004) | 190 | 0,125 rpm/bit |
| Total fuel used | LFC, 65257 (0xFEE9) | 250 | 0,5 L/bit |
| Total engine hours | HOURS, 65253 (0xFEE5) | 247 | 0,05 h/bit |
| Fuel rate | LFE, 65266 (0xFEF2) | 183 | 0,05 L/h/bit |
| Coolant temperature | ET1, 65262 (0xFEEE) | 110 | 1 °C/bit, −40 °C offset |

Diğer FMS parametreleri: wheel-based speed, fuel level, high-resolution total vehicle distance, tachograph verileri, fren ve debriyaj anahtarları, VIN.

- **Mülakat inceliği:** Yakıt tüketimini GPS'ten tahmin etmek yerine CAN'deki total fuel used sayacının **farkını** alın. Sayaç sıfırlanmasına ve wrap-around'a dikkat edin.
- **Akış:** Cihaz → (TCP/UDP/MQTT) → protocol gateway → kanonik event → stream (Kafka/Kinesis) → [storage writer, alarm engine, trip processor, live push] → TimescaleDB/PostGIS + S3 arşivi.

### 9.2 Protokoller: Teltonika Codec 8/8E, Queclink, MQTT

**Teltonika TCP akışı (resmi Teltonika wiki):**
1. Cihaz bağlanır. Önce 2 byte IMEI uzunluğu, ardından ASCII IMEI gönderir. Sunucu kabul için `0x01`, ret için `0x00` döner.
2. AVL paketi: `0x00000000` preamble (4 B), Data Field Length (4 B), Codec ID (1 B: `0x08` Codec 8, `0x8E` Codec 8 Extended, `0x10` Codec 16), Number of Data 1 (1 B), AVL kayıtları, Number of Data 2 (1 B), CRC-16 (4 B, ilk 2 byte sıfır).
3. **CRC-16/IBM**, Codec ID'den Number of Data 2'ye kadar hesaplanır.
4. Her kayıtta şu alanlar var: timestamp (8 B, Unix epoch ms), priority, GPS elementi (longitude/latitude 4 B ×10⁷, altitude, angle, satellites, speed) ve IO elementi.
5. **Codec 8E farkı:** IO ID ve sayaç alanları 1 yerine 2 byte'tır. Değişken uzunluklu (NX) IO grubu da vardır.
6. Sunucu, kabul edilen kayıt sayısını 4 byte big-endian olarak ACK'ler. ACK gelmezse cihaz aynı kayıtları **yeniden gönderir**. Duplicate'in kaynağı budur.
7. UDP varyantında ayrıca bir kanal başlığı (length, packet ID; örnekte 0xCAFE) ve AVL packet ID bulunur. ACK bu ID'leri yansıtır.

- **Endianness:** Teltonika alanları big-endian'dır. `BinaryPrimitives.ReadInt32BigEndian`. Koordinatlar signed'dır (two's complement).
- **Queclink:** Çoğunlukla ASCII, virgülle ayrılmış `+RESP:GTFRI,...$` biçimi. Bazı modellerde HEX/binary modu var. Satır sonu çerçeveleme ve SACK yanıtları kullanılır.
- **MQTT:** Yeni nesil cihazlarda TLS + client cert. QoS 1 at-least-once'tır, dolayısıyla duplicate beklenir.
- **HTTP:** Mobil uygulamalar ve düşük frekanslı cihazlar için.

### 9.3 .NET'te Ölçeklenebilir TCP Gateway (Kestrel/Sockets/Pipelines)

**S: 100 bin kalıcı TCP bağlantısını .NET'te nasıl yönetirsiniz?**
Model cevap:
- Kestrel'in `ConnectionHandler`'ı ya da doğrudan `Socket` + **System.IO.Pipelines** kullanırım. Pipelines, buffer yönetimini (ArrayPool tabanlı), kısmi okumayı ve backpressure'ı (`PauseWriterThreshold`) hazır verir.
- Bağlantı başına thread açılmaz; her şey async I/O'dur.
- **Kısmi çerçeveleri doğru işlemek gerekir:** paket bölünmüş ya da birleşik gelebilir. Length prefix okunur. Yeterli byte yoksa `AdvanceTo(consumed, examined)` ile daha fazla veri beklenir.
- Idle timeout ve keep-alive ayarlanır. Bağlantı başına bellek sınırlanır. Bozuk CRC'de paket loglanıp ACK gönderilmez ya da bağlantı kapatılır.

```csharp
public sealed class TeltonikaHandler(ChannelWriter<AvlRecord> output) : ConnectionHandler
{
    public override async Task OnConnectedAsync(ConnectionContext c)
    {
        var input = c.Transport.Input;
        string? imei = null;
        while (true)
        {
            var result = await input.ReadAsync(c.ConnectionClosed);
            var buf = result.Buffer;
            while (TryReadFrame(ref buf, imei is null, out var frame))
            {
                if (imei is null) { imei = ParseImei(frame); await c.Transport.Output.WriteAsync(new byte[] { 1 }); continue; }
                if (!Crc16Ibm.Validate(frame)) break;                   // ACK yok -> cihaz yeniden gönderir
                var records = AvlDecoder.Decode(frame, imei);
                foreach (var r in records) await output.WriteAsync(r);  // bounded channel => backpressure
                await c.Transport.Output.WriteAsync(BigEndian(records.Count)); // ACK: kalıcılık politikası sonrası
            }
            input.AdvanceTo(buf.Start, buf.End);
            if (result.IsCompleted) break;
        }
    }
}
```

- **Kritik tasarım kararı:** ACK'i ne zaman göndermeli? En güvenli seçenek, kayıt dayanıklı bir yere (Kafka/Kinesis ack'i ya da yerel WAL) yazıldıktan sonra göndermektir. Daha önce gönderirseniz düşük latency kazanırsınız ama crash'te veri kaybedersiniz.
- **Ölçek:**
  - NLB arkasında stateless gateway'ler (TCP passthrough). Önemli bir not: komut gönderebilmek için "IMEI → hangi gateway pod'u" eşlemesini Redis'te tutun.
  - Bağlantı başına bellek bütçesi hesaplayın.
  - `ulimit`/file descriptor ve ephemeral port ayarlarını kontrol edin.
  - Rolling deploy'da bağlantıları drain edin; cihazlar yeniden bağlanır, bağlantı fırtınasına karşı jitter.

### 9.4 Sıralama, Duplicate, Geç Gelen Veri, Backpressure

- **Duplicate:** Doğal anahtar `(device_id, ts)` (gerekirse + event_id). `INSERT ... ON CONFLICT DO NOTHING` ya da staging + merge. Stream tarafında kısa pencereli dedup cache (Redis SET + TTL).
- **Sıralama:** Partition key = device_id olursa cihaz bazında sıra korunur (Kafka partition / Kinesis shard). Global sıra gerekmez ve pahalıdır.
- **Out-of-order ve geç gelen veri:** Cihaz kapsama dışında saatlerce buffer'layıp sonra toplu gönderebilir.
  - Event time ile processing time ayrılır.
  - Trip ve alarm hesaplarında watermark + izin verilen gecikme kullanılır.
  - Geç gelen veri "canlı alarm" üretmemeli; geçmiş raporu yeniden hesaplatmalıdır. TimescaleDB continuous aggregate refresh penceresi buna göre ayarlanır.
  - "Son konum" yalnızca `ts > last_ts` ise güncellenir.
- **Backpressure zinciri:** TCP flow control ← Pipelines eşikleri ← bounded channel ← stream producer'ı ← consumer lag metriği → autoscaling. Hangi noktada "drop" edildiği bilinçli seçilmelidir: canlı konum düşebilir, CAN ve olay kayıtları düşmemelidir.

### 9.5 Zaman Serisi Modelleme: PostgreSQL + TimescaleDB + PostGIS

```sql
CREATE TABLE telemetry (
  tenant_id int NOT NULL, device_id bigint NOT NULL, ts timestamptz NOT NULL,
  geom geography(Point,4326), speed_kmh smallint, heading smallint, ignition boolean,
  odometer_m bigint, io jsonb, PRIMARY KEY (device_id, ts));
SELECT create_hypertable('telemetry', by_range('ts', INTERVAL '1 day'));
ALTER TABLE telemetry SET (timescaledb.compress, timescaledb.compress_segmentby='device_id',
  timescaledb.compress_orderby='ts DESC');
SELECT add_compression_policy('telemetry', INTERVAL '7 days');
SELECT add_retention_policy('telemetry', INTERVAL '180 days');
CREATE MATERIALIZED VIEW telemetry_1h WITH (timescaledb.continuous) AS
  SELECT device_id, time_bucket('1 hour', ts) b, max(speed_kmh) vmax, avg(speed_kmh) vavg,
         max(odometer_m)-min(odometer_m) dist_m FROM telemetry GROUP BY 1,2;
SELECT add_continuous_aggregate_policy('telemetry_1h', INTERVAL '3 days', INTERVAL '1 hour', INTERVAL '30 minutes');
```

- **Chunk aralığı:** Aktif chunk'ların index'leri belleğe sığacak şekilde seçilir.
- **Retention:** Chunk'ları komple drop eder. Bu, `DELETE`'ten çok daha ucuzdur ve VACUUM maliyeti yoktur.
- **Compression:** Sıkıştırılmış chunk'lara geç veri eklemek ya da update yapmak daha maliyetlidir. Compression gecikmesini, geç gelen veri penceresinden uzun tutun.
- **Downsampling:** Ham veri 90–180 gün, saatlik aggregate 2 yıl, günlük aggregate süresiz. Hiyerarşik continuous aggregate'ler mümkün. Ham veri ayrıca Parquet olarak S3'e arşivlenir.
- **"Son konum" sorgusu:** Hypertable'dan değil, ayrı bir `device_last_state` tablosundan (upsert) ya da Redis'ten okunur.
- **Saf PostgreSQL alternatifi:** Native declarative partitioning + pg_partman (RDS'te destekleniyor) + BRIN index'leri.
- **Multi-tenancy:** `tenant_id` kolonu + RLS (`SET app.tenant_id`), ya da büyük müşteri için ayrı schema/DB.

### 9.6 Algoritmalar: Geofence, Trip, Filtreleme, Map Matching, Sürüş Skoru, Yakıt/Mesafe

- **Geofencing:**
  - Daire geofence için Haversine mesafesi ≤ r. Poligon için point-in-polygon (ray casting) ya da PostGIS `ST_Contains`/`ST_DWithin`.
  - Ölçek için ön filtreleme: R-tree ya da GiST index, H3/geohash hücre → geofence id eşlemesi bellekte tutulur.
  - **Durum makinesi:** Her (araç, geofence) çifti için önceki durum (inside/outside) saklanır. Yalnızca geçişte event üretilir.
  - **Histerezis / dwell time:** Sınırda GPS zıplaması yüzünden sahte giriş-çıkışlar olur. Örneğin 2 ardışık nokta ya da 30 sn şartı ve tampon mesafe konur.
- **Trip tespiti:**
  - Kontak (ignition IO) birincil sinyaldir. Yoksa hız > eşik ve hareket kullanılır.
  - Durak tespiti: hız < 3 km/h ve > N dakika.
  - Minimum trip mesafesi ve süresi ile gürültü trip'leri elenir. Geç gelen veri trip'i böldüyse yeniden hesaplanır.
  - Rölanti: kontak açık, hız 0 ve RPM > 0.
- **GPS gürültü filtreleme:**
  - HDOP ya da uydu sayısı eşiği.
  - Fiziksel olarak imkânsız sıçramaları eleme: iki nokta arasındaki hız > 250 km/h ise atılır.
  - Durağan drift: hız 0 iken konum sabitlenir.
  - Kalman filtresi ile yumuşatma. Rota sadeleştirme için Douglas–Peucker (çizim amaçlı).
- **Map matching:** Gürültülü noktaları yol ağına oturtma. Gizli Markov modeli (HMM, Newson & Krumm yaklaşımı) + Viterbi algoritması kullanılır. Araçlar: OSRM `match`, Valhalla Meili, GraphHopper, Mapbox Map Matching API. Hız limiti ihlali ve gerçek yol mesafesi için gereklidir.
- **Sürüş davranışı skoru:**
  - Harsh braking/acceleration: ivmeölçerden (cihaz eco-driving olayları) ya da ardışık GPS hız farkından (Δv/Δt; örneğin < −3,5 m/s² sert fren) hesaplanır. Eşikler araç sınıfına göre kalibre edilir.
  - Harsh cornering (yanal g), hız ihlali (map-matched limit), aşırı rölanti, gece sürüşü.
  - **Skor:** 100 km başına olay sayısıyla normalize edilmiş ağırlıklı ceza, filoya göre persentil. Normalize etmezseniz çok yol yapan sürücü haksız yere düşük skor alır.
- **Mesafe ve yakıt:**
  - En doğrusu CAN odometresi ya da cihazın total odometer IO'su (Teltonika AVL ID 16). Bunlar yoksa Haversine toplamı kullanılır (gürültü filtrelemeden sonra; aksi halde mesafe şişer).
  - Yakıt için CAN total fuel used farkı. Yakıt seviyesi sensöründen ani düşüş = yakıt hırsızlığı alarmı (eğim ve sloshing için yumuşatma gerekir).

### 9.7 Alarm / Rule Engine

- **Kural modeli:** Kural JSON/DSL olarak tenant bazında saklanır. Örnek: `speed > 90 for 60s in geofence X between 22:00–06:00`.
- **Değerlendirme:**
  - Stream consumer'ında, cihaz başına state ile (son değerler, zamanlayıcılar) yapılır.
  - Basit eşik kuralları stateless'tır; süre ve sıra kuralları stateful'dur.
  - Kurallar cache'lenir ve değişiklik event'iyle invalidate edilir.
- **Çıktı:** `AlarmRaised` event'i → bildirim servisi (push, SMS, e-posta, webhook). Dedup ve throttle uygulanır (aynı alarm 10 dk'da bir). Escalation ve ack akışı vardır.
- **Seçenekler:** Kendi stream işlemciniz (.NET worker + Kafka), Flink (Managed Service for Apache Flink) ya da AWS IoT Rules (yalnızca basit SQL filtreleri için). Kullanıcı tanımlı kural için expression motorları (NCalc, DynamicExpresso) sandbox içinde kullanılmalıdır.

### 9.8 Gerçek Zamanlı Güncelleme ve Canlı Harita

- **SignalR:** WebSocket, SSE ve long polling fallback'i. Gruplar `tenant:{id}` ya da `vehicle:{id}` biçimindedir. Ölçeklemek için Redis backplane gerekir (veya Azure SignalR Service). AWS'de ALB + sticky session (negotiate sonrası WebSocket) + ElastiCache Redis backplane.
- **SSE:** Tek yönlü akış, HTTP/2 dostu, basit. Canlı konum çoğu zaman tek yönlüdür, bu yüzden SSE yeterli olabilir.
- **Fan-out optimizasyonu:**
  - Kullanıcı yalnızca görüş alanındaki (viewport/bbox) araçlara abone olur.
  - Sunucu 1 sn'lik delta batch gönderir. Değişmeyen araçlar gönderilmez.
  - Payload MessagePack protocol ile küçültülür.
- **Mobilde:** Uygulama arka plana geçince bağlantı kapatılır, ön plana gelince REST ile snapshot alınır ve akışa yeniden bağlanılır.

### 9.9 Komut ve Kontrol, OTA Firmware, Cihaz Yönetimi

- **Komut yaşam döngüsü (State pattern):** Created → Queued → Sent → Delivered/Acked → Executed | TimedOut | Failed. Her geçiş kalıcı olarak yazılır (denetim izi).
- **Teltonika:** Codec 12 GPRS komutları (ör. `getinfo`, `setdigout`) aynı TCP oturumu üzerinden gönderilir ve cihaz yanıt verir. Cihaz offline ise komut kuyrukta bekler ve bağlandığında gönderilir.
- **Correlation id ve timeout:** Örneğin 60 sn. Idempotent olmayan komutlarda (motor durdurma gibi) retry politikasına dikkat edin. Kritik komutlarda çift onay (iki aşamalı kullanıcı onayı) ve hız < 5 km/h gibi güvenlik koşulu konur.
- **OTA firmware:**
  - Kademeli rollout (%1 → %10 → %100), cihaz grubu ve hücre.
  - İmzalı imaj, sürüm uyumluluk matrisi, rollback.
  - AWS IoT Jobs ya da Teltonika FOTA WEB.
- **Cihaz yönetimi:** Provisioning (IMEI/ICCID kaydı, tenant atama), konfigürasyon şablonları, heartbeat ve last-seen, SIM/APN yönetimi, envanter.

### 9.10 Multi-tenancy, Güvenlik, KVKK/GDPR

**Multi-tenancy:**
- Tenant id token claim'inden alınır. EF 10 named query filter + PostgreSQL RLS ile çift katman koruma sağlanır.
- Noisy neighbor için tenant bazlı rate limit ve kota.
- Bayi → müşteri → alt müşteri hiyerarşisi (ltree ya da closure table).

**Cihaz güvenliği:**
- Klasik 2G/TCP tracker'lar genellikle şifresiz binary gönderir ve kimlik sadece IMEI'dir. Bu kolayca taklit edilebilir.
- Önlemler:
  - Özel APN/VPN (operatör private APN).
  - Kaynak IP allowlist.
  - Destekleyen modellerde TLS.
  - Anomali tespiti: aynı IMEI'den iki eşzamanlı bağlantı, imkânsız konum sıçraması.
- MQTT cihazlarda X.509 client sertifikası ve cihaz başına policy.

**KVKK (Türkiye):**
- **Kurul kararlarının yaklaşımı:**
  - Kişisel Verileri Koruma Kurulu'nun araç kiralama kara liste yazılımıyla ilgili 2021/1303 sayılı kararına göre, konum takibinde konum verisinin ilgili kişilerin "yaşam alışkanlıklarını ortaya çıkarabileceği" akılda tutulmalıdır.
  - Kurum yayınındaki bir örnekte araç takip şirketi, sözleşmeye uygun hareket etse bile hangi verilerin toplanacağını kendisi seçtiği için **veri sorumlusu** sayılıyor. Bu, SaaS telematik sağlayıcılarının rolünü sözleşmede netleştirmeleri gerektiği anlamına gelir.
  - Kurum'un dergisinde yayımlanan bir akademik makaleye göre risk bulunmayan durumlarda ve mesai saatleri dışında konum işlemede meşru menfaat bulunmamaktadır. Bu bir Kurul kararı değil, akademik görüştür.
  - İşçi-işveren ilişkisindeki güç dengesizliği nedeniyle açık rıza zayıf bir hukuki sebep sayılıyor. 2026/921 sayılı ilke kararı (biyometrik mesai takibi, RG 2.6.2026) bu bakışı güçlendiriyor. Bu karar doğrudan konuma ilişkin değildir, ancak benzetme yoluyla uygulanabilir.
- **Tasarım çıkarımları:**
  - Mesai dışında takibi teknik olarak durduran bir "özel kullanım / gizlilik modu".
  - Amaçla sınırlı örnekleme.
  - Rol bazlı erişim.
  - Kısa saklama süreleri ve otomatik retention.
  - Aydınlatma metni.
  - Erişim logları.
- **Yurt dışına aktarım:** 7499 sayılı Kanun ile değişen KVKK md. 9, 1 Haziran 2024'te yürürlüğe girdi. Kademeli yapı: yeterlilik kararı → uygun güvenceler (standart sözleşme, BCR vb.) → arızi aktarım istisnaları.
  - Standart sözleşme imzadan itibaren 5 iş günü içinde Kurum'a bildirilmelidir. Bildirmemenin idari para cezası 50.000 TL ile 1.000.000 TL arasındadır.
  - Sonuç: Sunucular AWS Frankfurt'taysa bu düzenli bir aktarımdır. Hukuki dayanak açık rıza değil, standart sözleşme olmalıdır.

**GDPR (EDPB Guidelines 01/2020 on connected vehicles, v2.0):**
- Konum verisi özellikle "ifşa edici" bir veri kategorisi olarak ele alınıyor ve yalnızca kesinlikle gerekliyse toplanması öneriliyor.
- Öneriler:
  - Toplama sıklığı ve hassasiyeti amaca göre ayarlanmalı.
  - Konum varsayılan ve sürekli açık olmamalı.
  - Konum aktifken kullanıcı bilgilendirilmeli; konum kapatılabilmeli.
  - Saklama süresi sınırlı olmalı.
  - Mümkünse veri araç içinde işlenmeli, araç dışına ham veri yerine skor çıkarılmalı.
  - Araç dışında işlemede genellikle DPIA gerekir.
- Not: İşverenin çalışanı izlemesi bu rehberin kapsamı dışındadır; ulusal iş hukuku ve WP29 Opinion 2/2017 geçerlidir.

### 9.11 Gözlemlenebilirlik ve Olay Yönetimi (Telematik)

- **Metrikler:**
  - Aktif bağlantı sayısı (protokol bazında), paket/sn, CRC hata oranı, decode hatası.
  - Ingest lag: `now - event_ts` ve `now - received_ts` ayrı ayrı.
  - Consumer lag, DLQ boyutu.
  - Cihaz "sessizlik" oranı: son 1 saatte veri göndermeyen yüzde.
- **Alarmlar:** Bağlantı sayısında %20 ani düşüş genelde operatör ya da DNS sorunudur, kod sorunu değildir.
- **Runbook'lar:** Bağlantı fırtınası (yeniden bağlanma dalgası), hot partition, DB yazma gecikmesi.
- **Postmortem:** Blameless format. Zaman çizelgesi, etki (kaç araç, kaç dakika veri gecikmesi, veri kaybı var mı?), kök neden (5 Whys), aksiyonlar. Sahibi ve tarihi atanır.

### 9.12 İngilizce Model Cevaplar
- *"How do you guarantee no data loss from Teltonika devices?"* — "The device retransmits until it receives an ACK with the accepted record count. So I only ACK after the batch is durably written to the stream, and I make storage idempotent on device_id plus timestamp. That way retransmissions create duplicates, never gaps."
- *"How do you handle late data?"* — "I separate event time from processing time. Live alerts use a watermark with an allowed lateness; late records update history and trigger recomputation of trips and aggregates, but never fire stale real-time alerts."
- *"Geofence false alarms?"* — "Per-vehicle, per-fence state machines with hysteresis: a buffer distance plus a dwell time or consecutive-point requirement, after GPS-quality filtering on HDOP and satellite count."


## 10. AWS (Cloud) — Niçin ve Ne Zaman

### 10.1 Servis Seçimi: Senaryo Odaklı

- **AWS IoT Core:** MQTT/MQTT5/WebSocket destekli, yönetilen broker.
  - **Neden:** Milyonlarca cihazda sertifika tabanlı mTLS kimlik doğrulama, cihaz başına policy (`iot:Connect` için `${iot:ClientId}` kısıtı), rules engine ile SQL benzeri filtreleme ve Kinesis/Lambda/S3/DynamoDB'ye yönlendirme.
  - **Device Shadow:** Bağlantısız cihazlar için desired/reported durum. Konfigürasyon gibi seyrek değişen veriler içindir, telemetri için değil. Belge başına 8 KB sınırı ve shadow başına saniyede 20 istek limiti var.
  - **Bilmeniz gereken limitler:**
    - QoS 0 ve 1 desteklenir, QoS 2 yoktur. QoS 0 bile tekrar teslim edilebilir; idempotency şart.
    - Mesaj boyutu en fazla 128 KB.
    - Ücretlendirme 5 KB'lık birimler üzerinden yapılır.
    - Persistent session'lar varsayılan olarak 1 saat saklanır; yeniden bağlanınca mesajlar saniyede 10 mesaj hızında teslim edilir.
    - Bağlantı başına 50 abonelik sınırı var.
  - **Önemli:** IoT Core, Teltonika'nın ham TCP binary protokolünü konuşmaz. Bu cihazlar için kendi gateway'iniz gerekir (ECS/EKS üzerinde, NLB arkasında). IoT Core yalnızca MQTT konuşan cihazlar ve mobil için uygundur.
- **Kinesis Data Streams vs MSK (Kafka):**
  - **Kinesis:** Yönetimi minimumdur. Shard başına yazma 1 MB/s ya da 1.000 kayıt/s, okuma 2 MB/s. Yeni bir on-demand stream'in varsayılan kotası yazma için 4 MB/s ve 4.000 kayıt/s'dir ve varsayılan olarak yazmada 200 MB/s'e, 200.000 kayıt/s'e kadar otomatik ölçeklenir. Partition key = device_id ile cihaz sırası korunur. Tek bir anahtar shard limitini aşamaz (hot shard). Lambda ve Flink entegrasyonu kolaydır.
  - **Firehose:** S3'e (Parquet dönüşümüyle) ya da OpenSearch/Redshift'e teslimat. Ham arşiv için idealdir.
  - **MSK/Kafka:** Uzun retention, log compaction, Kafka Connect/Debezium ekosistemi, consumer group esnekliği. Ekipte Kafka bilgisi varsa ya da çoklu bulut/hibrit taşınabilirlik gerekiyorsa tercih edilir. MSK Serverless operasyonu azaltır.
- **SQS vs SNS vs EventBridge:**
  - **SQS:** İş kuyruğu, competing consumers, DLQ, visibility timeout. FIFO kuyrukta message group = device_id ile cihaz bazlı sıra korunur. Rapor üretimi, bildirim gönderimi için.
  - **SNS:** Fan-out pub/sub (SNS → birden çok SQS), mobil push ve SMS.
  - **EventBridge:** İçerik tabanlı yönlendirme, SaaS/AWS event'leri, şema kaydı, zamanlanmış görevler (Scheduler). Düşük–orta hacimli domain/integration event'leri için. Saniyede yüz binlerce telemetri için değil.
  - **Özet kural:** Telemetri akışı → Kinesis/MSK. İş kuyruğu → SQS. Fan-out → SNS. İş event'lerinin yönlendirilmesi → EventBridge.
- **Lambda:**
  - Düzensiz yük ve event glue için: S3 arşiv tetikleyici, rapor üretimi, IoT rule hedefi, Kinesis batch işleme.
  - .NET 10 hem managed runtime hem container image olarak destekleniyor.
  - **Cold start azaltma:** Native AOT (trimming uyumluluğu gerekir), SnapStart, provisioned concurrency, ARM64/Graviton, küçük paket. AWS'nin .NET 10 duyurusuna göre file-based Lambda uygulamaları varsayılan olarak Native AOT kullanıyor. Lambda Managed Instances ile fonksiyonlar EC2 üzerinde serverless operasyon modeliyle çalıştırılabiliyor.
  - **Uygun değil:** Kalıcı TCP bağlantısı tutan gateway (Lambda'da bağlantı tutulamaz), 15 dakikayı aşan işler.
- **ECS/Fargate vs EKS vs EC2:**
  - **ECS Fargate:** Varsayılan önerim. Sunucu yönetimi yoktur, .NET API'ler ve worker'lar için yeterlidir.
  - **EKS:** Ekipte K8s bilgisi var, çoklu bulut, ileri seviye scheduling ya da operatörler gerekiyorsa.
  - **EC2:** Self-hosted TimescaleDB, özel ağ tuning'i ya da maliyet için Graviton + Savings Plans.
  - TCP gateway için ECS (Fargate ya da EC2 launch type) + **NLB** (L4, statik IP/Elastic IP; cihaz firmware'inde IP sabitlenmişse kritik).
- **API Gateway:** REST/HTTP API. Auth (Cognito/JWT authorizer), throttling, usage plan'lar (B2B API kotası). WebSocket API de mümkündür, ama SignalR kullanıyorsanız ALB daha basittir. Yüksek trafikli iç API'lerde ALB daha ucuzdur.
- **RDS/Aurora PostgreSQL:** İlişkisel çekirdek veriler (tenant, araç, kullanıcı, kural). Aurora: hızlı failover, okuma replikaları, Serverless v2. PostGIS desteklenir, **TimescaleDB desteklenmez** (extension allow-list'te yok).
- **Zaman serisi seçenekleri:**
  - (a) Tiger Cloud / Timescale Cloud (AWS üzerinde, VPC peering/PrivateLink).
  - (b) EC2 üzerinde self-hosted TimescaleDB. Yedekleme ve HA sizin sorumluluğunuzdadır.
  - (c) RDS/Aurora + native partitioning + pg_partman.
  - (d) Timestream for InfluxDB.
  - Timestream for LiveAnalytics 20 Haziran 2025'ten beri yeni müşterilere kapalıdır, yeni tasarımda önermeyin.
  - Benim varsayılanım: PostGIS + SQL ekosistemi istendiği için (a) ya da (c).
- **DynamoDB:** Anahtar-değer erişimi. Cihaz "son durum"u, bağlantı/oturum eşlemesi (IMEI → gateway), idempotency anahtarları (TTL ile). Tek basamaklı ms gecikme ve yönetim yükü yok. Ad-hoc sorgu ve coğrafi sorgu için uygun değildir.
- **S3:** Ham telemetri arşivi (Firehose → Parquet, `tenant/date` partition), data lake (Glue Catalog + Athena ile ad-hoc analiz), firmware dosyaları, rapor çıktıları. Lifecycle ile Standard → IA → Glacier. Object Lock ile değiştirilemez denetim kaydı.
- **ElastiCache (Redis/Valkey):** Son konum cache'i, SignalR backplane, rate limit, dağıtık kilit, geofence H3 lookup, dedup SET'leri.
- **Cognito:** Kullanıcı havuzu, OAuth/OIDC, mobil PKCE, grup/claim'lerde tenant id. Kurumsal müşteriler için SAML federasyonu.
- **Secrets Manager vs Parameter Store:** Secrets Manager otomatik rotasyon (RDS şifreleri) ve cross-account erişim sağlar, ücretlidir. Parameter Store konfigürasyon ve basit secret'lar (SecureString) için ucuz bir seçenektir. .NET'te `AWS.Extensions.Configuration` / `Amazon.Extensions.Configuration.SystemsManager` ile `IConfiguration`'a bağlanır.
- **CloudWatch / X-Ray:** Log, metrik, alarm, dashboard. Embedded Metric Format. X-Ray ya da ADOT (AWS Distro for OpenTelemetry) ile tracing. Benim tercihim OTel SDK + ADOT Collector; vendor bağımlılığı azalır.
- **IAM en iyi uygulamaları:**
  - Least privilege, servis başına rol (ECS task role).
  - Uzun ömürlü access key yok. CI için GitHub OIDC ile role assume.
  - SCP'ler ve permission boundary'ler.
  - IAM Access Analyzer.
  - Çok hesaplı yapı (Organizations + Control Tower: dev/stage/prod ayrı hesap).
- **VPC temelleri:**
  - 3 AZ'de public subnet'ler (NLB/ALB, NAT) ve private subnet'ler (ECS, RDS).
  - Security group'lar (stateful) ve NACL'ler (stateless).
  - S3/DynamoDB için VPC endpoint'leri: NAT maliyetini düşürür.
  - PrivateLink. Cihazlar için operatör private APN → Site-to-Site VPN / Direct Connect.
- **CloudFront:** Web (Next.js) statik varlıkları, harita tile'ları cache'i, WAF ve Shield entegrasyonu.
- **CI/CD:**
  - GitHub Actions (OIDC) → build/test → ECR'ye image push → ECS rolling ya da blue/green (CodeDeploy), ya da Lambda alias + kademeli trafik kaydırma (`Linear10PercentEvery1Minute`).
  - CodePipeline alternatifi.
  - DB migration ayrı bir pipeline adımıdır (idempotent script).
- **IaC:**
  - **CDK:** C#/TypeScript, yüksek seviyeli construct'lar. Uygulama ekibiyle aynı dili konuşur.
  - **Terraform:** Çoklu bulut, geniş ekosistem, plan/apply. State S3'te, kilit DynamoDB'de.
  - **CloudFormation:** Native, drift tespiti; CDK de buna derlenir.
  - Seçim ekibin bilgisine ve çoklu bulut ihtiyacına göre yapılır. Önemli olan hiçbir prod kaynağın elle yaratılmamasıdır.

### 10.2 Maliyet Optimizasyonu
- NAT Gateway veri işleme ücreti gizli bir kalemdir. VPC endpoint kullanın.
- IoT Core 5 KB'lık birimlerle ücretlendirir. Mesajları birleştirin (batch), ama 128 KB sınırını aşmayın.
- Kinesis'te provisioned ile on-demand arasında karşılaştırma yapın (sabit yükte provisioned daha ucuzdur).
- Tiger Data'nın Hypercore dokümantasyonuna göre TimescaleDB'nin columnstore sıkıştırması depolamayı tipik olarak %90–98 azaltır. Soğuk veri S3/Parquet'e taşınır.
- Graviton (ARM64), Fargate Spot (worker'lar için), Savings Plans.
- CloudWatch log retention ayarlayın. Debug logları prod'da kapalı tutun.
- Etiketleme (tenant/servis) ve Cost Explorer ile birim maliyet: "araç başına aylık $".

### 10.3 Yüksek Erişilebilirlik ve Ölçeklenebilirlik
- Her katman en az 2–3 AZ'de çalışır. Aurora Multi-AZ, ElastiCache replikası.
- Gateway'ler stateless çalışır. Bağlantı yeniden kurulumu cihazın sorumluluğundadır. NLB cross-zone açılır.
- Stream kalıcılığı sayesinde consumer çökse bile veri kaybolmaz (retention 24 saat – 7 gün+).
- Autoscaling: CPU yerine bağlantı sayısı ve consumer lag metrikleriyle yapılır.
- **DR:** RPO/RTO tanımlanır. Aurora Global Database ya da cross-region snapshot. S3 CRR.

### 10.4 "Bu Sistemi AWS'te Nasıl Kurardın?" — Model Cevap
**Soru:** "Teltonika ve MQTT cihazları olan, çok kiracılı bir filo takip platformunu AWS'te kur."
1. **Giriş katmanı:**
   - Teltonika TCP → NLB (Elastic IP) → ECS üzerinde .NET Pipelines gateway (3 AZ).
   - MQTT cihazlar → IoT Core (X.509) → Rule → Kinesis.
   - Mobil → API Gateway/ALB → ECS API.
2. **Omurga:** Kinesis Data Streams (on-demand, partition key = IMEI). Gateway, ACK'i Kinesis `PutRecords` başarılı olduktan sonra gönderir.
3. **İşleme:** ECS worker'ları (KCL ya da kendi consumer'ınız): normalize → dedup → TimescaleDB batch `COPY`. Alarm/geofence worker'ı cihaz state'ini Redis'te tutar. Trip worker'ı.
4. **Depolama:** Aurora PostgreSQL (çekirdek veri + PostGIS), Tiger Cloud ya da EC2 üzerinde TimescaleDB (telemetri), DynamoDB/Redis (son durum), Firehose → S3 Parquet (arşiv) + Athena.
5. **Gerçek zamanlı:** ALB üzerinde SignalR (ElastiCache backplane). Push bildirim için SNS/FCM.
6. **Güvenlik:** Cognito (tenant claim), WAF, private subnet'ler, Secrets Manager, KMS ile şifreleme, cihazlar için private APN + VPN.
7. **Operasyon:** OTel → ADOT → CloudWatch/X-Ray. GitHub Actions OIDC → ECR → ECS blue/green. CDK ile IaC. Hesaplar arası ayrım.
8. **Trade-off'lar:** Kafka yerine Kinesis seçtim çünkü operasyon yükü düşük. Retention ya da Kafka ekosistemi gerekirse MSK'ye geçerim. Timestream LiveAnalytics yeni müşteriye kapalı olduğu ve PostGIS+SQL istediğim için TimescaleDB seçtim.

### 10.5 İngilizce Model Cevaplar
- *"SQS, SNS or EventBridge?"* — "SQS for work queues with retries and a DLQ, SNS for fan-out and mobile push, EventBridge for content-based routing of business events at moderate volume. High-rate telemetry goes to Kinesis or Kafka, not EventBridge."
- *"Why not Lambda for the device gateway?"* — "Teltonika devices keep long-lived TCP sessions, and Lambda can't hold connections. I run the gateway on ECS behind an NLB and use Lambda for event-driven glue like archive processing and report generation."
- *"Where would you store time-series data on AWS?"* — "TimescaleDB on Tiger Cloud or self-managed, because RDS doesn't support the extension. Or plain Aurora PostgreSQL with pg_partman if we want fully managed AWS. I'd avoid Timestream for LiveAnalytics since it's closed to new customers."


## 11. Sistem Tasarımı ve Davranışsal Sorular

### 11.1 Tasarım: "100 bin aracın canlı konumunu işleyen platform"
**Tahmin (back-of-envelope):**
- Hareket halindeyken 10 sn'de bir nokta, araçların ~%50'si aktif → ~5.000 nokta/sn ortalama. Tepe yük ×3 → ~15.000/sn.
- Kayıt başına ~200 B → ~3 MB/s. Kinesis'te kayıt sayısı limiti nedeniyle en az ~15 shard ya da on-demand.
- Günde ~430 milyon satır. Ham veri ~85 GB/gün; Tiger Data'nın belgelediği %90–98 Hypercore sıkıştırma oranı bu veride de tutarsa günde yaklaşık 1,7–8,5 GB (bu, Tiger Data rakamlarından hesaplanmış bir değerdir, telematik verisinde ölçülmüş değildir).

**Bileşenler:** §10.4'teki mimari.

**Kritik kararlar:**
- Cihaz bazında partition'lama.
- Idempotent `COPY` batch'leri.
- Son konum için Redis/DynamoDB.
- Görünür alan (viewport) tabanlı fan-out.
- Retention ve continuous aggregate'ler.
- ACK'in dayanıklı yazımdan sonra gönderilmesi.

**Darboğaz tartışması:**
- DB yazma: batch + compression + gerekirse tenant bazında sharding.
- Hot shard: kötü partition key seçimi.
- Bağlantı fırtınası: deploy sırasında yeniden bağlanma. Jitter ve kademeli drain.
- WebSocket fan-out: backplane + delta gönderimi.

**Takip soruları:** "Bir AZ düşerse ne olur?" "Bir müşteri 20 bin araç eklerse?" "Veri 6 saat gecikmeli gelirse raporlar ne olur?"

### 11.2 Tasarım: "Geofence alarm sistemi"
- **Gereksinimler:** Tenant başına 10 bin geofence (poligon ya da daire), giriş/çıkış/dwell alarmları, p95 < 5 sn, sahte alarm oranı düşük olmalı.
- **Tasarım:**
  - Geofence CRUD → Aurora/PostGIS (doğrulanmış poligonlar, `ST_IsValid`).
  - Değişiklik event'i → worker'lar bellekteki spatial index'i (H3 hücresi → aday geofence'ler ya da STRtree; NetTopologySuite) yeniden yükler.
  - Stream consumer cihaz bazlı partition'la çalışır: nokta → kalite filtresi → aday geofence'ler → kesin point-in-polygon → (cihaz, geofence) durum makinesi (Redis hash) → histerezis/dwell kontrolü → `GeofenceEvent` → outbox → bildirim servisi (dedup, sessiz saatler, kanal tercihleri).
- **Geç gelen veri:** Watermark'ı geçen noktalar canlı alarm üretmez, yalnızca geçmiş olay olarak kaydedilir.
- **Ölçek:** Partition başına bir worker. Durum Redis'te tutulduğu için rebalance güvenlidir.
- **Test:** Kayıtlı gerçek GPS izleriyle replay testleri. Sınır vakaları: sınırda zıplama, tünel, uzun kesinti.

### 11.3 Diğer Tasarım Soruları (Kısa Çerçeveler)
- **Cihaza komut gönderme servisi:**
  - IMEI → gateway eşlemesi tutulur (Redis, TTL'li heartbeat).
  - Komut tablosu + state machine.
  - Gateway'e yönlendirme: Redis pub/sub ya da gateway başına SQS kuyruğu.
  - Timeout ve ack takibi. Offline kuyruk.
  - Denetim kaydı.
- **Sürüş skoru raporu:** Batch (gece) ile streaming arasındaki trade-off. Olayların 100 km'ye normalize edilmesi. Rapor PDF'i S3'e yazılır, imzalı URL ile paylaşılır.
- **Offline mobil sürücü uygulaması:** Yerel SQLite outbox, idempotency anahtarları, çakışma kuralları, pil bütçesi.

### 11.4 Senior/Lead Sorumlulukları
- **Code review:** Öncelik sırası: doğruluk, güvenlik, anlaşılırlık, performans. Stil konuları linter/formatter'a bırakılır. Küçük PR kültürü. "Neden"i soran yorumlar. Review SLA'sı (ör. 24 saat).
- **Mentorluk:** Pair programming, tasarım dokümanı (RFC/ADR) yazdırma, sorumluluğu kademeli devretme. Ölçüt: kişinin bağımsız teslim edebildiği işin büyüklüğü.
- **Teknik borç:**
  - Borç kaydı (etki × olasılık).
  - Her sprintte sabit kapasite (%15–20) ya da "kamp alanı kuralı".
  - İş tarafına riskin diliyle anlatılır. Örnek: ".NET 8 desteği 10 Kasım 2026'da bitiyor; güvenlik yamaları duruyor."
- **Olay sonrası analiz:** Blameless format. Tespit süresi, müdahale süresi, etki. Aksiyonların takibi.
- **ADR'ler:** Kararın bağlamı, seçenekler, sonuç ve sonuçları yazılır. Mülakatta "bir ADR örneğiniz var mı?" sorusuna hazırlıklı olun.

### 11.5 Davranışsal sorular, açılış cümlesi ve STAR hikâyeleri

#### ⭐ "Kendini tanıt" (ilk 60 saniye)

Notundaki açılış cümlesi güçlü; bu rol için iki küçük değişiklik önerdim: (1) "Team Lead" yerine **senior full-stack** vurgusu (ilan "Full Stack Software Developer"; unvan kafa karıştırmasın), (2) telematik/IoT ve mobil/web'i öne al. İstersen eski haline döndür.

> *"10 yılı aşkın .NET deneyimi olan, yüksek ölçekli, event-driven ve IoT/telematik sistemlerine odaklanmış bir senior full-stack mühendisiyim. En değerli tecrübelerim Yemeksepeti'nde quick-commerce mikroservislerini kurmak, Delivery Hero'da global entegrasyon altyapısı tasarlamak ve şu an Drivee'de günde 50 milyon telemetri verisini işleyen bir IoT ekosistemini yönetmek oldu. Backend'de .NET ve Kafka, ön yüzde React ve React Native ile uçtan uca çalışıyorum. Unvanım Team Lead olsa da günümün çoğunu kod yazarak ve mimari karar alarak geçiriyorum; kendimi 'inşaatçı' olarak görüyorum."*

English: *"I'm a senior full-stack engineer with 10+ years of .NET, focused on high-scale, event-driven and IoT systems. I built quick-commerce microservices at Yemeksepeti, designed a global integration platform at Delivery Hero, and now run an IoT platform at Drivee that processes around 50 million telemetry records a day. I work end to end with .NET and Kafka on the backend and React and React Native on the front. My title is team lead, but I spend most of my day writing code and making architectural decisions."*

**Dört koz (bu sırayla tut):** (1) monolit → mikroservis dönüşüm liderliği, (2) "you build it, you run it" (Docker, Traefik, GoCD, kendi altyapın), (3) cross-platform vizyon (.NET + React + React Native), (4) çevik iletişim ve cross-functional ekip yönetimi.

#### ⭐ Hikâye 1: Monolitten mikroservise

- **S:** Hızlı çıkmak için Web Forms ve monolit doğru karardı; şirket büyüyünce yeni özellik haftalar almaya başladı.
- **T:** Backend'i mikroservislere bölmek + React web + React Native + cross-functional ekip + DevOps, paralel olarak.
- **A:** CI/CD ve container stratejisini kurdum; **DDD ile servis sınırlarını** belirledim; **API Gateway + BFF** ile mobil/web ekiplerini bağımsızlaştırdım.
- **R:** Release döngüleri haftadan günlere indi, ekipler birbirini beklemeden deploy etti, mobil ürün canlıya çıktı. *(Kendi gerçek rakamınla somutlaştır: "haftada 1 → günde N deploy".)*

#### ⭐ Hikâye 2: "En kötü mimari kararın?" (stres sorusu: Kaza Bildirim)

> *"En büyük hatamı dönüşümün başında yaptım: kaza bildirim modülünü legacy Web Forms monolitinde bıraktım. SignalR ile real-time çalışan kritik bir sistemdi. Müşteriler büyük rapor çektiğinde IIS thread'leri bloklanıyor (thread starvation) ve tüm sistem yanıt veremez hale geliyordu; sahadan gelen kaza bildirimleri de bu kilitlenmeye takılıyordu. Mimariyi acilen event-driven, loosely coupled yapıya çevirdim: legacy sistem yalnızca Kafka'ya mesaj bırakan bir publisher oldu (fire-and-forget), yepyeni izole bir SignalR mikroservisi Kafka'yı tüketti. Çıkardığım ders: mikroservis sınırlarını sadece domain'e göre değil, modülün business criticality seviyesine göre çizerim."*

Altın detaylar: "benim kararımdı" (sahiplenme), "kaza bildirimi gecikiyordu" (insan/iş etkisi), thread starvation, outbox/idempotency ile **bugün daha güvenli yapardım** (öğrenim: yalnızca fire-and-forget değil, **outbox + idempotent consumer**, §5.6). Bu "bugün olsa farklı ne yapardım" cümlesi hikâyeyi senior yapar.

#### ⭐ Hikâye 3: Ekip direnci ve teknoloji seçimi

> *"En büyük risk teknik değil kültüreldi. Web Forms'tan modern teknolojiye geçişi yukarıdan dikte etmek direniş yaratırdı. Süreci keşif yolculuğuna çevirdim: Angular vs React, Swift/Kotlin vs MAUI vs React Native için ortak POC'ler yaptık. Ekip kendi araştırmasıyla React + React Native'de karar kıldı. Unified tech stack öğrenme eğrisini kısalttı ve cross-functional geliştiriciler yarattı. Teknoloji ekibin seçimi olduğu için direnç yaşamadık."*

#### ⭐ Çatışma yönetimi

> *"Ekip güvenli olduğu için eski monoliti korumak istedi; ben yeni veri hacminin onu çökerteceğini biliyordum. Tartışmadım: Kafka ile küçük bir prototip yapıp farkı gerçek metriklerle gösterdim. Veriyi görünce mikroservis mimarisine katıldılar."*

#### Test kültürü sorusunda dürüst ve bilgili konumlan

> *"Test konusunda kendimi 'yeterli ve gelişen' olarak konumlandırırım. Test piramidiyle yazıyorum: çok unit, sınırlı integration (Testcontainers), minimum E2E. Drivee'de SonarQube ve kod inceleme araçlarıyla kalite kapıları kurduk, coverage'ı PR onayına bağladık. Coverage'ı hedef değil gösterge olarak görürüm."* (Derinlik için §6.1.)

#### Bu rol için eklenecek hikâye şablonu (telematik): reconnect storm

*(Rakamlar temsilîdir; kendi gerçek olayınla değiştir.)* **S:** Gateway deploy'u sonrası 30 bin cihaz aynı anda yeniden bağlandı, DB yazma kuyruğu taştı. **T:** Veri kaybı olmadan toparlamak. **A:** Ingest'i bounded channel + ACK erteleme ile yavaşlattım; cihazlar retransmit ettiği için veri kaybolmadı; sonra rolling drain + jitter + connection-rate alarmı ekledim. **R:** Veri kaybı sıfır, gecikme 40 dakikada normale döndü, sonraki deploy'larda tekrarlanmadı.

**Diğer sorular (hazır ol):** "Teknik borcu iş tarafına nasıl sattın?" (risk, maliyet, EOL tarihi: .NET 8 desteği 10 Kasım 2026'da bitiyor), "Junior'ı nasıl büyüttün?", "Bir hatanı anlat", "Belirsiz gereksinimle teslim" (prototip + hızlı geri bildirim), "Neden bu rol, neden biz?" (telematik + .NET + RN birleşimi).

**İpuçları:** Her hikâyede **1 rakam, 1 trade-off, 1 "bugün olsa farklı ne yapardım"**; "biz" ve "ben"i ayır; hikâye başına 2 dakika; bilmediğini söylemek güçtür: *"Bunu Polly ile çözerdim ama spesifik retry policy'yi canlıda uygulamadım, dokümana bakarım"* uydurmadan değerlidir. **Mülakat iki yönlüdür:** sen de şirketi, ekibi ve kültürü değerlendiriyorsun.

### 11.6 Live coding stratejisi

**Beklenebilecek "kır ve yeniden yap" senaryoları:**
1. **Memory allocation:** `string.Split` çok allocation yapar; "5 GB'lık string gelseydi?" → tamamını belleğe alma; `ReadOnlySpan<char>`, `StringReader`, **stream** ve `System.IO.Pipelines` ile parça parça işle (§3.2, §9.3).
2. **Eşzamanlılık:** "Bu bakiye hesabını saniyede 10.000 işleme çevir" → `Dictionary` yerine `ConcurrentDictionary`; ama `AddOrUpdate`/`GetOrAdd` delegesi **çakışmada yeniden çalışabilir** (yan etkisiz olmalı); `decimal` bakiyede bileşik güncelleme için anahtar başına kilit ya da minor unit `long` + `Interlocked`; çok instance'ta DB'de atomik update (§5.3). Soru: "ya aynı anda iki istek gelirse?"
3. **SOLID/genişletilebilirlik:** "Crypto ödeme tipi eklendi, ücret dinamik" → hard-coded `static` yapıyı kır; `ICurrencyProcessor` + DI (OCP), Strategy + registry (Q46).
4. **Async yap / DI ekle / testle:** `async` zinciri, `CancellationToken`, test edilebilirlik için `TimeProvider`/repo soyutlaması.

**Altın kural:** Asla tamamen sessiz kalma; trade-off'ları sesli söyle: *"Şimdilik `List` kullanıyorum çünkü sıra önemli; darboğaz görürsek `HashSet`'e çeviririm."* **Edge-case refleksi:** onlar sormadan söyle: null, boş girdi, hatalı format, race condition, çok büyük girdi.

**Tıkanma anı protokolü (5 adım):** (1) Sessizliği boz: *"Bir darboğaz fark ettim, mantığı oturtmak için sesli düşüneceğim."* (2) Problemi parçala: neyi bildiğini, neyin eksik olduğunu söyle. (3) **Önce brute force yaz:** O(n²)'yi yaz, çalıştır, sonra optimize et ("çalışan kötü kod, çalışmayan mükemmel fikirden iyidir"). (4) Bilmediğini soyutla: `bool IsValidIban()` diye geç, iş mantığına odaklan. (5) İpucu iste: mülakatçı düşman değil, pair programmer'dır; *"A mı B mi? Sizce hangisi?"* Küçük görev kütüphanesi: §2.9 (LRU, token bucket), §5.1 (mutabakat).

### 11.7 Mülakatçıya Sorulacak Akıllı Sorular
- Hangi cihaz markaları ve protokolleri destekleniyor? Gateway'ler şirket içinde mi yazıldı?
- Günlük mesaj hacmi, aktif cihaz sayısı ve en büyük tenant ne kadar?
- Zaman serisi verisi nerede tutuluyor? Retention ve raporlama yeniden hesaplama stratejisi ne?
- .NET 8 → 10 upgrade planınız var mı (EOL 10 Kasım 2026)?
- Mobil tarafta Expo mu bare RN mi kullanılıyor? New Architecture'a geçiş tamamlandı mı?
- On-call rotasyonu, olay yönetimi ve postmortem kültürü nasıl?
- KVKK açısından çalışan konumu ve mesai dışı takip nasıl ele alınıyor?
- Bu rolde ilk 90 günde başarı nasıl ölçülecek?

### 11.8 İngilizce Model Cevaplar
- *"Tell me about a time you improved system reliability."* — "Situation: reconnect storms after deploys overloaded our ingestion. Task: zero data loss and faster recovery. Action: I introduced bounded channels with delayed ACKs, connection draining with jitter, and a lag-based alert. Result: no data loss, recovery in about 40 minutes, and no repeat in later deploys."
- *"How do you handle technical debt?"* — "I keep a visible debt register ranked by risk, reserve a fixed share of each sprint, and tie items to business dates. For example, the .NET 8 end-of-support date makes the upgrade a security issue, not a nice-to-have."


## 12. Eksik Konular: Senior Full Stack İçin Ek Alanlar ⭐

Bu bölüm, senior full-stack mülakatlarında sık sorulan ama §1–§11'de ayrı bir başlık almamış alanları toplar. Önce haritaya bak; sonra boşluk olan konulara in. Vaktin kısıtlıysa sırayla: **Q100** (Docker/Kubernetes), **Q102** (AI destekli geliştirme), **Q91–Q92** (güvenlik), **Q103** (yük testi), **Q96** ("URL yazınca ne olur").

| Alan | Durum | Nerede |
|---|---|---|
| OWASP API riskleri, OAuth2/OIDC, JWT, yetkilendirme modelleri | ➕ yeni | §12.1 (web güvenliği ve BFF için Q90, §7.5) |
| REST tasarımı, idempotency, sayfalama | ✅ var | Q39, Q64 |
| gRPC / GraphQL / REST seçimi | ➕ yeni | §12.2 |
| İndeks, izolasyon, EXPLAIN, sharding | ✅ var | §5 |
| MVCC/VACUUM, bağlantı havuzu, "PostgreSQL yeter mi?" | ➕ yeni | §12.3 |
| HTTP/TLS/DNS, tarayıcı render hattı, HTTP cache ve CDN | ➕ yeni | §12.4 |
| React, Next.js, test, erişilebilirlik | ✅ var | §7 |
| TypeScript dil bilgisi, i18n, saat dilimi, para | ➕ yeni | §12.5 |
| Docker, Kubernetes, 12-factor, graceful shutdown | ➕ yeni | §12.6 |
| Branch stratejisi, feature flag, AI destekli geliştirme | ➕ yeni | §12.7 |
| Yük testi ve kapasite planı | ➕ yeni | §12.8 |
| Takograf, OEM API'leri, Data Act, ağ geçişleri gibi telematik alan bilgisi | ➕ yeni | §12.9 |
| Ek live-coding görevleri (aralık birleştirme, top-K, kayan pencere) | ➕ yeni | §12.10 |
| Azure/GCP, ileri Kubernetes, native mobil, ML | ⚠️ kapsam dışı | §12.11 (ne diyeceğin dahil) |

### 12.1 Güvenlik: API, kimlik doğrulama ve yetkilendirme

#### ⭐ Q91. OWASP API Security Top 10: en önemli riskleri .NET API'de nasıl kapatırsın?

**Model cevap:** API riskleri web arayüzü risklerinden farklıdır; çünkü istemci tarayıcı değil, doğrudan HTTP'dir ve her uç noktayı tek tek yoklayabilir. En çok zarar verenler **yetkilendirme** hatalarıdır (2023 listesi; sürümü owasp.org'dan kontrol et):

- **BOLA / IDOR (API1):** `GET /vehicles/{id}` isteğinde kimliğin geçerli olması, o araca erişme hakkın olduğu anlamına gelmez. Sahiplik/tenant koşulu **sorgunun içinde** olmalıdır; GUID kullanmak (tahmin edilemezlik) yetki değildir. Varlığı sızdırmamak için yetkisiz nesnede `403` yerine çoğu zaman `404` dönülür.
- **BOPLA / mass assignment (API3):** İstek gövdesini doğrudan entity'ye bağlama (`IsAdmin`, `TenantId` gibi alanlar dışarıdan set edilir). Giriş ve çıkış için **allowlist DTO** kullan; entity'yi dışarı serileştirme (aşırı veri sızıntısı).
- **BFLA (API5):** Yönetici işlevlerine (araç silme, komut gönderme) rol/policy ile fonksiyon düzeyinde yetki; "UI'da buton gizli" yetki değildir.
- **Kaynak tüketimi (API4):** Sayfa boyutu üst sınırı, gövde boyutu limiti, rate limiting (`AddRateLimiter`), istek zaman aşımı, pahalı sorgular için iptal (`CancellationToken`).
- **SSRF (API7):** Kullanıcının verdiği URL'ye sunucudan istek atan özellikler (webhook, görsel çekme) için izin listesi, iç IP aralıklarını ve bulut metadata adresini engelle; AWS'te **IMDSv2** zorunlu tut.
- **Güvenlik yanlış yapılandırması (API8) ve envanter (API9):** Eski API sürümleri, açık Swagger, ayrıntılı hata mesajları, geniş CORS. Kullanılmayan sürümleri kapat; hata cevabında `ProblemDetails` kullan ama stack trace verme.
- **Hassas iş akışlarının kötüye kullanımı (API6):** Örn. komut gönderme veya toplu dışa aktarma uç noktasına hız ve miktar sınırı, ek doğrulama.

```csharp
app.MapGet("/vehicles/{id:guid}", async (Guid id, ICurrentTenant tenant, AppDb db, CancellationToken ct) =>
{
    var v = await db.Vehicles.AsNoTracking()
        .Where(x => x.Id == id && x.TenantId == tenant.Id)      // BOLA: sahiplik koşulu sorgunun içinde
        .Select(x => new VehicleDto(x.Id, x.Plate, x.Name))      // BOPLA: yalnızca izin verilen alanlar
        .SingleOrDefaultAsync(ct);
    return v is null ? Results.NotFound() : Results.Ok(v);       // yetkisiz ve yok aynı cevap
}).RequireAuthorization();
```

**Savunma katmanı:** EF global query filter (tenant) + PostgreSQL RLS (§5, §9) + her yeni uç nokta için yetki testi ("başka tenant'ın kaynağı 404 dönüyor mu?" otomatik test).
**Takip soruları:** "Tenant filtresini unutan bir geliştiriciyi nasıl yakalarsın?" Global filter + RLS + entegrasyon testlerinde "iki tenant ile çapraz erişim" senaryosu. "Rate limit'i nerede uygularsın?" Kenarda (WAF/API Gateway) kaba, uygulamada kullanıcı/tenant bazlı ince.
**Sık hata:** Yetkiyi yalnızca controller'da kontrol edip arka plan işinde, SignalR hub'ında ya da dışa aktarma uç noktasında unutmak. Bir de "kimlik doğrulandı = yetkilendirildi" karışıklığı.

#### ⭐ Q92. OAuth2/OIDC akışları, JWT doğrulama ve yetkilendirme modelleri

**Model cevap:**

- **Hangi akış:** Tarayıcı, mobil ve sunucu tarafı uygulamalar için **Authorization Code + PKCE**. Servisten servise (cihaz yönetimi → faturalama) **Client Credentials**. Cihaz kodu akışı ekran/klavyesi olmayan cihazlar için. **Implicit** ve **Resource Owner Password** akışları kullanılmaz (OAuth 2.1 taslağı ikisini de dışarıda bırakır, PKCE'yi zorunlu kılar).
- **OAuth ≠ kimlik doğrulama:** OAuth yetkilendirme (access token); kullanıcının kim olduğunu **OIDC** (ID token, `sub`, `userinfo`) söyler. Access token'ı istemci içinde ayrıştırıp kimlik kararı verme; ID token istemci içindir, API'ye gönderilmez.
- **JWT doğrulaması:** İmza (JWKS'ten `kid` ile anahtar, anahtar rotasyonuna hazır), `iss`, `aud`, `exp`/`nbf` (küçük clock skew), izin verilen algoritma listesi (`none` ve algoritma karıştırma saldırısı). JWT **imzalıdır, şifreli değildir**: payload'a hassas veri koyma.
- **İptal sorunu:** JWT süresi dolana kadar geçerlidir. Kısa ömürlü access token (5–15 dk) + **refresh token rotasyonu** (kullanılan refresh token geçersiz olur; eskisi tekrar gelirse oturum iptal) + kritik işlemde introspection ya da deny-list.
- **Saklama:** Web'de BFF ile `HttpOnly; Secure; SameSite` çerez (§7.5, Q90). Mobilde Keychain/Keystore (`expo-secure-store`). `localStorage`'a token koyma.
- **Yetkilendirme modeli:** **RBAC** (roller; basit), **claims/policy tabanlı** (ASP.NET Core `AddAuthorizationBuilder().AddPolicy(...)`), **resource-based** (`IAuthorizationService.AuthorizeAsync(user, resource, policy)`: "bu kullanıcı *bu* araca erişebilir mi?"), **ABAC** (öznitelik: tenant, saat, konum), **ReBAC** (ilişki grafiği; bayi → müşteri → alt müşteri → araç hiyerarşisinde doğal; OpenFGA/SpiceDB gibi araçlar var). Scope'lar *istemcinin* ne yapabileceğini, rol/policy *kullanıcının* ne yapabileceğini söyler; ikisini karıştırma.
- **Multi-tenant:** `tenant_id` claim'i token'dan alınır, kullanıcı girdisinden asla; kullanıcı birden fazla tenant'a üyeyse "aktif tenant" seçimi sunucuda doğrulanır.

```csharp
builder.Services.AddAuthentication().AddJwtBearer(o =>
{
    o.Authority = "https://idp.example.com";          // JWKS ve issuer buradan keşfedilir
    o.TokenValidationParameters.ValidAudience = "fleet-api";
    o.TokenValidationParameters.ValidAlgorithms = ["RS256"];
});
builder.Services.AddAuthorizationBuilder()
    .AddPolicy("CanSendCommands", p => p.RequireClaim("scope", "commands:write")
                                        .RequireRole("dispatcher"));
```

**Takip soruları:** "Refresh token çalındı, ne olur?" Rotasyon + yeniden kullanım tespiti oturumu düşürür; cihaz bağlama (DPoP/mTLS) bir sonraki seviyedir. "Neden API'de session çerezi yerine bearer?" Üçüncü taraf istemci ve mobil için; çerez tarayıcı BFF'i için. "Cognito/Entra/Keycloak farkı?" Yönetilen hizmet (operasyon az, özelleştirme sınırlı) ile self-host (esnek, operasyon sende).
**Sık hata:** `aud` doğrulamamak (başka API için üretilmiş token kabul edilir), uzun ömürlü access token, rolü istemciden gelen alana bağlamak.

### 12.2 API stili seçimi

#### Q93. REST, gRPC, GraphQL ve push (WebSocket/SSE): hangisini ne zaman seçersin?

**Model cevap:** Varsayılan **REST/HTTP + JSON**: araç, önbellek, gözlemlenebilirlik ve üçüncü taraf entegrasyonu en olgun olandır. Sapma gerekçe ister:

| Seçenek | Ne zaman | Bedeli |
|---|---|---|
| **REST** | Genel/dış API, CRUD, HTTP cache | Çok ekranlı istemcide over/under-fetching |
| **gRPC** | İç servislerden servise; düşük gecikme, protobuf sözleşmesi, streaming, deadline | Tarayıcıdan doğrudan değil (gRPC-Web gerekir); insan okuması ve cache zor |
| **GraphQL** | Çok farklı ekranı olan istemci (mobil + web), alan bazlı seçim, birleşik BFF | N+1 (DataLoader), sorgu derinliği/karmaşıklığı sınırı, alan bazlı yetki, HTTP cache zor |
| **WebSocket / SSE** | Sunucudan istemciye canlı veri (§9.8) | Bağlantı yönetimi, ölçekleme (backplane), yeniden bağlanma |
| **Webhook** | Dış sisteme olay bildirimi | İmza (HMAC), tekrar deneme, idempotency, sıra garantisi yok |

- **gRPC sözleşme evrimi:** Alan numarası değişmez ve yeniden kullanılmaz; silinen alanı `reserved` yap; yalnızca yeni alan ekleme geriye dönük uyumludur. Her çağrıda **deadline** ve iptal yay.
- **GraphQL'de risk:** İstemci sorguyu yazdığı için "pahalı sorgu" saldırısı mümkün; derinlik/maliyet limiti, persisted query ve alan düzeyinde yetki şart. .NET'te Hot Chocolate yaygındır.
- **Telematik cevabı:** Cihaz → sunucu ham TCP/MQTT; iç servisler olay akışı (Kafka/Kinesis) ve gerekirse gRPC; dış/mobil/web REST + canlı veri için SSE/SignalR. GraphQL'i yalnızca ekran çeşitliliği gerçekten sorunsa eklerim.

**Mülakat cümlesi:** "Teknolojiyi tüketicisine göre seçerim: dış dünyaya REST, iç sıcak yola sözleşmeli gRPC, canlı veriye push; GraphQL'in maliyetini ekran çeşitliliği haklı çıkarıyorsa ödemeye değer."
**Sık hata:** "gRPC REST'ten hızlıdır, hep onu kullan" demek (ölçmeden); GraphQL'i "REST'in yerine geçti" diye anlatmak.

### 12.3 PostgreSQL: ek konular

#### Q94. MVCC, VACUUM, bloat ve bağlantı havuzu: üretimde neye bakarsın?

**Model cevap:**

- **MVCC:** `UPDATE`, satırın yeni bir sürümünü yazar; eski sürüm (*dead tuple*) hâlâ görünür olabilecek işlemler bitene kadar kalır. **Autovacuum** onları temizler. Temizlenemezse tablo ve indeks **şişer (bloat)**, sorgular yavaşlar.
- **Vacuum'u engelleyenler:** Uzun süren transaction'lar, `idle in transaction` oturumlar, unutulmuş replication slot'ları (xmin ufku ilerlemez). İzleme: `pg_stat_activity` (durum ve süre), `pg_stat_user_tables.n_dead_tup`, `pgstattuple`.
- **Çok güncellenen tablo (örn. `device_last_state`):** HOT update'ten yararlanmak için `fillfactor` düşür (ör. 70–80), güncellenen kolonları indeksleme, autovacuum'u tabloya özel sıklaştır (`autovacuum_vacuum_scale_factor` varsayılanı %20'dir; büyük tabloda çok geç tetiklenir). Telemetri hypertable'ında ise silme yerine chunk drop (§9).
- **xid wraparound:** Autovacuum "freeze" işini yapamazsa veritabanı yazmaya kapanabilir; `age(datfrozenxid)` izlenir.
- **Bağlantılar:** PostgreSQL her bağlantı için ayrı süreç açar; `max_connections` (varsayılan 100) yükseltmek çözüm değildir (bellek, context switch). Uygulama tarafında Npgsql havuzu (varsayılan en fazla 100 bağlantı, connection string başına), arada **PgBouncer** (transaction modunda session durumuna, `SET`, advisory lock, `LISTEN/NOTIFY` ve eski sürümlerde prepared statement'a dikkat).
- **"Connection pool exhausted" hatası:** Tipik nedenler: `DbContext`/connection'ı dispose etmemek, `await` etmeden fire-and-forget DB çağrısı, yavaş sorgu ya da uzun transaction'ın bağlantıyı tutması, paralel `Task.WhenAll` ile çok sayıda eşzamanlı sorgu. Havuzu büyütmeden önce **neden tutulduğuna** bak.

**Takip sorusu:** "Tablo şişti, `VACUUM FULL` yapar mısın?" Tabloyu kilitler; canlı sistemde `pg_repack` ya da partition yeniden oluşturma tercih edilir.
**Sık hata:** `idle in transaction` kalan uzun transaction'ı fark etmemek; havuz sorununu `max_connections`'ı artırarak "çözmek".

#### Q95. "PostgreSQL ile her şeyi yaparız" ne zaman doğru, ne zaman değil?

**Model cevap:** PostgreSQL pek çok ihtiyacı tek sistemde karşılar: `jsonb` (esnek alan), tam metin arama (`tsvector` + GIN), `pgvector` (vektör arama), PostGIS (coğrafi), TimescaleDB/partitioning (zaman serisi), iş kuyruğu (`FOR UPDATE SKIP LOCKED`, §5.3), `LISTEN/NOTIFY`, advisory lock. Her yeni sistem **bir operasyon yüküdür** (yedek, izleme, on-call, bilgi) ve veri tutarlılığı sorunudur (dual write → outbox, §5.6). Bu yüzden önce ölçeği **ölçerim**, sonra ayırırım:

- **Redis/Valkey:** alt milisaniye önbellek, rate limit, kilit, canlı "son durum".
- **Kafka/Kinesis:** yüksek hacimli olay akışı, tekrar oynatma, birden çok tüketici.
- **OpenSearch/Elasticsearch:** alaka sıralaması, facet, büyük ölçekli arama.
- **ClickHouse gibi sütunlu analitik DB:** milyarlarca satırda ad-hoc analitik.
- **DynamoDB:** tek haneli ms anahtar-değer erişimi, sunucusuz ölçek (ad-hoc sorgu ve JOIN yok).

**Cevap şablonu:** "Başlangıçta tek PostgreSQL; darboğaz kanıtlanınca (metrik, EXPLAIN, yük testi) o iş yükünü ayrı sisteme taşırım. Taşırken tutarlılığı outbox ile korurum."
**Sık hata:** "NoSQL daha hızlı" demek (neye göre?); ya da tersine, PostgreSQL'e analitik sorguları ve kuyruğu sınırsız yükleyip üretim yazma yolunu yavaşlatmak.

### 12.4 Ağ ve HTTP temelleri

#### ⭐ Q96. Tarayıcıya URL yazıp Enter'a bastığında ne olur?

**Model cevap:** Klasik ama senior'da derinliğine bakılır; katmanları sırala ve her birinde gecikmenin nereden geldiğini söyle:

1. **URL ayrıştırma, HSTS** (HTTPS zorunluluğu), tarayıcı önbelleği ve service worker kontrolü.
2. **DNS:** tarayıcı → işletim sistemi → özyinelemeli çözücü → yetkili sunucu (TTL ile önbellek). CDN'e `CNAME`; anycast ile en yakın kenar.
3. **Bağlantı:** TCP üçlü el sıkışma. **HTTP/3'te QUIC (UDP)** ve TLS 1.3 el sıkışması birleşiktir. **TLS 1.3:** 1 RTT; SNI ile sertifika seçilir, sertifika zinciri doğrulanır, ALPN ile `h2`/`h3` seçilir. 0-RTT yeniden bağlanma hızlıdır ama yeniden oynatma riski vardır (yalnızca idempotent istekler).
4. **HTTP isteği:** HTTP/1.1 (bağlantı başına sıralı, uygulama katmanında head-of-line blocking), **HTTP/2** (tek bağlantıda çoklama, başlık sıkıştırma; TCP katmanında HOL kalır), **HTTP/3** (akışlar bağımsız; paket kaybı diğer akışları durdurmaz).
5. **Kenar → yük dengeleyici (L4/L7) → uygulama → veritabanı/önbellek.** Yanıt `Cache-Control` ile önbelleğe alınabilir (Q97).
6. **Render hattı:** HTML ayrıştırılır → DOM; CSS → CSSOM; render tree → layout → paint → composite. `<script>` ayrıştırmayı bloklar (`defer`/`async` ile önlenir); CSS render'ı bloklar; görüntü ve font geç gelirse LCP/CLS bozulur. Next.js'te sunucu HTML'i hemen akar, **hydration** ile etkileşim kazanır (§7).

**Nerede ölçersin:** DevTools Network waterfall (DNS, Connect, SSL, TTFB, Content Download), `curl -w` zaman değişkenleri, Lighthouse/CrUX (gerçek kullanıcı verisi).
**Takip soruları:** "TTFB yüksek, nereye bakarsın?" Sunucu işleme/DB sorgusu mu, soğuk başlangıç mı, kenara hiç cache isabeti yok mu, coğrafi uzaklık mı? "Neden HTTP/2'de çok sayıda küçük dosya artık sorun değil?" Çoklama; ama bundle/priorite ve cache hâlâ önemlidir.
**Sık hata:** "DNS → sunucu → HTML" demekle yetinmek; TLS ve render hattını atlamak.

#### Q97. HTTP önbellekleme ve CDN: `Cache-Control`, `ETag`, `stale-while-revalidate`

**Model cevap:**

- **`Cache-Control: max-age=N`** istemci (ve paylaşımlı önbellek) süresini; **`s-maxage`** yalnızca paylaşımlı önbellek/CDN süresini belirler. **`no-cache` "kullanmadan önce doğrula" demektir (saklar!)**, **`no-store` hiç saklama** demektir. Kullanıcıya özel yanıtta `private`.
- **Doğrulama:** `ETag` + `If-None-Match` → değişmediyse `304 Not Modified` (gövde taşınmaz). `Last-Modified` benzerdir, daha zayıf.
- **Hash'li statik dosyalar** (`app.3f9a1c.js`): `public, max-age=31536000, immutable`; HTML kısa süreli ya da `no-cache`. Böylece "önbellek temizleme" sorunu, **dosya adını değiştirmekle** çözülür (Next.js `_next/static` böyle çalışır).
- **`stale-while-revalidate`:** Süresi dolmuş içeriği hemen göster, arka planda yenile. Gecikme/tazelik dengesi.
- **`Vary`:** Cache anahtarını değiştirir (`Accept-Encoding`, `Accept-Language`). `Vary: Cookie` ya da `Authorization` ile kişiye özel yanıtı paylaşımlı önbelleğe koymak veri sızıntısına yol açar; kişiye özel yanıta `private, no-store`.
- **CDN:** Cache anahtarı (URL + seçili başlık/query), purge/invalidations (pahalı; sürümlü URL tercih), **origin shield** (kaynağı koruma), `X-Cache` başlığıyla isabet izleme. Telematikte harita tile'ları, statik konfigürasyon ve herkese açık raporlar CDN'e uygundur; canlı konum uygun değildir.

**Sık hata:** `no-cache` ile `no-store`'u karıştırmak; kimlik doğrulamalı yanıtı paylaşımlı cache'e koymak; HTML'e uzun `max-age` vererek yeni sürümü kullanıcıya ulaştıramamak.

### 12.5 Frontend: TypeScript dili, i18n, zaman ve para

#### ⭐ Q98. TypeScript'te senior düzeyde neleri bilmelisin?

**Model cevap:** (React'e özgü kısım için Q79.)

- **Yapısal tipleme:** Uyum, isimle değil şekille belirlenir. Aşırı özellik kontrolü yalnızca nesne literallerinde yapılır.
- **`any` vs `unknown`:** `any` tip denetimini kapatır ve yayılır; `unknown` kullanmadan önce daraltmayı (narrowing) zorlar. `strict` açık olmalı; `noUncheckedIndexedAccess` dizi/indeks erişimini `T | undefined` yapar. `catch (e)` değişkeni `unknown` ele alınır.
- **Daraltma:** `typeof`, `in`, eşitlik, **discriminated union** ve kullanıcı tanımlı tip koruması (`x is T`). Birleşim tipinde tüm durumları işlediğini **`never`** ile derleme zamanında kanıtla (kapsamlılık kontrolü).
- **Generics:** Kısıt (`<T extends { id: string }>`), varsayılan tip parametresi, çıkarım. Yardımcı tipler: `Partial`, `Required`, `Readonly`, `Pick`, `Omit`, `Record`, `ReturnType`, `Awaited`, `NonNullable`. Koşullu ve eşlenmiş tipleri temel düzeyde oku/yaz.
- **`as const` ve `satisfies`:** `as const` değişmez literal tipleri üretir. `satisfies` ise bir değerin bir tipe uyduğunu **doğrularken** daha dar çıkarımı korur.
- **Tip silinir:** Çalışma zamanında yoktur; dışarıdan gelen veri (API, form, `localStorage`) **Zod gibi bir şemayla** doğrulanmalı, `z.infer` ile tip şemadan türetilmelidir.
- **`interface` vs `type`:** Nesne şekillerinde ikisi de olur; `interface` bildirim birleştirme (declaration merging) ve genişletmede rahat, `type` birleşim/kesişim/koşullu tiplerde zorunlu. Tutarlı ol. `enum` yerine genelde string literal birleşimi ya da `as const` nesne tercih edilir (daha az çalışma zamanı ve tuhaflık).
- **Modüller:** `import type` ile yalnızca tip içe aktar; `tsconfig`'te `moduleResolution`, `isolatedModules` (Babel/SWC/Turbopack uyumu).

```ts
type Cmd =
  | { kind: 'immobilize'; vehicleId: string }
  | { kind: 'setOutput'; vehicleId: string; pin: number; on: boolean };

const assertNever = (x: never): never => { throw new Error(`İşlenmeyen durum: ${JSON.stringify(x)}`); };

function describe(c: Cmd): string {
  switch (c.kind) {
    case 'immobilize': return `Immobilize ${c.vehicleId}`;
    case 'setOutput':  return `Pin ${c.pin} -> ${c.on ? 'ON' : 'OFF'}`;
    default:           return assertNever(c);   // yeni kind eklenip işlenmezse derleme hatası
  }
}

const routes = { map: '/map', fleet: '/fleet' } satisfies Record<string, `/${string}`>;
// routes.map tipi '/map' olarak kalır (Record<string,string> olsaydı genişlerdi)
```

**Takip soruları:** "`Object.keys(obj)` neden `string[]` döner?" Yapısal tipleme nedeniyle nesnenin fazladan anahtarı olabilir. "`Partial<T>` ile `Pick<T,K>` farkı?" Hepsini opsiyonel yapmak ile seçili anahtarları almak.
**Sık hata:** `as` ile tipi "susturmak" (gerçek doğrulama yerine); `any`'yi geçici diye bırakıp yaymak; API cevabını doğrulamadan tipe güvenmek.

#### Q99. Çok dilli, çok saat dilimli ürün: i18n, tarih/saat ve para

**Model cevap:**

- **Metinler:** Cümleleri birleştirme; **ICU MessageFormat** ile çoğul/cinsiyet kuralları. React'te `react-i18next`, `next-intl` ya da FormatJS; RN'de `expo-localization` + i18next. Çevirileri anahtarlarla yönet, eksik anahtar için varsayılan dile düş. Metin uzunluğu dile göre değişir (Almanca ~%30 uzun): sabit genişlik varsayma.
- **Biçimlendirme:** Tarayıcıda/Hermes'te `Intl.DateTimeFormat`, `Intl.NumberFormat`, `Intl.PluralRules`, `Intl.Collator('tr')` (sıralama kültüre göredir). RTL için mantıksal CSS özellikleri (`margin-inline-start`) kullan.
- **Türkçe `i` tuzağı:** `"I".ToLower()` `tr-TR` kültüründe `ı` döner. Kimlik/dosya adı/protokol karşılaştırmalarında `StringComparison.OrdinalIgnoreCase` ya da `ToLowerInvariant()` kullan; kullanıcıya gösterilen metinde kültür duyarlı olanı.
- **Zaman:** Anı (instant) **UTC** olarak sakla (`timestamptz`, `DateTimeOffset`); **saat dilimini IANA kimliğiyle** (`Europe/Istanbul`) sakla, ofsetle değil; yalnızca gösterimde dönüştür. Türkiye 2016'dan beri sabit UTC+3'tür, ama sistemin başka ülkelerde DST ile çalışması gerekir: ilerleme saatinde "olmayan saat", geri alma saatinde "iki kez yaşanan saat" vardır. **Tekrarlayan yerel olay** ("her gün 08:00 raporu") için yerel saat + saat dilimi saklanır, bir sonraki tetik zamanı her seferinde hesaplanır. .NET'te `TimeProvider`, `TimeZoneInfo.FindSystemTimeZoneById("Europe/Istanbul")` (IANA kimlikleri platformlar arası çalışır), karmaşık takvimde NodaTime. JS'te `Date` tuzakları: ay 0 tabanlı, yalnızca-tarih dizesi UTC sayılır; yeni Temporal API için tarayıcı desteğini kontrol et.
- **Telematikte saat:** Cihaz saati sapabilir (GPS saati genelde doğrudur, RTC değil). Hem `event_ts` (cihaz) hem `received_ts` (sunucu) sakla; sapma büyükse alarm.
- **Para:** `float` değil, `decimal` (.NET) ve `numeric` (PostgreSQL); ya da küçük birimde tam sayı (kuruş). Para birimini değerle birlikte taşı; yuvarlama kuralını (banker's rounding, `MidpointRounding`) açık seç.

**Sık hata:** Yerel saati veritabanına yazmak; ofseti saat dilimi sanmak; `ToLower()` ile kimlik karşılaştırmak; çevirilecek metni kodda birleştirmek.

### 12.6 Container ve Kubernetes temelleri

#### ⭐ Q100. .NET servisini nasıl container'laştırır ve Kubernetes'te güvenle çalıştırırsın? (Docker, probe'lar, graceful shutdown, 12-factor)

**Model cevap:**

**İmaj:** Çok aşamalı (multi-stage) build: SDK imajında derle, yalnızca çalışma zamanı imajını yayına al (küçük ve daha az saldırı yüzeyi). `restore` katmanını kaynak koddan ayrı kopyala ki bağımlılık katmanı cache'lensin; `.dockerignore` kullan; **root olmayan kullanıcı** ile çalıştır; imaj etiketini sabitle (digest ya da sürüm); imaj taraması (Trivy vb.) CI'da.

```dockerfile
FROM mcr.microsoft.com/dotnet/sdk:10.0 AS build
WORKDIR /src
COPY src/Api/Api.csproj src/Api/
RUN dotnet restore src/Api/Api.csproj            # bağımlılık katmanı cache'lenir
COPY . .
RUN dotnet publish src/Api/Api.csproj -c Release -o /app --no-restore

FROM mcr.microsoft.com/dotnet/aspnet:10.0 AS final
WORKDIR /app
COPY --from=build /app .
USER $APP_UID                                      # root olarak çalıştırma
EXPOSE 8080
ENTRYPOINT ["dotnet", "Api.dll"]
```

**Kubernetes'te dağıtım:**

- **Probe'lar:** *Readiness* "trafik alabilir mi" (bağımlılıklar hazır), *liveness* "süreç kilitlenmedi mi" (yalnızca sürecin kendisi; **DB'ye bakma**, yoksa DB kesintisinde tüm pod'lar yeniden başlar), *startup* yavaş açılışta liveness'ı bekletir.
- **Kaynaklar:** `requests` zamanlama içindir, `limits` tavandır. .NET container bellek sınırını görür (varsayılan GC heap sınırı bellek limitinin %75'i); sınırı bilmeden Server GC/DATAS ayarlama. CPU limiti throttling yaratabilir; ölç.
- **Graceful shutdown:** Pod silinirken **SIGTERM** gelir; aynı anda endpoint listesinden çıkarılma yarışı vardır. Çözüm: `preStop` ile kısa bekleme (ya da readiness'i hemen düşür), `terminationGracePeriodSeconds`'ı uygulama kapanma süresinden uzun tut, ASP.NET Core `HostOptions.ShutdownTimeout`'u ayarla (.NET 6+ varsayılan 30 sn), uzun işlerde `IHostApplicationLifetime` ve `CancellationToken` ile işi bitir. **TCP gateway'de:** yeni bağlantı kabulünü durdur, mevcut cihazları jitter ile yeniden bağlanmaya yönlendir (reconnect storm, §9).
- **Dayanıklılık:** En az 2 replika, `topologySpreadConstraints` ile AZ'lere dağıt, **PodDisruptionBudget**, HPA (CPU yerine bağlantı sayısı/consumer lag gibi özel metrik).
- **Yapılandırma:** ConfigMap/Secret ya da External Secrets (Secrets Manager); imajda sır yok.

```yaml
containers:
  - name: api
    image: registry.example.com/fleet-api:1.8.3
    ports: [{ containerPort: 8080 }]
    resources:
      requests: { cpu: 250m, memory: 256Mi }
      limits:   { memory: 512Mi }
    startupProbe:   { httpGet: { path: /health/live,  port: 8080 }, periodSeconds: 2,  failureThreshold: 30 }
    livenessProbe:  { httpGet: { path: /health/live,  port: 8080 }, periodSeconds: 10, failureThreshold: 3 }
    readinessProbe: { httpGet: { path: /health/ready, port: 8080 }, periodSeconds: 5,  failureThreshold: 3 }
    lifecycle:
      preStop: { exec: { command: ["sleep", "10"] } }
terminationGracePeriodSeconds: 45      # preStop + uygulama kapanış süresinden uzun
```

**12-factor'ün mülakatta işe yarayan maddeleri:** yapılandırma ortamda (env), durumsuz süreçler (durum Redis/DB'de), loglar stdout'a, hızlı açılış ve zarif kapanış (disposability), geliştirme/üretim paritesi, tek kod tabanı çok dağıtım.
**ECS karşılığı:** Task definition, container health check, `stopTimeout`, ALB target group'ta deregistration delay; Fargate'te aynı prensipler (§10).
**Takip soruları:** "Pod OOMKilled oldu, ne yaparsın?" Bellek limiti ve GC heap davranışı, sızıntı (§3.2), `dotnet-counters`; "readiness neden bazen yetmez?" Yük altında bağımlılık yavaşlarsa pod'u servisten çıkarmak kapasiteyi azaltıp kaskad yaratabilir; timeout/devre kesici ile birlikte düşün.
**Sık hata:** Liveness'a bağımlılık kontrolü koymak; `latest` etiketi; root çalıştırma; kapanışta bağlantıları kesip veri kaybetmek; her şey için CPU HPA'sı.

### 12.7 Süreç: branch stratejisi, feature flag ve AI destekli geliştirme

#### Q101. Trunk-based development mı GitFlow mu? Feature flag'ler ne işe yarar?

**Model cevap:** **Trunk-based:** kısa ömürlü dallar (bir iki gün), küçük PR'lar, her PR'da CI, `main` her zaman yayına hazır. Birleşme sorunları erken çıkar, sürekli teslim kolaylaşır. **GitFlow** (uzun ömürlü `develop`/`release` dalları) sürüm başına paketlenen ürünlerde ve **mobil uygulamada** (mağaza onayı, eski sürümler sahada) kısmen gerekir; mobilde bile `main` + release dalı/etiketi (hotfix için) yeterli olur.

- **Feature flag**, *deploy* ile *release*'i ayırır: kod kapalı hâlde yayına girer, sonra kademeli açılır. Türler: **release flag** (kısa ömürlü), **ops/kill switch** (kritik özelliği anında kapat), **deney** (A/B), **izin** (plan/müşteri bazlı).
- **Borcu:** Eskimiş flag teknik borçtur; sahip, kaldırma tarihi ve temizleme görevi ata. Her iki kolu test et. Kritik yol dışında flag değerlendirmesi hızlı ve hata durumunda **güvenli varsayılana** düşmeli. .NET'te `Microsoft.FeatureManagement`, bağımsız standart olarak OpenFeature; mobilde uzaktan yapılandırma çevrimdışıyken varsayılanla çalışmalı.
- **Birleştirme stratejisi:** squash (temiz geçmiş), rebase (doğrusal), merge commit (bağlamı korur): ekipçe seç ve tutarlı ol. Branch protection, CODEOWNERS, zorunlu CI. Semantic Versioning ve Conventional Commits ile sürüm notu otomasyonu.
- **Monorepo:** pnpm workspaces + Turborepo/Nx ile web ve RN arasında tip/doğrulama paylaşımı (§7.10); .NET tarafında çözüm içinde proje sınırları.

**Sık hata:** Haftalarca yaşayan dal; "flag'i sonra temizleriz"; flag'leri güvenlik/yetki kararı için kullanmak.

#### ⭐ Q102. AI kod asistanlarını (Copilot, Claude Code, MCP) nasıl kullanırsın? Riskleri ve doğrulama yaklaşımın?

**Model cevap:** 2026'da bu soru yaygın ve doğru cevap "kullanmam" ya da "her şeyi yazdırırım" değildir. Ben **AI'yi hızlı bir eş geliştirici gibi kullanırım, kodun sahibi yine bendir:**

- **Nerede değerli:** Boilerplate ve test iskeleti, tanımadığım koda keşif ve özet, refaktör ve migrasyonlar (mekanik dönüşümler), dokümantasyon, hata ayıklama için hipotez üretme.
- **Nerede dikkatli:** Güvenlik açısından hassas kod, eşzamanlılık, veri geçişleri, performans iddiaları (ölçmeden güvenme), bilmediğim bir alan (üretileni değerlendiremiyorsam yayına almam).
- **Doğrulama:** Aynı inceleme standardı (PR'da AI'ın yazdığı kod insan kodu gibi okunur), **testler ve tip/linter/CI bir emniyet ağıdır**, küçük ve incelenebilir değişiklikler (büyük diff'i körlemesine onaylamam), önce plan/spec sonra uygulama. AI'ın yazdığı kodun testini yine AI yazıyorsa niyeti ben tanımlarım; yoksa testler hatayı da "doğrulanmış" hâle getirir.
- **Bağlam disiplini:** Depo içinde mimari kuralları ve komutları anlatan dosyalar (`CLAUDE.md`/`AGENTS.md` benzeri), net ADR'ler; asistan projenin kurallarına uysun.
- **Veri ve gizlilik:** Sır, müşteri verisi, canlı telemetri/konum verisi (KVKK) prompt'a girmez; kurumsal sözleşme/veri saklama ayarlarını şirket politikasına göre kullanırım.
- **Güvenlik riskleri:** Uydurulmuş (hallucinated) paket adları ve tedarik zinciri saldırısı (önerilen bağımlılığı doğrula, lockfile/tarama), **prompt injection** (issue metni, web sayfası ya da MCP aracı çıktısı içindeki gizli talimatlar ajanı yönlendirebilir), ajana **asgari yetki** (salt-okunur token, kum havuzu, komut onayı), lisans/IP riski.
- **Takım etkisi:** Kod üretimi hızlanır, **inceleme darboğaz olur**; PR'ları küçük tutarım. Junior'ların öğrenmesi için "ne ürettiğini anla ve açıkla" kuralı. Ölçüt: çevrim süresi, hata/geri alma oranı; "satır sayısı" değil.

**Kendi deneyimini ekle** (Claude Code, GitHub Copilot ve MCP iş akışlarındaki gerçek bir örnek: ne denedin, neyi yakaladı, neyi yanlış yaptı, ne öğrendin).
**Takip soruları:** "AI'ın yazdığı bir hata prod'a çıkarsa kim sorumlu?" Birleştirmeyi onaylayan ve yayına alan mühendis/ekip; bu yüzden aynı kalite çıtası. "Ajanın repo'da komut çalıştırmasına izin verir misin?" İzole ortamda, onaylı/allowlist komutlarla, sırsız.
**Sık hata:** Çıktıyı çalıştırmadan/okumadan yapıştırmak; AI'ı üretkenlik "sihri" diye anlatmak ya da tümden reddetmek; gizlilik konusunu atlamak.

### 12.8 Performans: yük testi ve kapasite planı

#### ⭐ Q103. Ingestion gateway'ini ve API'yi nasıl yük testinden geçirirsin? Kapasiteyi nasıl planlarsın?

**Model cevap:**

1. **Hedefi tanımla:** SLO (ör. "p95 < 300 ms", "cihazdan haritaya p95 < 3 sn"), tepe yük (RPS, eşzamanlı bağlantı, paket/sn) ve büyüme varsayımı. Hedefsiz yük testi anlamsızdır.
2. **İş yükü modeli:** Gerçek davranışı taklit et: telematikte N bin kalıcı TCP bağlantısı, her biri belirli sıklıkta Codec 8 çerçevesi, ACK bekleme ve **yeniden bağlanma dalgası**. API'de gerçekçi istek karışımı ve veri hacmi (küçük tabloda indeks davranışı yanıltır).
3. **Açık/kapalı model ve coordinated omission:** Kapalı modelde sanal kullanıcılar yanıt beklediği için sistem yavaşlayınca daha az istek yollanır ve gecikme **olduğundan iyi görünür**. Hedefin "saniyede X istek gelir" ise **sabit varış hızı** (k6 `constant-arrival-rate`) kullan.
4. **Little yasası:** `L = λ · W`. Saniyede 1.000 istek × 0,2 sn ortalama süre = ~200 eşzamanlı istek; bağlantı havuzu, thread ve pod sayısı buradan başlar.
5. **Test türleri:** *smoke* (çalışıyor mu), *load* (beklenen yük), *stress* (kırılma noktası), *spike* (ani sıçrama), *soak* (saatler süren; bellek sızıntısı, bağlantı sızıntısı).
6. **Yüzdelikler:** Ortalama yanıltır; p50/p95/p99 ve hata oranı. Gecikmeyi dağılım olarak (histogram) izle.
7. **Darboğaz avı (USE):** Utilization/Saturation/Errors ile CPU, bellek, GC, thread pool kuyruğu, DB bağlantı havuzu, kilit bekleme, ağ. Yük testi sırasında gözlemlenebilirlik (§6.3) açık olmalı.
8. **Araçlar:** k6 (betik tabanlı, CI'ya uygun), NBomber (.NET), Gatling, JMeter, Locust; TCP için özel cihaz simülatörü (kendi yazdığın ya da `System.Net.Sockets` ile).
9. **Kapasite:** Tepe yükün üstünde **boşluk (headroom) %30–40**, otomatik ölçekleme metriği olarak CPU yerine bağlantı sayısı ve consumer lag; ölçekleme **gecikmesi** hesaba katılır. Maliyet metriği: "araç başına aylık $".

**Mülakat cümlesi:** "Önce SLO ve gerçekçi iş yükü, sonra sabit varış hızıyla test, darboğazı gözlemlenebilirlikle bulup tek tek gideririm; yük testi bir kez değil, her büyük sürümde CI'da tekrarlanan bir kapıdır."
**Sık hata:** Üretimden çok küçük veriyle test; yalnızca ortalama gecikmeye bakmak; yük testi ortamında bağımlılıkları mock'layıp gerçek darboğazı gizlemek; soak testini atlayıp sızıntıyı üretimde bulmak.

### 12.9 Telematik: ek alan bilgisi

#### Q104. Telematik alanında §9'da geçmeyen hangi kavramları bilmelisin?

**Model cevap:** Mülakatçı, bu alanda gerçekten çalıştığını kelime dağarcığından anlar. Aşağıdakilerin ne olduğunu ve ürün/mühendislik etkisini bir cümleyle söyleyebilmelisin:

| Kavram | Kısaca | Mühendislik/ürün etkisi |
|---|---|---|
| **Takograf (tachograph)** | AB'de ağır ticari araçlarda sürüş/dinlenme kaydı (Reg. 561/2006, 165/2014); akıllı takograf konum ve uzaktan erişim ekler | Sürücü kartı ve araç ünitesi verisinin uzaktan indirilmesi, uyumluluk raporu. Temel kurallar: günlük 9 saat sürüş (haftada iki kez 10), 4,5 saat sürüş sonrası 45 dk mola, haftalık en fazla 56 saat, iki haftada 90 saat. Türkiye'de uluslararası taşımacılıkta AETR geçerli; kuralları ürün kapsamına göre doğrula |
| **ELD / HOS (ABD)** | FMCSA elektronik kayıt cihazı ve çalışma saati kuralları | Pazar ABD ise ayrı uyumluluk modülü |
| **DTC, UDS, J1939 DM1** | OBD-II arıza kodları, ISO 14229 teşhis protokolü, J1939 aktif arıza mesajı (SPN + FMI) | Arıza alarmları ve önleyici bakım |
| **Sürücü kimliği** | iButton/RFID, BLE etiketi, takograf kartı | Sürücü-araç eşleşmesi, KVKK kapsamında kişisel veri |
| **Sensörler** | BLE (sıcaklık, TPMS), yakıt seviye sensörü (analog/RS-485), kapı/PTO | Kalibrasyon tablosu, yakıt hırsızlığı tespiti, soğuk zincir uyarıları |
| **EV telematiği** | SoC/SoH, şarj oturumu, menzil | OEM verisi ya da sınırlı CAN okuması; şarj noktası protokolü (OCPP) ayrı bir alandır |
| **OEM API'leri (rFMS vb.)** | Üretici bulutundan veri; cihaz takmadan | Aftermarket cihaz ile OEM verisini birleştirme, kota ve sözleşme |
| **AB Data Act** | 12 Eylül 2025'ten itibaren uygulanıyor; bağlı ürün verisine erişim ve paylaşım hakları | Veri sahipliği ve üçüncü tarafa aktarım ürün kararlarını etkiler (hukuk onayıyla) |
| **eCall (112)** | AB'de yeni homologasyonlu araçlarda zorunlu kaza bildirimi | Aftermarket'te ayrı, kaza algılama (ivme) ve bildirim ürünleri |
| **Video telematiği (ADAS/DMS)** | Olay tetiklemeli kısa video, yorgunluk/dikkat tespiti | Bant genişliği (Wi-Fi/LTE ile yükleme, S3 imzalı URL), yüz verisi ve KVKK |
| **Ağ geçişi** | 2G/3G kapatılıyor; LTE Cat 1/Cat M1/NB-IoT, eSIM, çok operatörlü SIM | Filo cihaz yenileme planı, APN/SIM yönetimi, SMS ile uyandırma/komut yedeği |
| **GNSS karıştırma ve sabotaj** | Jamming/spoofing, güç kesme, anten sökme | Ani uydu/sinyal kaybı ve ana güç kesintisi alarmları; imkânsız sıçrama filtresi (§9.6) |

**Cevap şablonu:** "Bu alanın tamamında production'da çalışmadıysam bile hangi veriyi nereden aldığımı, hangi regülasyonun ürüne etki ettiğini biliyorum; uyumluluk kapsamını hukuk ve ürün ekibiyle netleştiririm."
**Sık hata:** Mevzuat rakamlarını mutlak doğru diye aktarmak (ülke ve araç sınıfına göre değişir); sürücü kimliği ve konumu "teknik veri" sanıp KVKK'yı atlamak.

### 12.10 Ek live-coding görevleri

#### Q105. Telematik temalı üç hızlı görev: aralık birleştirme, top-K, kayan pencere

**Model cevap:** Her görevde önce **girdi/çıktı örneği ve sınır durumlar**, sonra basit çözüm, sonra karmaşıklık (protokol §11.6). Üçü de gerçek sorunlardan gelir.

**1) Trip birleştirme (interval merge):** "Aralarındaki boşluk ≤ 5 dk olan trip'leri birleştir." Sırala (O(n log n)), tek geçişte birleştir. Tuzak: iç içe aralıkta bitiş zamanı **küçülmemeli**.

```csharp
public record Trip(DateTime Start, DateTime End);

public static List<Trip> MergeTrips(IEnumerable<Trip> trips, TimeSpan maxGap)
{
    var result = new List<Trip>();
    foreach (var t in trips.OrderBy(t => t.Start))
    {
        if (result.Count > 0 && t.Start - result[^1].End <= maxGap)
        {
            var last = result[^1];
            result[^1] = last with { End = t.End > last.End ? t.End : last.End };  // iç içe: End küçülmesin
        }
        else result.Add(t);
    }
    return result;
}
```

**2) Top-K en hızlı araç:** Hepsini sıralamak O(n log n); **boyutu K olan min-heap** ile O(n log K) ve bellek O(K). .NET 6+'ta `PriorityQueue<TElement,TPriority>` min-heap'tir: en küçük tepede durur, K'yı aşınca en küçüğü at.

```csharp
public static List<(string Id, double Speed)> TopK(IEnumerable<(string Id, double Speed)> items, int k)
{
    var heap = new PriorityQueue<(string Id, double Speed), double>();
    foreach (var it in items)
    {
        heap.Enqueue(it, it.Speed);
        if (heap.Count > k) heap.Dequeue();            // en küçüğü at: K büyük kalır
    }
    var res = new List<(string Id, double Speed)>();
    while (heap.TryDequeue(out var x, out _)) res.Add(x);
    res.Reverse();                                      // büyükten küçüğe
    return res;
}
```

**3) Son N dakikanın ortalama hızı (kayan pencere):** Kuyruk + toplam tutulur; yeni değer eklenirken pencere dışına çıkanlar kuyruğun başından atılır. Her ekleme amorti O(1). Konuşulacaklar: sırasız veri (watermark ve geç gelen kayıt, §9.4), kayan noktada birikim hatası (periyodik yeniden hesap), cihaz başına ayrı pencere ve bellek sınırı.

**Diğer çalışma listesi:** iki sıralı listeyi birleştirme, `Dictionary` ile mutabakat (§5.1), en uzun tekrarsız alt dizi (kayan pencere), BFS/DFS (bölge/komşuluk), ikili arama (zaman damgasına göre en yakın kayıt), dedup `(device_id, ts)`.
**Sık hata:** Önce karmaşık çözüme atlamak; sınır durumları (boş girdi, tek eleman, eşit zaman damgası) konuşmamak; kodu yazdıktan sonra örnekle elle çalıştırmamak.

### 12.11 Kapsam dışı alanlar ve "bilmediğimde ne derim"

Bu dosya şunları **derinlemesine** işlemez. Gerekirse kısa bir çalışma yap; aşağıdaki cümle kalıbı dürüst ve güvenli bir cevap verir.

| Alan | Neden önemli olabilir | Hızlı çalışma önerisi |
|---|---|---|
| Azure / GCP eşdeğerleri | Şirket AWS dışı bulutu kullanıyorsa | AWS servis karşılıklarını eşle (Event Hubs ↔ Kinesis/Kafka, AKS ↔ EKS, Entra ID ↔ Cognito) |
| İleri Kubernetes (Helm, operatörler, service mesh) | Platform ekibi yoksa uygulama ekibi bilmek zorunda | Q100'ü pekiştir; Helm chart okuma, Ingress, HPA |
| Terraform ayrıntısı | IaC ortak dil olabilir | State, modül, plan/apply, drift |
| Native mobil (Swift, Jetpack Compose) | RN'de native modül yazarken | Expo Modules API ile küçük bir Kotlin modülü |
| Blazor / WebAssembly | .NET ağırlıklı ekiplerde ön yüz seçeneği | React ile farkı (render modeli, JS interop) |
| Veri mühendisliği (Spark, ClickHouse) | Analitik ekip ile çalışırken | Parquet/Athena (§10) ve OLAP/OLTP farkı |
| ML/AI özellikleri (anomali tespiti, ETA tahmini) | Ürün yol haritasında olabilir | Baseline kural + istatistik önce; model sonra |

**Bilmediğimde şöyle derim:** "Bunu production'da doğrudan kullanmadım. Benzer problemi şöyle çözdüm: *(kendi örneğin)*. Bu alanda ilk iki haftada şu kaynakla ve küçük bir prototiple derinleşirim." Uydurmaktan çok daha güçlü bir senior sinyalidir.

### 12.12 İngilizce Model Cevaplar

- *"How do you prevent BOLA / IDOR in an API?"* — "Ownership is enforced inside the query, not just at the controller: every read and write is scoped by tenant and owner, and I return 404 for objects the caller can't access. I back it with global query filters, row-level security in PostgreSQL, and automated cross-tenant tests, because a single forgotten filter is enough for a breach."
- *"Why Authorization Code with PKCE rather than the implicit flow?"* — "The implicit flow returns tokens in the browser URL, where they can leak through history, referrers and scripts. Authorization Code with PKCE keeps the token exchange on a back channel and binds it to a one-time verifier, so an intercepted code is useless. For web apps I go one step further with a backend-for-frontend and HttpOnly cookies."
- *"Liveness vs readiness probes?"* — "Readiness decides whether a pod receives traffic, so it can include dependencies. Liveness decides whether the process should be restarted, so it must only check the process itself. If liveness depends on the database, a database outage restarts every pod and turns an incident into a cascade."
- *"How would you load test the ingestion gateway?"* — "I start from the SLO and a realistic workload: tens of thousands of persistent TCP connections sending valid Codec 8 frames, plus reconnect storms. I use a constant arrival rate to avoid coordinated omission, watch percentiles rather than averages, find the bottleneck with USE metrics, and keep a soak test to catch leaks."
- *"When is PostgreSQL enough, and when would you add another system?"* — "I start with PostgreSQL because every extra system adds operational cost and a consistency problem. Once metrics or load tests prove a bottleneck, I move that workload out: Redis for sub-millisecond reads, Kafka for high-volume streams, a columnar store for analytics, and I keep them consistent with an outbox."
- *"How do you use AI coding assistants responsibly?"* — "I treat them as a fast pair programmer and stay accountable for the result: small reviewable diffs, tests and CI as the safety net, no secrets or customer data in prompts, least privilege for agents, and extra scrutiny for suggested dependencies and anything touched by prompt injection."


## 13. Hızlı Gözden Geçirme: Cheat Sheet

**Tarihler ve sürümler**
- .NET 10 LTS (Kas 2025 → Kas 2028). .NET 8 ve 9 EOL: **10 Kasım 2026**. .NET 11 RC1, GA Kasım 2026 (STS, C# 15 union). C# 14: extension blocks, `field`, `?.=`. C# 13: `Lock`. EF 10: LeftJoin/RightJoin, named filters.
- React **19.3** (ViewTransition, Fragment ref, `browser()`), 19.2 (`<Activity>`, `useEffectEvent`), Compiler 1.0. Next.js **16.4**: Cache Components (17'de varsayılan), `proxy.ts`, async `params`, Turbopack. RSC RCE: CVE-2025-55182.
- RN 0.82 = yalnızca New Architecture; 0.84 Hermes V1; Expo SDK 54 = Legacy'nin son sürümü; SDK 56 = RN 0.85 / React 19.2.
- PostgreSQL 18: skip scan. Kafka 4.x: KRaft-only. Ticari: MediatR, AutoMapper, MassTransit v9, FluentAssertions v8.
- Timestream LiveAnalytics 20.06.2025'ten beri yeni müşteriye kapalı. TimescaleDB RDS/Aurora'da yok.

**Temel .NET (en sık sorulanlar)**
- Platform: C# → IL → JIT (tiered, dynamic PGO) / ReadyToRun / Native AOT. CLR, CTS, CLS, BCL. .NET Framework ≠ .NET Core ≠ .NET 5+ ≠ .NET Standard.
- **Value vs reference:** stack/heap *uygulama detayıdır*; struct sınıf alanıysa/dizi elemanıysa/boxed ise heap'te. Boxing = value type → `object`/interface. `string` immutable; kültür (Turkish-I): `StringComparison.Ordinal`/`OrdinalIgnoreCase`.
- `==` / `Equals` / `GetHashCode`: Equals'ı override edersen GetHashCode'u da; **mutable key** Dictionary'de kaybolur. `record` = değer eşitliği.
- `const` (derleme zamanı) / `readonly` (çalışma zamanı). `ref/out/in`. Referans tipler de değer olarak (referansın kopyası) geçer. Nullable: `int?` ≠ NRT.
- abstract vs interface; `virtual/override/new/sealed`; extension method; delegate/event/closure (**event aboneliği çıkılmazsa leak; döngü değişkeni closure'ı**); generics + covariance/contravariance.
- **Koleksiyonlar:** `List` Add amortize O(1); `Dictionary/HashSet` **ortalama** O(1); `SortedX` O(log n); `FrozenDictionary` oku-çok. LINQ **deferred execution**, çoklu enumerasyon tuzağı, `First/Single`, `Any` > `Count()>0`. `ConcurrentDictionary.GetOrAdd/AddOrUpdate` delegesi birden çok çalışabilir.
- **Exception:** `throw;` stack'i korur, `throw ex;` sıfırlar; `when` filtresi; `using`/`await using`; exception normal akış için değil.
- **Async:** I/O → async/await (thread tutmaz), CPU → `Task.Run`/`Parallel`. `.Result/.Wait()` = starvation; `async void` yalnızca event handler; `lock` içinde `await` yok → `SemaphoreSlim`; `CancellationToken` zinciri; `Task.WhenAll`.
- **ASP.NET Core:** middleware sırası (Exception → HTTPS → Routing → CORS → AuthN → AuthZ → RateLimiter → Endpoint). DI: Singleton/Scoped/Transient, **captive dependency**, keyed services. Options: `IOptions` / `Snapshot` / `Monitor`, `ValidateOnStart`. `ProblemDetails` (RFC 9457). Health: live / ready.
- **EF Core:** DbContext = UoW + identity map, scoped. `AsNoTracking`, N+1 → Include/projection, `ExecuteUpdate`, bulk → `COPY`, idempotent migration.
- **HttpClient:** `IHttpClientFactory`; resilience handler; retry yalnızca idempotent.

**İleri .NET**
- GC: Gen0/1/2, LOH ≥ 85.000 B, POH. Gen0/1 durdurur; yalnızca background Gen2 eşzamanlı. Server GC + DATAS (.NET 9+). `GC.Collect()` çağırma. Finalizer yerine `SafeHandle`.
- Leak avı: `dotnet-counters` → `dotnet-gcdump` → gcroot. Sınırsız cache, static event, captive dependency.
- Allocation: Span/Memory, ArrayPool, `SequenceReader`, readonly struct + `in`, `LoggerMessage`. Async state machine struct'tır, askıda boxlanır.
- Channels: bounded + Wait = backpressure; sıra için IMEI'ye göre partition. Reflection: cache → delegate → expression → source generator.
- OTel: `Activity`/`Meter`/`ILogger` yerleşik, **SDK NuGet**; W3C `traceparent`; mesaj header'ında taşı.

**Mimari ve pattern**
- Varsayılan: modular monolith + ayrı ingestion. Outbox + idempotent consumer. Saga: az adım koreografi, çok adım orkestrasyon (**mutlak değil**). ACL vendor protokolü için. CQRS bedeli: eventual consistency.
- **DIP ≠ IoC ≠ DI ≠ container; DI GoF değil.** Strategy = nasıl, Factory = kim. SOLID bir sezgi aracı; YAGNI. Kullanma: EF üstüne generic repo, tek implementasyonlu interface, erken microservice, telemetriye event sourcing.
- GoF ↔ .NET: middleware = Chain of Responsibility, DelegatingHandler = Decorator, BackgroundService = Template Method, komut = Command + State.

**Veri ve dağıtık sistemler**
- Big O = **büyüme üst sınırı**; worst/avg/amortized ayrı. Mutabakat: `HashSet` O(n+m); bellek kısıtında sort + iki işaretçi; Bloom filter yaklaşık.
- **PostgreSQL:** clustered index yok (heap; `CLUSTER` bir kez). Composite: eşitlik önde, aralık sonda; iki eşitlikte sıra fark etmez; PG18 skip scan. SARGable: kolonu fonksiyona sokma → expression index. Partial index, `INCLUDE`, BRIN (zaman serisi), `CONCURRENTLY`. Teşhis: `EXPLAIN (ANALYZE, BUFFERS)`, `pg_stat_statements`.
- **İzolasyon (PG):** RU = RC; RC varsayılan; RR = snapshot (+`40001` retry); Serializable = SSI, bloklamaz, **retry gerekir**.
- **Lost update:** önce atomik `SET x = x + @v`; koşullu `WHERE amount >= @a` + satır sayısı; sonra optimistic (`xmin`/version), `FOR UPDATE`, Serializable + retry. Check-then-act yarışı!
- ACID'deki C ≠ CAP'teki C (linearizability). CAP **işlem bazında**; Redis Cluster CP değildir; PACELC.
- Sharding son çare: range (hotspot), hash (re-shard), **consistent hashing** + virtual nodes; shard key = erişim kalıbı; hot shard → Redis sayaç + write-behind / sharded counter.
- Cache: cache-aside; stampede = jitter + single-flight + refresh-ahead/XFetch; HybridCache yalnızca tek örnek içinde; Redis kilit = `SET NX PX` + token + Lua release; **lock yetmez, idempotent ol** (+ fencing token).
- Kafka: partition içinde sıra, consumer group, `acks=all` + `min.insync.replicas`, idempotent producer, at-least-once → idempotent consumer, DLQ. Outbox: aynı transaction + relay (`SKIP LOCKED`/CDC) + inbox.

**Kalite ve operasyon**
- Test piramidi; AAA, **tek mantıksal durum/test**; stub/mock/fake farkı; Testcontainers; coverage = gösterge. Wallet: unit test yetmez, paralel integration testi.
- Rolling (kapasite düşer, iki sürüm) / Blue-Green (anında switch, %100 trafik) / **Canary** (%5, metrik izle, riskli değişiklik) + feature flag. DB: expand → migrate → contract. Drain + reconnect storm.
- Gözlemlenebilirlik: trace + log + metrik; Trace ID vs Span ID; yüksek kardinalite etiket yok; SLO'ya alarm.
- Cross-cutting: .NET'te yerleşik AOP yok → middleware, filter, decorator, pipeline behavior.

**React / Next.js**
- Render (saf) vs commit; `key` kararlı id (indeks değil); state'i aşağı it; türetilmiş veriyi state'e koyma; effect = dış sistemle senkron, cleanup, `AbortController`; fonksiyonel `setState`; stale closure → `useEffectEvent`.
- Performans: ölç → state colocation → memo; **React Compiler** otomatik; sanallaştırma; `startTransition`. Core Web Vitals: LCP < 2,5 sn, **INP** < 200 ms, CLS < 0,1.
- React 19: Actions, `useActionState`, `useOptimistic`, `use`, `ref` prop. 19.3: ViewTransition, Fragment ref.
- Next.js: Server Components varsayılan, `'use client'` yapraklara; **Server Action = herkese açık endpoint → authz + doğrulama**; Cache Components = `'use cache'` + `cacheLife` + `cacheTag`; `proxy.ts`; async `params`. Token: `localStorage` değil, BFF + HttpOnly çerez.
- Canlı harita: React'i hot path'ten çıkar, tampon + 1 sn toplu GeoJSON güncellemesi, viewport aboneliği, cluster, sanallaştırılmış liste.

**React Native**
- JSI (senkron, C++), TurboModules (lazy, codegen), Fabric, Bridgeless. FlashList, dar selector, harita için throttle + cluster + native layer.
- TanStack Query (server state) + Zustand/RTK (client). Android 14 konum: FGS `type=location` + `FOREGROUND_SERVICE_LOCATION`, ön planda başlat, kademeli izin.
- SecureStore/Keystore, pinning + yedek pin, PKCE. OTA = yalnızca JS (`runtimeVersion`/fingerprint). Jest + RNTL, Detox/Maestro; GitHub Actions → EAS Build/Submit; Sentry + source map.

**Telematik**
- Teltonika: IMEI handshake (0x01) → `00000000 | len(4) | codec(08/8E/10) | N1 | records | N2 | CRC-16/IBM(4)`. Big-endian, lat/lon ×10⁷, 8E'de IO ID 2 byte. ACK = kabul edilen kayıt sayısı (4 B); ACK gelmezse retransmit → duplicate. **ACK'i dayanıklı yazımdan sonra gönder.**
- J1939/FMS: 250 kbit/s. EEC1 61444 (RPM, 0,125/bit), LFC 65257 (toplam yakıt, 0,5 L/bit), HOURS 65253 (0,05 h/bit), LFE 65266, ET1 65262 (−40 offset). Yakıt = sayaç **farkı** (reset/wrap-around dikkat).
- Ingest: Pipelines + bounded channel; `(device_id, ts)` idempotent upsert; event time ≠ processing time; watermark; "son konum" yalnızca `ts > last_ts`.
- TimescaleDB: hypertable (1 gün chunk), compression (segmentby device_id, 7 gün), retention (chunk drop), continuous aggregate, son durum ayrı tabloda.
- Geofence: spatial index → PIP → durum makinesi + histerezis/dwell. Trip: ignition önce, durak eşiği, minimum mesafe. Filtre: HDOP/uydu, imkânsız hız, Kalman. Map matching: HMM + Viterbi. Skor: Δv/Δt, 100 km normalizasyonu.
- Komut: Created → Sent → Acked / TimedOut, correlation id, `Idempotency-Key`, kritik komutta güvenlik koşulu. OTA: kademeli + imzalı + rollback.
- KVKK: konum = yaşam alışkanlıklarını ortaya çıkaran veri (2021/1303); mesai dışı takip için gizlilik modu; çalışan açık rızası zayıf dayanak; md. 9: standart sözleşme + 5 iş günü içinde bildirim. EDPB 01/2020: yalnızca gerekliyse, varsayılan sürekli değil, saklama kısa.

**AWS**
- IoT Core: QoS 0/1 (QoS 2 yok), 128 KB, 5 KB ücret birimi, shadow 8 KB, persistent session 1 saat. Ham TCP binary konuşmaz → kendi gateway (NLB + ECS).
- Kinesis: shard 1 MB/s ya da 1.000 kayıt/s yazma, 2 MB/s okuma; partition key = cihaz. SQS = iş kuyruğu, SNS = fan-out, EventBridge = iş event'i yönlendirme, Kinesis/MSK = telemetri.
- Lambda .NET 10: Native AOT, SnapStart; TCP gateway için uygun değil. Fargate varsayılan, EKS K8s bilgisi varsa. Güvenlik: GitHub OIDC, least privilege, VPC endpoint (NAT maliyeti), Secrets Manager. IaC: CDK (C#/TS), Terraform.

**Eksik konular (§12)**
- **Güvenlik:** BOLA = sahiplik koşulu **sorgunun içinde**, 404 dön; allowlist DTO (mass assignment); rate limit, SSRF allowlist + IMDSv2. OAuth: **Code + PKCE** (SPA/mobil), Client Credentials (servis); implicit/ROPC yok; JWT'de `iss`, `aud`, `exp`, algoritma allowlist; JWT imzalı, **şifreli değil**; refresh rotasyonu. RBAC < policy < resource-based < ReBAC.
- **API stili:** varsayılan REST; iç sıcak yol gRPC (alan numarası değişmez, `reserved`, deadline); GraphQL = ekran çeşitliliği varsa (N+1, derinlik limiti); canlı veri SSE/SignalR.
- **PostgreSQL:** MVCC → dead tuple → autovacuum; vacuum'u uzun transaction ve slot engeller; sıcak tabloda `fillfactor`; havuz tükenmesi = sızıntı/uzun transaction, `max_connections`'ı artırma; "önce tek PostgreSQL, darboğaz kanıtlanınca ayır".
- **HTTP:** DNS → TCP/QUIC → TLS 1.3 → HTTP/2-3 → CDN → app; `no-cache` ≠ `no-store`; hash'li dosya `immutable`; kişiye özel yanıt `private, no-store`; `Vary` dikkat.
- **TypeScript:** `unknown` > `any`; discriminated union + `never` kapsamlılık; `satisfies`; tip silinir → Zod; `import type`. **Zaman:** UTC + IANA saat dilimi, ofset ≠ saat dilimi; `ToLowerInvariant`/Ordinal (Türkçe `i`); para `decimal`/`numeric`.
- **Container/K8s:** multi-stage, non-root, restore katmanı cache; readiness = trafik, **liveness = yalnızca süreç (DB'ye bakma)**; SIGTERM + `preStop` + grace period; PDB, AZ yayılımı; bellek limiti ↔ GC; OOMKilled → sızıntı/limit.
- **Süreç:** trunk-based + feature flag (deploy ≠ release, flag borcu); AI asistan: sahibi sensin, küçük diff, test/CI emniyet ağı, sır/KVKK verisi yok, prompt injection, hallucinated paket, ajana asgari yetki.
- **Yük testi:** SLO → gerçekçi iş yükü → **sabit varış hızı** (coordinated omission), p95/p99, Little: `L = λ·W`, soak testi, headroom %30–40.
- **Telematik ek:** takograf (9 sa/gün, 4,5 sa → 45 dk mola, 56 sa/hafta, 90 sa/2 hafta), rFMS/OEM API, Data Act (12.09.2025), 2G/3G → LTE, GNSS jamming/sabotaj alarmı.
- **Live coding:** interval merge (iç içe aralıkta `End` küçülmesin), top-K = min-heap O(n log K), kayan pencere = kuyruk + toplam. Bilmediğinde: "production'da kullanmadım, benzerini şöyle çözdüm, ilk iki haftada şöyle öğrenirim."

**Mülakat taktiği**
- Önce gereksinim ve ölçeği sor → kaba tahmin → basit tasarım → darboğaz ve trade-off → gözlemlenebilirlik + güvenlik ile kapat. Her cevapta "ne zaman kullanmam". STAR: 1 rakam, 1 trade-off, 1 öğrenim.
- Tıkanınca: sesli düşün → parçala → brute force yaz → bilmediğini soyutla → ipucu iste. Edge case'leri onlar sormadan söyle (null, boş, race).
- Bilmediğini dürüstçe söyle. Mülakat iki yönlü: sen de soru sor (§11.7).

---


## 14. Dikkat Edilecekler ve Kaynaklar

- **Sürüm bilgileri hızlı değişiyor.** .NET 11 henüz RC aşamasında; GA özellikleri değişebilir ("deneysel" bileşenleri kesinleşmiş gibi anlatma). React 19.3 ve Next.js 16.4 bilgileri **8 Ekim 2026'da react.dev ve nextjs.org yayınlarından** teyit edildi; Next.js sürümleri sık güvenlik yaması alıyor, mülakat öncesi resmi blogdan son durumu kontrol et. Expo SDK 57 / RN 0.86 eşleşmesi üçüncü taraf bir rehbere dayanıyor; expo.dev changelog'undan doğrula. Hermes V1 bellek regresyonu SDK 56'da bilinen bir sorun olarak duyuruldu, sonradan düzeltilmiş olabilir.
- **PostgreSQL, Redis, Kafka ve CAP düzeltmeleri** resmi belgelere ve Kleppmann'ın CAP yazısına dayanır (kaynaklar aşağıda). Kafka sürümü ve MSK'nin desteklediği Kafka sürümleri farklı olabilir; AWS'te kullanılabilir sürümü kontrol et.
- **AWS limitleri** bölgeye göre değişebilir ve bir kısmı artırılabilir kotadır: AWS General Reference'ın "AWS IoT Core endpoints and quotas" sayfasına göre hesap başına saniyelik rule evaluation varsayılanı 20.000, ancak bazı bölgelerde (Stockholm, Paris, İspanya, Bahreyn/BAE, Hong Kong, Kanada Central, GovCloud) 2.000'dir ("Adjustable: Yes"). Rakam verirken **"varsayılan kota"** de.
- **Teltonika wiki'de tutarsızlık:** farklı sayfalarda maksimum paket boyutları çelişkili. Parser'ı sabit bir maksimuma değil, length alanına ve makul bir üst sınıra göre yaz.
- **KVKK:** Şirket aracını kullanan çalışanın GPS verisini doğrudan konu alan yayımlanmış bir Kurul kararı bulunamadı. Mesai dışı takip ve meşru menfaat yorumları Kurum dergisindeki akademik makaleye ve uygulamacı görüşlerine dayanıyor. 2026/921 sayılı ilke kararı biyometrik veriyle ilgilidir; konuma ancak benzetme yoluyla uygulanabilir. **Bu metin hukuki tavsiye değildir.**
- **Senin notların** bu role (.NET, RN, telematik, AWS) göre uyarlandı. Senin kendi deneyimin ve STAR hikâyelerin (§11.5) notlarından alındı; **rakamları kendi gerçek ve doğrulanabilir verilerinle** ifade et. Temsilî olarak işaretlenen örnekleri (reconnect storm) kendi olayınla değiştir.
- Kod örnekleri mülakat anlatımı içindir, üretime olduğu gibi alınmamalıdır (hata yönetimi, güvenlik ve yapılandırma sadeleştirilmiştir).

**Ana kaynaklar** (birincil/resmi): [React 19.3](https://react.dev/blog/2026/09/09/react-19-3) · [Next.js 16.4](https://nextjs.org/blog/next-16-4) · [PostgreSQL: çok kolonlu indeksler](https://www.postgresql.org/docs/current/indexes-multicolumn.html) · [PostgreSQL: transaction isolation](https://www.postgresql.org/docs/current/transaction-iso.html) · [Redis Cluster spesifikasyonu](https://redis.io/docs/latest/operate/oss_and_stack/reference/cluster-spec/) · [Kleppmann: Please stop calling databases CP or AP](https://martin.kleppmann.com/2015/05/11/please-stop-calling-databases-cp-or-ap.html) · [.NET destek politikası](https://dotnet.microsoft.com/platform/support/policy/dotnet-core) · [HybridCache (Microsoft Learn)](https://learn.microsoft.com/aspnet/core/performance/caching/hybrid) · Microsoft Learn ve .NET Blog, reactnative.dev, Expo changelog, Android Developers, AWS dokümantasyonu, Teltonika Wiki, FMS-Standard, KVKK Kurul kararları, EDPB Guidelines 01/2020. Metinler birebir kopyalanmadı, özetlendi.
