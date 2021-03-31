## Routes / Yönlendiriciler

* Basit bir Laravel Route'u, URL i alır ve yönlendirme işlemlerini gerçekleştirir.

* Route'lar **routes** klasörü içerisinde bulunurlar. Bu dosyalar uygulama tarafından otomatik olarak yüklenir **App\Providers\RouteServiceProvider**.

* **routes/web.php** klasörü, web tarafında kullanacağımız route'ların tanımlandığı klasördür. Bu routelar **web** Middleware'ine bağlıdır.
  * Web middleware'i **session state** ve **CSRF** korumasını barındırır.

* **routes/api.php** klasörü, **api** Middleware'ine bağlıdır.

* Çoğu uygulamalarda route tanımlamalarına **routes/web.php** dosyasından başlanır. Bu klasörde tanımlanan route'lar web Url'inden
gelen isteklere erişebilir.

```php
use App\Http\Controllers\UserController;

Route::get('/user', [UserController::class, 'index']);
```

* **routes/api.php** dosyası **RouteServiceProvider**'la iç içe geçmiştir. Bu grup otomatik olarak **/api** prefix'i barındırır.
Dolayısıyla her route'da **/api** kullanmamıza gerek kalmaz.

* Laravel Router'da Kullanabileceğimiz Methodlar :
  * get
  * post
  * put
  * patch
  * delete
  * options

* Bazı durumlarda bir url'e birden farklı isteklerle erişim gelmesini bekleyebiliriz. Bunun için **match** veya **any** kullanılır.
  * Matc bizim seçtiğimiz methodlara izin verirken, any gelen bütün methodlara erişim sağlamamıza izin verir.

```php
Route::match(['get', 'post'], '/', function(){});

Route::any('/', function(){});
```
* **post, put, patch** ve **delete** isteklerine izin veren **web.php** içerisindeki route'lar **CSRF token** beklerler, eğer
token gönderilmezse istekler geri çevirilecektir. Formlara **@csrf** eklememiz gerekir.

* Eğer kullanıcıyı direkt bir Url'ye gönderecek bir route tanımlayacaksak **Route::redirect** metodunu kullanabiliriz. Bu method
sayesinde full route veya controller olmadan yönlendirme işlemini gerçekleştirebiliriz.

```php
Route::redirect('/buradan', '/buraya')
```

* **Route::redirect** 302 statü kodu döndürür. Üçüncü parametre göndererek bu kodu değiştirebiliriz.

```php
Route::redirect('/buradan', '/buraya', 301);
```

* **Route::permanentRedirect** metodu 301 statü kodu döndürür.

* Eğer direkt olarak **view** döndürmek istiyorsak, **Route::view** methodunu kullanabiliriz. Bu method da redirect gibi kısayoldur.
* View methodu ilk parametre olarak url, ikinci parametre olarak yönlendirilecek view dosyası, üçüncü parametre olarak view'e gönderilecek
verileri alır.

```php
Route::view('/welcome', 'welcome', ['name' => 'Matris']);
```

* Url üzerinden parametre almak için routelarda **{parameter}** bu yazımı kullanabiliriz. ? koyarak parametreyi isteğe bağlı hale
getirebiliriz.

```php
Route::get('/user/{id}', function($id) {
   return $id;
});
```

* **where** method zincirlemesini kullanarak **Regex** desenleri ile, parametrenin alabileceği veya alması zorunlu olan değerleri
belirtebiliriz.
  * Hazır patternleri de kullanabiliriz, örneğin :
    * ->whereNumber('id')
    * ->whereAlpha('name')
    * ->whereAlphaNumeric('name')
    * ->whereUuid('id')

```php
Route::get('/user/{name}', function ($name) {

})->where('name', '[A-Za-z]+');
```

* Eğer regex patternleri'ni bir çok yerde kullanıyorsak **Route::pattern** methodunu **App\Providers\RouteServiceProvider** class'ı
içerisindeki **boot** methodunda tanımlayabiliriz. Böylece bütün routeların bu koşulu sağlaması gerekir.

* **->where('search', '.*')** ile parametere içinde / kullanmamıza izin verebiliriz.

* Route'lara isim vermek için **name** methodu kullanılır.

```php
Route::get('/user/profile', function () {

})->name('profile');
```

* **redirect()->route('route_name')** ile direkt route'lara yönlendirme yapabiliriz.
  * Parametre eklemek için : **route('profile', ['id'=> 1]);

* **named** methodu ile bir isteğin o nameden gelip gelmediğini bulabiliriz, true/false döndürür. 

* Route'ları gruplamak için **->group()** kullanılır.
* Route'lara middleware eklemek için **->middleware** kullanılır.

```php
Route::middleware(['first', 'second'])->group(function () {
   Route::get('/', function() {
   });
});
```

* **domain** methodu istedğin geldiği subdomain'e göre yönlendirmeleri gerçekleştirebiliriz.

* **prefix** methodu ile belirli bir sabit değerden sonra gelecek bütün isteklere tek tek eklemek zorunda kalmayız.

```php
Route::prefix('admin')->group(function () {
  Route::get('/users', function () {
  });
})
```

* **prefix** yerine **name** verip gruplama yaparsak, bu name bütün route'ların başına eklenir.

* **missing** metodu ile bulunmayan route'ların nereye yönlendirme yapacağını değiştirebiliriz, default olarak 404 sayfası döner.

* **fallback** metodu ile, eğer tanımladığımız hiç bir route isteğe karşılık gelmiyorsa çalışacak fonksiyonu yani yapılacak
yönlendirmeyi belirleyebiliriz.

* **current**, **currentRouteName** ve **currentRouteAction** metoduyla gelen isteklerin nereden geldiği hakkında bilgi edinebiliriz.

* **php artisan route:cache** komutu ile route'ları cache'e alabiliriz. Bu bütün route'ların yüklenme süresini azaltır.

* **php artisan rotute:clear** komutu ile cache'deki route'ları temizleyebiliriz.










