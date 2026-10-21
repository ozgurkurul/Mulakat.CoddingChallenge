### Cross-cutting concerns
Çapraz kesen ilgiler/endişeler, yazılım geliştirmede uygulamanın birden fazla modülünü veya katmanını etkileyen, ancak uygulamanın ana iş mantığına (core business logic) doğrudan ait olmayan işlevlerdir.
Bu kavramı daha iyi anlamak için şu detaylara bakabiliriz:

* Tanım: Bir yazılımın ana fonksiyonlarını gerçekleştiren "core concerns" (çekirdek ilgiler) vardır. Örneğin bir bankacılık uygulamasında "para transferi yapmak" ana bir işlevdir. Ancak bu işlem yapılırken güvenlik kontrolü yapılması, işlemin kaydedilmesi (logging) ve hata yönetimi gibi işlemler de gerekir. İşte bu yan işlevler, uygulamanın her yerinde (farklı modüllerde) tekrarlandığı için "çapraz kesen" (cross-cutting) olarak adlandırılır.
* Temel Örnekler:
* Logging (Günlükleme): Hangi metodun ne zaman çalıştığını kaydetmek.
   * Security (Güvenlik): Kullanıcının yetkisi olup olmadığını kontrol etmek.
   * Validation (Doğrulama): Verilerin istenilen formatta olup olmadığını kontrol etmek.
   * Caching (Önbellekleme): Sık kullanılan verileri hızlı erişim için saklamak.
   * Exception Handling (Hata Yönetimi): Oluşabilecek hataları merkezi bir noktadan yönetmek.
   * Transaction Management: Veritabanı işlemlerinin bütünlüğünü sağlamak.
* Neden Önemlidir? Eğer bu yan işlevleri her metoda manuel olarak yazarsanız, kodunuzda çok fazla tekrar oluşur ("code scattering") ve ana iş mantığınız karmaşıklaşır ("code tangling"). Bu durum kodun bakımını zorlaştırır.
* Çözüm (AOP): Cross-cutting concern'leri ana koddan ayırıp bağımsız bir yapı olarak yönetmek için Aspect-Oriented Programming (AOP) tekniği kullanılır. AOP sayesinde bu işlevler merkezi bir yerde tanımlanır ve ihtiyaç duyulan noktalara (join points) otomatik olarak "enjekte" edilir.