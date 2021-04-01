## Middleware / Ara Katman

* **Middleware** gelen HTTP isteklerini inceleme ve filtreleme özelliklerini sağlar.
  * Örnek olarak Laravel uygulamaya giriş yapan kullanıcıları doğurlamak için bir middleware içerir.

* Authenticationdan farklı olarak istediğimiz işleri de yaptırabiliriz. Örneğin bir loglama middleware'i oluşturup uygulamaya gelen istekleri loglayabiliriz.

* Middleware'ler **app/Http/Middleware** klasörü içerisinde bulunurlar.

* Middleware tanımlamak için : **make::middleware** komutunu kullanırız.
  * php artisan make:middleware CheckTokenIsValid

```php
public function handle ($request, Closure $next) {
   if($request->input('token') !== 'token1') {
      return redirect('home');
   }

   return $next($request);
}
```

* $next callback'i ile $request'i kullanarak middleware'den sonra gidilecek yere yönlendirme yapılır. 

* Middleware'ler before ve after olarak iki farklı şekilde kullanılabilir.
  * before : İstek yerine getirilmeden önce middleware'i çalıştırır.
  * after : istek yerine getirildikten sonra middleware'i çalıştırır.
  * Bu tanımlamalar middleware'in dosyasındaki class ismine **BeforeMiddleware** veya **AfterMiddleware** verilmesiyle akfitfleştirilebilir.

* Eğer bir middleware uygulamaya her HTTP isteği geldiğinde çalışacaksa, **app/Http/Kernel.php** dosyası içerisinde $middleware property olarak tanımlanmalıdır. 

* Oluşturduğumuz middleware'i route'larda kullanmak için **app/Http/Kernel.php** dosyasına eklememiz gerekir. Default olarak $rotueMiddleware property'si
içerisinde middlewareler bulunmaktadır.

```php
protected $rotueMiddleware = [
   'auth' => \App\Http\Middleware\Authenticate::class, ...
];
```

* Oluşturduğumuz middleware'i kullanmak için :

```php
Route::get('/profile', function () {

})->middleware('auth');
```
* Birden çok middleware eklemek istersek, dizi şeklinde parametreleri verebiliriz.

```php
Route::get('/profile', function () {

})->middleware(['auth', 'second']);
```

* Key yerine direkt class ismini de verebiliriz. 

* Eğer route'ları bir grubun içinde tanımlayıp middleware eklersek, middleware'in etki etmemesini istediğimiz route için ->withoutMiddleware('isim'); kullanabiliriz.

* Bazen bir middleware grubunun içine middleware'leri ekleyerek bir middleware içinde birden çok middleware'i çalıştırabliriz. 
  * Bunun için $middlewareGroups özelliğini kullanabiliriz. web ve api, App\Providers\RouteServiceProvider içerisinde uygulamaya zaten eklenmiştir.

* Nadir olarak middleware'leri sıraya sokmamız gerekebilir. Bunun için $middlewarePriority ile sırayla middleware'leri yazabiliriz.

* Middleware'e ek parametre verilebilir. Bunun için handle fonksiyonuna parametre veririz ve rotuelara middleware tanımlarken, parametreyi de dahil ederiz.

```php
public function handle ($request, Closure $next, $role) {
  if (! $request->user()->hasRole($role)) {
  
  }
  
  return $next($request);
}
```

* Routelarda parametre vermek için : kullanarak middleware'e parametre ekleyebiliriz.

```php
Route::put('/post/{id}', function ($id) {

})->middleware('role:editor');
```

Bu not [Laravel 8](https://laravel.com/)'in orjinal dökümantasyonundaki [Middleware](https://laravel.com/docs/8.x/middleware) bölümündeki bilgilerden yararlanılarak hazırlanmıştır.

