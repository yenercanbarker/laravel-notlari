## Views
 
* View dosyaları **.blade.php** formatında **resources** klasörü altında **views** klasörü içerisinde bulunurlar.

* Oluşturduğumuz view'ı görüntülemek için ;

```php
Route::get('/', function () {
    return view('greeting', ['name' => 'James']);
}); 

// böyle bir kullanımı da mevcuttur.

use Illuminate\Support\Facades\View;

return View::make('greeting', ['name' => 'James']);
```

* İç içe dosyalardaki view'ları çağırmak için view ismi ve dosyalar arasına **.** eklenir.
  * Resources içerisindeki views klasöründeki admin klasörünün içine girer ve profile.blade.php view'ını döndürür.

```php
return view('admin.profile');
```

* **View** Facade'ının **first** methodunu kullanarak, dizi şeklinde verdiğimiz viewlardan, mevcut olan ilkini çağırabiliriz.

```php
use Illuminate\Support\Facades\View;

return View::first(['custom.admin', 'admin'], $data);
```

* Bir view'ın mevcut olup olmadığını kontrol etmek için : 

```php
if (View::exists('emails.customer')) {
    //
}
```

* View'a veri göndermek için ;
  * **with** methodu zincirlenebilir.

```php
return view('greetings', ['name' => 'Victoria']);

// veya 

return view('greeting')
            ->with('name', 'Victoria')
            ->with('occupation', 'Astronaut');
```

* Bir veriyi bütün view dosyalarında görüntülemek istiyorsak, **App\Providers\AppServiceProvider** klasöründeki **boot** methodu içerisine;
  * View Facade'ının **share** methodunu kullanarak veriyi kaydedebiliriz.

```php
View::share('key', 'value');
```

* View dosyalarını optimize etmek için **cache**'e aktarabiliriz, php artisan yardımıyla bu işlemi gerçekleştirebiliriz.
  * Cache'i temizlemek için de **clear** yöntemini kullanabiliriz.

```php
php artisan view:cache

php artisan view:clear
```

Bu ders notu, [Laravel 8](https://laravel.com/docs/8.x/)'in orjinal dökümantasyonundaki [Views](https://laravel.com/docs/8.x/views) bölümündeki veriler
kullanılarak oluşturulmuştur.

