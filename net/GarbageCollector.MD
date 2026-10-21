### Garbage Collector "Gens" (Generations - Nesiller)
Garbage Collector "Gens" (Generations - Nesiller), bellek yönetimini optimize etmek için nesnelerin ömürlerine göre farklı kategorilere ayrılmasıdır. Çöp toplayıcı, tüm belleği sürekli taramak yerine, nesneleri yaşlarına göre gruplandırarak (nesillere ayırarak) sadece gerekli alanlarda temizlik yapar.
Bu sistemin temel dayanağı "Weak Generational Hypothesis" (Zayıf Nesil Hipotezi) denilen, "yeni oluşturulan nesnelerin çoğu çok kısa sürede ölür" prensibidir.
## Nesil Yapısı (Generations)
Genellikle üç ana nesil bulunur:

* Generation 0 (Gen 0 - Genç Nesil): Yeni oluşturulan tüm nesneler burada başlar. En sık temizlik yapılan bölgedir çünkü nesnelerin çoğu burada "çöp" olur.
* Generation 1 (Gen 1 - Ara Nesil): Gen 0 temizliğinden sağ kurtulan (hâlâ kullanılan) nesneler buraya aktarılır. Burası Gen 0 ile Gen 2 arasında bir tampon görevi görür.
* Generation 2 (Gen 2 - Yaşlı Nesil): Uzun süre hayatta kalan nesneler buraya taşınır (örneğin statik veriler veya uygulama boyunca yaşayan nesneler). Buradaki temizlik işlemi en ağır olanıdır ve en nadir burada temizlik yapılır.


## Neden Nesiller Kullanılır?

* Performans: Belleğin tamamını taramak yerine sadece Gen 0'ı taramak çok daha hızlıdır.
* Verimlilik: Kısa ömürlü nesneler hızla temizlenirken, uzun ömürlü nesnelerle işlemci boşuna meşgul edilmez.
* Düşük Gecikme: Küçük temizlik işlemleri (Minor GC) uygulamayı neredeyse hiç durdurmazken, büyük temizlikler (Major/Full GC) daha nadir gerçekleşir.
