## CSRF Protection / CSRF Token

* Gelen cross-site requestler, doğrulanmamış kullanıcıların yetkisiz yaptığı işlemler gibi zararlı durumlar içerebilir. Bu yüzden Laravel **CSRF (Cross-site Request Forgery)**
  ile uygulamayı güvenli hale getirir.

* Laravel her kullanıcı oturumu için bir **CSRF Token** oluşturur. Bu token uygulamadan istek yapan kullanıcıyı doğrulamak için kullanılır. Oturumlar her 
sıfırlandığında csrf token içeriği değiştiği için güvenlik açıklarını ortadan kaldırır.

* **csrf_token** helper fonksiyonunu kullanarak oturumun tokenine erişebiliriz.

```php
Route::get('/token', function (Request $request) {
  $token = $request->session()->token();
  $token = csrf_token();
});
```
* View içerisinde form oluşturduğumuzda **@csrf** ile **hidden** bir csrf input oluşturabiliriz, bu input'un value'si tokendir.

```blade
<form method="POST" action="">
  @csrf
</form>
```

* Kullanıcı bir istekte bulunduğunda, route'un bağlı olduğu **web** middleware'i default olarak çalışır.
  * **App\Http\Middleware\VerifyCsrfToken** middleware'i otomatik olarak çalışır. Formdan gelen csrf token ile oturumun csrf tokenlerini karşılaştırır ve
  tokenler uyuşursa doğrulanmış bir kullanıcıyla işlem yaptığımızı biliriz.
  
* Bazen proje dışından bir csrf token bekleyebiliriz. (Stripe gibi ödeme işlemlerinde)

* **routes/web.php** içerisindeki bütün route'lar web middleware'ine bağlıdır, bu tarz dışardan gelen istekleri web içine yazmamalıyız.
**App\Providers\RouteServiceProvider** içinde VerifyCsrfToken middleware'inde **$except** property'si içerisinde url'leri ekleyebiliriz.

* **App\Http\Middleware\VerifyCsrfToken** middleware'i csrf token'i yalnızca post parametresi olarak almak zorunda değildir. Html meta etiketi olarak token'i
tanıtıp, formu göndereceğimiz yere header parametresi olarak gönderebiliriz.

```blade
<meta name="csrf-token" content="{{ csrf_token() }}" />
```

```js
$.ajaxSetup({
    headers: {
        'X-CSRF-TOKEN': $('meta[name="csrf-token"]').attr('content')
    }
});
```

Bu not [Laravel](https://laravel.com/)'in orjinal dökümantasyonundaki [CSRF Token](https://laravel.com/docs/8.x/csrf) sayfasındaki bilgiler kullanılarak 
hazırlanmıştır.


