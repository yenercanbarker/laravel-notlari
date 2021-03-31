## İstek Yaşam Döngüsü

* Laravel uygulamasına gelen bütün istekler **public/index.php** dosyasına gider.
  * index.php pek fazla kod içermez, bu dosya diğer Laravel Framework dosyalarının yüklenme noktasıdır.
  * index.php dosyası, Composer tarafından oluşturulan yükleyici yükler ve ardından **bootstrap/app** içinden
  Laravel uygulamasının bir örneğini alır.
  
* Sonrasında istek, gelen isteğin türüne bağlı olarak HTTP veya Console çekirdeğine gönderilir. **app/Http/Kernel** klasörü
HTTP kernel'ın bulunduğu klasördür.
  * HTTP kernel, **Illuminate\Foundation\Http\Kernel** sınıfından extend edilmiştir, bu sınıf istek gerçekleştirilmeden
  önce çalışacak dizi halinde verilmiş **bootstrappers** değerlerini içerir.
    * Bootstrappers, **Hata Ayıklama, Loglama İşlemleri, Uygulama Ortamının Algılanması** ve istek gerçekleştirilmeden
    yapılması gereken işlemleri yapar.
  * HTTP kernel, isteklerin gerçekleşmeden önce geçmeleri gerektiği HTTP middleware'lerin listesini barındırır.
  * HTTP kernel **handle** metodu, **Request** alır ve **Response** döndürür.

* **Servis sağlayıcılar**, **config/app.php** dosyası içerisinde **providers** dizisinde bulunurlar. Laravel bu dizideki
servis sağlayıcıların instance'ını oluşturacaktır. Provider'lar oluştuktan sonra, **register** methodları çalışır. Bu metodun
çalıştıktan sonra, **boot** metodu çalışacaktır.
  * Servis sağlayıcılar, **database**, **queue**, **validation** ve **routing** component'lerini ön yüklemekle sorumludur.

* **App\Providers\RouteServiceProvider**, **routes** klasörü içerisinde bulunan route dosyalarını yükler.
  * Uygulama önyüklendikten ve bütün servis sağlayıcılar register olduktan sonra, gelen istek router'lara yönlendirir.
  * Router'lar yönlendirme işlemlerini yapar.

* Router'lar yönlendirmeleri yapmadan önce ara katman dediğimiz **Middeware**'ler devreye girer. Burada çeşitli işlemler
sorgulamalar vb. işlemleri yapabiliriz.
  * İstek bu ara katmanlardan geçebilirse, router'lar response olarak istediğimiz verileri döndürür.

* Son olarak, response middleware ile tekrar geriye döner ve HTTP kernel'ın *handle* methodu response objesini index.php'ye
* döndürür. index.php *send* methodunu çalıştırır, bu method response içeriğini kullanıcının browser'ına gönderir.

Bu not Laravel'in resmi dökümantasyonundaki [Request Lifecycle](https://laravel.com/docs/8.x/lifecycle) sayfasındaki veriler kullanılarak hazırlanmıştır.
