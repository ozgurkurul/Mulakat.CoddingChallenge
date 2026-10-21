## Veritabanı İndeksleme (Database Indexing) ve Sorgu Optimizasyonuna (Query Optimization)
Veritabanı indeksleme, yüksek ölçekli sistemlerde sorgu performansını belirleyen en kritik unsurdur. Milyonlarca satırlık bir tabloda yavaş çalışan bir sorguyu hızlandırmak için mülakatlarda beklenen teorik ve pratik yaklaşımlar şu şekildedir:

### 1. İndekslerin Temel Mantığı ve B-Tree 
İndeksleme temelde, sorguların karmaşıklığını $O(N)$ (Full Table Scan - tüm tabloyu satır satır tarama) seviyesinden, $O(\log N)$ (Index Seek - ağaç yapısı üzerinden bulma) seviyesine indirme işlemidir. 
*   İlişkisel veritabanları (PostgreSQL, SQL Server) indeksleri genellikle **B-Tree (Balanced Tree)** veri yapısında tutar.
*   Bu yapı, aranan değere ulaşmak için veriyi her adımda eleyerek (logaritmik olarak) ilerler ve milyonlarca kayıt içinde veriyi birkaç disk okumasıyla bulur.

### 2. Clustered vs. Non-Clustered Index (Fiziksel ve Mantıksal Sıralama)
Mülakatlarda aralarındaki fark mutlaka sorulur:
*   **Clustered Index:** Verinin disk üzerinde fiziksel olarak nasıl sıralanacağını belirler. Bu nedenle bir tabloda **sadece 1 tane** olabilir (Genellikle Primary Key'dir). Kitabın sayfa numaralarına göre sıralanması gibidir.
*   **Non-Clustered Index:** Verinin fiziksel sırasını değiştirmez. Bunun yerine, aranan değerin diskte hangi adreste/satırda olduğunu gösteren bir referans (pointer) yapısı oluşturur. Bir tabloda birden fazla olabilir. Kitabın sonundaki alfabetik indeks (fihrist) gibidir.

### 3. SARGable (Search Argument Able) Sorgular
Mülakatların en büyük tuzaklarından biridir. İlgili kolonda indeks olsa bile, sorguyu yanlış yazarsan veritabanı o indeksi kullanamaz ve Full Table Scan yapar.

*   **Kötü (İndeksi Bozan) Kullanım:** `WHERE` koşulunda indeksli kolonu bir fonksiyona veya işleme sokmak.
    ```sql
    -- "CreatedAt" kolonunda indeks var ama bu sorgu indeksi KULLANAMAZ.
    SELECT Id FROM Users WHERE YEAR(CreatedAt) = 2025;
    ```
*   **İyi (SARGable) Kullanım:** İndeksli kolonu yalın bırakmak.
    ```sql
    -- İndeks KULLANILIR (Index Seek).
    SELECT Id FROM Users WHERE CreatedAt >= '2025-01-01' AND CreatedAt < '2026-01-01';
    ```

### 4. Over-Indexing (Aşırı İndeksleme) ve Trade-off
Her kolona indeks koymak sistemi hızlandırmaz, aksine çökertir.
*   **Trade-off:** İndeksler okuma (Read) işlemlerini hızlandırırken, yazma (Insert/Update/Delete) işlemlerini yavaşlatır. Çünkü tabloya her yeni satır eklendiğinde, o tabloya bağlı tüm B-Tree indeks yapılarının da yeniden güncellenmesi ve dengelenmesi gerekir.
*   Yüksek okuma/yazma oranına (Read-Heavy) sahip tablolarda indeksleme bol yapılabilirken, sürekli veri yazılan (Write-Heavy) log veya telemetri tablolarında indeksleme minimumda tutulmalıdır.

---

### Mülakat Senaryosu: Composite (Bileşik) İndeks ve Sıralama

Scorp uygulamasında milyonlarca satırlık bir `Users` (Kullanıcılar) tablon var. Uygulamadaki bir özellik için şu SQL sorgusu sürekli çalışıyor:

`SELECT * FROM Users WHERE Country = 'TR' AND Username = 'ozgur';`

Bu sorguyu hızlandırmak için hem `Country` hem de `Username` kolonlarını içeren tek bir **Composite Index (Bileşik İndeks)** oluşturmaya karar verdin. 

Bileşik indekslerde kolonların tanımlanma sırası çok kritiktir. İndeksi `(Country, Username)` sırasıyla mı yoksa `(Username, Country)` sırasıyla mı oluşturursun? Bu sırayı belirlerken veritabanı teorisinde hangi kavramı (örneğin seçicilik/cardinality) dikkate alırsın ve neden?

Cevap: Where koşulunda arama yaparken composite indeksli kolonlarda arama yaparken önce ilk kolona bakacağından dolayı, `(Country, Username)` sırasıyla indek oluştururdum. Böylece Country: 'TR' ile arama yaptığımda Türkiye'deki kullanıcılara hızlıca ulaşabilirim. Ama Username ilk sırada olsaydı ve where koşulunda arama yapmaya çalışsaydım, indek arama yerine full scan yapacaktı sistem.   

Söylediklerinde veritabanı teorisinin en önemli kurallarından biri olan **"Leftmost Prefix" (En Sol Öncelik)** kuralının mantığını çok doğru yakalamışsın. Gerçekten de bir bileşik indekste, B-Tree aramaya her zaman en soldaki kolondan başlar. Eğer indeks `(Username, Country)` sırasındaysa ve sen sadece `WHERE Country = 'TR'` diye aratırsan, veritabanı indeksi kullanamaz ve Full Table Scan yapar.

**Ancak**, sorumuzda her iki kolon da `WHERE` şartında var (`Country = 'TR' AND Username = 'ozgur'`). İşte mülakatçının bu senaryoda senden duymayı beklediği ve performansı asıl belirleyen o sihirli kelime devreye giriyor: **Cardinality (Seçicilik / Tekillik Oranı)**.

Mülakatlarda bileşik indeks sırası belirlenirken altın kural şudur: **En yüksek Cardinality'ye (en çok benzersiz değere) sahip kolon, indekste her zaman en başa yazılmalıdır.**

Bu kuralı senaryomuza uygularsak:
*   **Country (Düşük Cardinality):** Dünyada topu topu ~200 ülke var. 10 milyon kullanıcının 3 milyonu 'TR' değerine sahip olabilir.
*   **Username (Yüksek Cardinality):** Neredeyse benzersizdir. 10 milyon kullanıcı içinde 'ozgur' username'ine sahip belki de sadece 1 kişi vardır.

Şimdi bu iki farklı indeks senaryosunun arka planda ne kadar disk I/O (okuma) yaptığına bakalım:

**1. Senin Önerin: `(Country, Username)` İndeksi**
1. Adım: Veritabanı B-Tree'de önce 'TR' dalına girer. Karşısına **3 milyon** kayıtlık bir blok çıkar.
2. Adım: O 3 milyon kaydın içinde dönerek 'ozgur' değerini bulmaya çalışır. 

**2. Doğru/Optimum Öneri: `(Username, Country)` İndeksi**
1. Adım: Veritabanı B-Tree'de önce 'ozgur' dalına girer. Karşısına sadece **1** kayıt çıkar.
2. Adım: O 1 kaydın Country değerinin 'TR' olup olmadığını kontrol eder. 

İkinci yöntem, aranacak veri kümesini ilk adımda milyonlardan 1'e düşürdüğü için devasa bir hız farkı yaratır.

### Mülakat Beklentisi: Trade-Off (Ödünleşim) Açıklaması
Mülakatçıya şu cevabı vermelisin: *"Sorgumuzda her iki kolon da bulunduğu için Cardinality'si yüksek olan `Username` kolonunu başa alarak `(Username, Country)` indeksini oluştururum. Bu sayede veritabanı ilk adımda veri setini neredeyse 1'e indirir. **Ancak**, sistemimizde sık sık sadece ülkeye göre arama yapan (`WHERE Country = 'TR'`) başka sorgularımız da varsa, bu sorgular `(Username, Country)` indeksini (Leftmost kuralı gereği) kullanamayacaktır. Böyle bir durumda stratejimizi ya iki ayrı indeks oluşturmak ya da uygulamanın read/write yoğunluğuna göre yeniden değerlendirmek yönünde yaparım."*


