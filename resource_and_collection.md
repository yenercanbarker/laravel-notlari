## Resource & Collection

* Resource, modeldeki verileri api'den döndüreceğimiz json formatındaki veriye istediğimiz formatta yazdırmak için kullanılır.
  * Her model içerisinde veriyi formatlamak yerine resource ile bir kere formatlamak ve istediğimiz yerde kullanmak mantıklı.
  * Ve resource içinde bir yeri değiştirdiğimizde return ettiğimiz yerlerde o formata göre veri gönderilir.

* Bir **resource** oluşturmak için **make:resource** artisan komutunu çalıştırabiliriz. Default olarak resource'lar **app/Http/Resources** klasöründe bulunur.
  * Resource'lar **Illuminate\Htto\Resources\Json\JsonResource** sınıfını kullanır.

```
php artisan make:resource UserResource
```

* Resource ile modeldeki veriyi json'a formatlayarak return edebiliyoruz, ek veri eklemek veya başka bilgi eklemek için resource ile birlikte **collection** kullanıyoruz.
 
* Collection oluşturmak için resource oluşturma komutunun yanına **--collection-- ekleyebiliriz.
  * Veya oluşturacağımız resource'a isim olarak UserResource yerine **UserCollection** diyebiliriz.
  * Collection resources **Illuminate\Http\Resources\Json\ResourceCollection** sınıfını kullanır.

```
php artisan make:resource User --collection 

php artisan make:resource UserCollection
```

* Bir resource json formatında veri dönecek bir model'i formatlamak için kullanılır.

```php
namespace App\Http\Resources;

use Illuminate\Http\Resources\Json\JsonResource;

class UserResource extends JsonResource
{
  public function toArray($request) 
  {
    return [
      'id' => $this->id,
      'name' => $this->name,
      'created_at' => $this->created_at,
      'updated_at' => $this->updated_at
    ];
  }
}
```

* Her resource sınıfı array şeklinde belirtilen yapıyla return edildiğinde Json formatında veri döndürecek **toArray** metodunu barındırır.

* Resource içerisinde model'imizdeki verileri **$this** ile kullanabiliriz.

* Resource'u tanımladıktan sonra route veya controller'da kullanabiliriz.

* Resource constructor olarak model'i kabul eder.

```php
use App\Http\Resources\UserResource;
use App\Models\User;

Route::get('/user/{id}', function ($id) {
  return new UserResource(User::findOrFail($id));
});
```

* Birden fazla resource döneceksek veya sayfalanmış resource yapımız varsa **collection** metodunu kullanabiliriz.
  * Resource'un collection metodu

```php
use App\Http\Resources\UserResource;
use App\Models\User;

Route::get('/users', function ($id) {
  return new UserCollection(User::all());
});
```

* Collection ile ResourceCollection oluşturduktan sonra, **data** bölümüne collection'ı verebiliriz.

```php
namespace App\Http\Resources;

use Illuminate\Http\Resources\Json\ResourceCollection;

class UserCollection extends ResourceCollection
{
  /**
   * Transform the resource collection into an array
   *
   * @param  \Illuminate\Http\Request  $request
   * @return array
   */
   public function toArray($request)
   {
      return [
        'data' => $this->collection,
        'links' => [
          'self' => 'link-value',
        ],
     ];
   }
}
```

* Bir route resource collection döndürürken Laravel collection'ın anahtarlarını numara göresine sıralandırır.
  * **preserveKeys** özelliğini kullanarak collection'ın anahtarını değiştirebiliriz.
  * **public $preserveKeys = true** ile bu özelliği aktif hale getirebiliriz. 

* Bu özelliği aktif edersek veri anahtarı değişir.

```php
use App\Http\Resources\UserResource;
use App\Models\User;

Route::get('/users', function () {
  return UserResource::collection(User::all()->keyBy->id);
});
```

* **$this->collection** özelliği Collection içerisindeki her bir resource için mapleme işlemi yapar.

* Sonu Collection ile biten bir sınıf aynı isimde sonu Resource ile biten ile biten resource ile maplenir.
  * Örneğin **UserCollection** bize **UserResource** resource instancelarını mapler, **$collects** ile resource collection'ı değiştirebiliriz.

* Json formatında göndereceğimiz veriyi bir de ana dizi içerisine almak için header gibi **$wrap** özelliğini kullanabiliriz.
  * Default olarak collection resource'u return ettiğimizde **data** ile wraplenir, yani data içerisinde dizi şeklinde resourcelar bulunur.

```json
{
  "data": [
    {
      "id": 1,
      "name": "Eladio Schroeder Sr.",
      "email": "therese28@example.com",
    },
    {
      "id": 2,
      "name": "Liliana Mayert",
      "email": "evandervort@example.com",
    }
  ]
}
```

* data yerine farklı bir wrapper kullanmak için $wrap attribute'una istediğimiz değeri verebiliriz.

```php
public static $wrap = 'user'
```

* Wrapping kullanmak istemiyorsak ve default datayı da istemiyorsak **Illuminate\Http\Resources\Json\JsonResource** sınıfındaki **withoutWrapping** metodunu kullanabiliriz.
  * AppServiceProvider veya kullandığımız başka service provider içerisinde **boot** metodunda çalıştırabiliriz.
  * Ekstra dahil ettiğimiz key'i kaldırmaz.
  
```php
public function boot()
{
  JsonResource::withoutWrapping();
}
```

* Sayfalama işlemi olan Resource Collection'larda wrapping'i kaldırsak bile data kullanılır çünkü paginated response'lar her zaman **meta** ve **links** keylerini
  barındırır.
  
* Collection metodunun instance'ına constructor'una **paginate** kullanarak sayfalanmış bir şekilde json veri döndürebiliriz.

* Resource'larda veri döndürürken bazen bir koşula bağlı olarak veri göndermemiz gerekebilir. **when** metodunu kullanarak bir koşul ekleyebiliriz.
  * When metodu ikinci parametre olarak fonksiyon alabilir, içindeki dönecek değerle true/false'u karşılaştırır.

```php
use Illuminate\Support\Facades\Auth;

public function toArray($request) 
{
 return [
  'secret' => $this->when(Auth::user()->isAdmin(), 'secret-value')
 ]
}
```

* **mergeWhen** metodunu kullanarak ve ikinci parametre olarak döndüreceğimiz değerleri dizi şeklinde ekleyebiliriz.
  * İlk parametredeki koşul true dönerse ikinci parametredeki değerler de eklenerek veri gönderilir.

```php
public function toArray($request) {
  $this->mergeWhen(Auth::user()->isAdmin, [
    'first-secret' => 'value',
    'second-secret' => 'value'
  ])
}
```

* İlişkili veriler döndüreceğimiz zaman **whenLoaded** metodunu kullanabiliriz.
  *  Bu metod gereksiz ilişki yüklemelerinden kaçınmamızı sağlar.
  *  Relation'ın kendisini değil ismini kullanarak çalışır.
  *  Aşağıdaki örnekte eğer relation yüklenmediyse resource responce gönderilmeden posts key'i içinden kaldırılır.
  
```php
public function toArray($request)
{
  return [
    'id' => $this->id,
    'name' => $this->name,
    'email' => $this->email,
    'posts' => PostResource::collection($this->whenLoaded('posts')),
    'created_at' => $this->created_at,
    'updated_at' => $this->updated_at,
  ];
}
```

* **Pivot** tablolar için **whenPivotLoaded** metodu kullanılabilir.
  * İlk parametre olarak pivot tablonun adını alır.
  * İkinci parametre olarak pivot tablodan veri geldiyse kullanılabilecek olan veridir.

```php
public function toArray($request)
{
  return [
    'id' => $this->id,
    'name' => $this->name,
    'expires_at' => $this->whenPivotLoaded('role_user', function () {
      return $this->pivot->expires_at;
    }),
  ];
}
```

* whenPivotLoaded ilk parametre olarak pivot tablodaki model'i new ile alabilir.

* whenPivotLoadedAs metodu ile pivot key'i yerine tablonun key'ini kullanabiliriz.

* Bazı json api standartları meta data verisinin de eklenilmesini ister.
  * Genelde **links**, **meta** vb.

* ResourceCollection  içerisinde **with** metodunu kullanarak **Top Level Meta Data** oluşturabiliriz.
  * Bu resource response edilirken json'a eklenir.

* Veya return edeceğimiz zaman constructor'a eklemek için **additional** metodunu ekleyebiliriz.
  * Array ile key value şeklinde verileri ek verileri kullanabiliriz. Meta key'i ile meta data gönderebiliriz.

* Bazı durumlarda gönderilecek HTTP response gönderilmeden önce customize edilmelidir.

* Resource döndüreceğimiz kısımda **response()** metodunu zincirleyerek ve **header** metodunu zincirleyerek ve key value şeklinde header ekleyerek gönderebiliriz.
  * Veya Resource içerisinde **withResponse** metodunu kullnarak response dönmeden önce formatlamasını sağlayabiliriz.
  * $request ve $response olarak iki parametre alır.

```php
public function withResponse($request, $response) {
  $response->header('X-Value', 'True')
}
```

Bu not [Laravel'in Orjinal Dökümantasyonu](https://laravel.com/docs/8.x)'ndaki [Eloquent: API Resources](https://laravel.com/docs/8.x/eloquent-resources) bölümündeki 
veriler kullanılarak hazırlanmıştır.


