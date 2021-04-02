## Controllers 

* Bütün işleri routelarda yapmak yerine, routelarda sadece yönlendirme işlemlini yapıp, mantıksal işleri yapabilmemiz için **Controller** adı verilen bir yapı
oluşturulmuştur.

* Controller'lar bir class'a bağlı olan işleri gerçekleştirmek için oluşturulan sınıflardır.
  * İsimlendirilirken DegiskenController olarak tanımlanırlar.
  * Varsayılan klasör yapısında **App\Http\Controllers** içerisinde bulunurlar.
  
* Bir sınıfın Controller olabilmesi için **App\Http\Controllers\Controller** sınıfından türetilmesi zorunlu değildir ama bu sefer middleware ve authorize methodlarından
faydalanamayız.

* Routelarda gelen request'e göre controllerlara yönlendirme işlemi yapmak için aşağıdaki ifade kullanılır.
  * /user/1 şeklinde bir istek gelirse, UserController'ın show metodu çalıştırılır.

```php
Route::get('/user/{id}', [UserController::class, 'show']);
```

* Eğer Controller tek bir işlem içeriyorsa ekstra olarak method belirtmeden, **__invoke()** methodunu kullanarak controller'a yönlendirilme yapıldığında
methodun çalışmasını sağlayabiliriz.

```php
public function ___invoke() {

}
```
```php
Route::get('/', HomeController::class);
```

* Aşağıdaki artisan komutu ile invokable controller oluşturabiliriz :  

```
php artisan make:controller HomeController --invokable
```

* Kullanacağımız model'leri **resource** olarak yani **CRUD işlemlerini barındıran** sınıflar olarak tanımlıyorsak, **resource** route'unu kullanabiliriz.
  * CRUD işlemleri için teker teker route oluşturup, method tanımlamalarını yapmamıza gerek kalmaz, resource o işi bizim için yapar.
  
```
php artisan make:controller UserController --resource 
```
* Yukarıdaki gibi bir Controller oluşturursak, Controller class'ının içerisine otomatik olarak **index, create, store, show, edit, update** ve **destroy** methodları
oluşturulur. Bu Controller'ı route'a bağlamak için : 

```php
Route::resource('photos', PhotoController::class);
```

* resource Route'u Controller'daki methodlara karşılık olan route'ları otomatik olarak barındırır.

| Verb      | URI                  | Action  | Route Name     |
|-----------|----------------------|---------|----------------|
| GET       | /photos              | index   | photos.index   |
| GET       | /photos/create       | create  | photos.create  |
| POST      | /photos              | store   | photos.store   |
| GET       | /photos/{photo}      | show    | photos.show    |
| GET       | /photos/{photo}/edit | edit    | photos.edit    |
| PUT/PATCH | /photos/{photo}      | update  | photos.update  |
| DELETE    | /photos/{photo}      | destroy | photos.destroy |

* Resource Route'larda kullandığımız model bulunamazsa 404 hatası döndürür.
  * Model bulunamadığı zaman **missing()** methodu çalışır.

```php
Route::resource('photos', PhotoController::class)->missing(function (Request $request) {
  return Redirect::route('photos.index');
})
```

* Resource olarak tanımladığımız model'lerde istemediğimiz aksiyonlara izin vermemek için route içerisinde sınırlandırabiliriz.

```php
Route::resource('photos', PhotoController::class)->except([
    'create', 'store', 'update', 'destroy'
]);
```

* API'lar için resource route'u oluşturacağımız zaman, **create** ve **edit** routelarının olmaması için route'u **apiResource** olarak oluşturabiliriz.

```php
Route::apiResource('photos', PhotoController::class);
```

```
php artisan make:controller PhotoController --api
```

* İç içe resource route kullanmak istiyorsak . ile birleştirebiliriz.
  * Aşağıdaki url'e erişmek için => /photos/{photo}/comments/{comment}

```php
Route::resource('photos.comments', PhotoCommentController::class);
```

* İç içe resource routelarda çoklu route kullanmak için : 

```php
Route::resource('photos.comments', CommentController::class)->shallow();
```

* Resource route'lara isim verebiliriz.

```php
Route::resource('photos', PhotoController::class)->names([
    'create' => 'photos.build'
]);
```

* Resource route'lara parametre ekleyebiliriz.

```php
Route::resource('users', AdminUserController::class)->parameters([
    'users' => 'admin_user'
]);
```

* Resource route'lara scoped verebiliriz.
  * /photos/{photo}/comments/{comment:slug}
  * Scoped özelliği, parent ve child modellerin bağlı olduğunu belirtir.

```php
Route::resource('photos.comments', PhotoCommentController::class)->scoped([
    'comment' => 'slug',
]);
```

* Resource route'larda default verilen ingilizce route isimlerini değiştirebiliriz.

```php
Route::resourceVerbs([
        'create' => 'crear',
        'edit' => 'editar',
    ]);
```

* Resource dışındaki bir route'u aynı controller'a yönlendirmek istiyorsak, controller ve metod verebiliriz, bu route'u Resource ismiyle kullanamayız.

```php
Route::get('/photos/popular', [PhotoController::class, 'popular']);
Route::resource('photos', PhotoController::class);
```

Bu not [Laravel 8](https://laravel.com/)'in orjinal dökümantasyonundaki [Controllers](https://laravel.com/docs/8.x/controllers) bölümündeki veriler kullanılarak hazırlanmıştır.









