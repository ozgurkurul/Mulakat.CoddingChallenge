Harika fikir. Madem Scorp'un o "milyonlarca kullanıcı" ve "yüksek ölçek" vurgusu üzerinden gidiyoruz, mülakatta karşına çıkma ihtimali çok yüksek olan klasik bir mutabakat (reconciliation) senaryosu üzerinden bir pratik yapalım. 

Senin `program.cs`'te yazdığın bakiye ve ödeme sisteminin bir uzantısı olduğunu hayal et.

### Senaryo: Gün Sonu Ödeme Mutabakatı
Scorp'un muhasebe servisi için bir eşleştirme metodu yazman isteniyor. Gün sonunda elinde iki farklı liste var:
1.  **`scorpIslemleri`**: Scorp'un kendi veritabanından çektiğimiz, o gün işlem gören `StreamerId`'lerin listesi. (Örn: 1 Milyon kayıt)
2.  **`bankaIslemleri`**: Ödeme sağlayıcısından (bankadan) gelen, o gün başarıyla para transferi yapılan `StreamerId`'lerin listesi. (Örn: 1 Milyon kayıt)

Senden istenen görev: **Sadece her iki listede de bulunan (ortak olan) yayıncıların ID'lerini döndüren bir metot yazmak.**

Aşağıda bir junior geliştiricinin yazabileceği, ilk akla gelen "saf" (naive) çözümü paylaşıyorum. Bu kod lokal ortamda 100 kayıtta harika çalışır ama canlıya çıkıp 1'er milyon kayıt geldiğinde sistemi kilitler.

```csharp
public List<string> OrtakYayinculariBul(List<string> scorpIslemleri, List<string> bankaIslemleri)
{
    var ortaklar = new List<string>();
    
    // Scorp listesini dönüyoruz
    foreach (var scorpId in scorpIslemleri)
    {
        // Banka listesinde bu ID var mı diye bakıyoruz
        if (bankaIslemleri.Contains(scorpId)) 
        {
            ortaklar.Add(scorpId);
        }
    }
    
    return ortaklar;
}
```

### Şimdi Sıra Sende:

1.  Yukarıdaki "kötü" kodun Time Complexity'si (Zaman Karmaşıklığı) Big O notasyonunda nedir ve kod neden canlı sistemde çöker? *(İpucu: `List<T>.Contains()` metodunun arka planda ne yaptığına dikkat et).*
2.  "Zamanı hızlandırmak için hafızadan (RAM) feda et" kuralını uygulayarak, bu kodu $O(N)$ (Doğrusal Zaman) seviyesine nasıl çekersin? Hangi .NET veri yapısını kullanmak saniyeler süren bu işlemi milisaniyelere düşürür?</T>



### Çözüm:
1. Zaman karmaşıklığı: $O(N^2)$
2. $O(N)$ Çözüm:
```csharp
public List<string> OrtakYayinculariBul(List<string> scorpIslemleri, List<string> bankaIslemleri)
{
    var ortaklar = new List<string>();
    
    // 1. Banka işlemlerini Hash tabanlı bir yapıya alıyoruz (Space Trade-off)
    var bankaIslemleriSet = new HashSet<string>(bankaIslemleri);
    
    // 2. Scorp listesini dönüyoruz (O(N))
    foreach (var scorpId in scorpIslemleri)
    {
        // 3. HashSet.Contains() O(1) sürede çalışır!
        if (bankaIslemleriSet.Contains(scorpId)) 
        {
            ortaklar.Add(scorpId);
        }
    }
    
    return ortaklar;
}
```
 `List<T>.Contains()` metodu arka planda diziyi baştan sona tek tek gezer (linear search). Yani 1 milyonluk ilk listeyi dönerken, her bir eleman için 1 milyonluk ikinci listeyi de taramaya çalışır. Bu da $1.000.000 \times 1.000.000$ işlem demek olur ki, sistemin CPU'su bu $O(N^2)$ yükün altında ezilir.


