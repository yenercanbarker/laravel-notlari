## Eloquent : Getting Started

* **Eloquent**, bir **ORM**(Object Relational Mapper)'dır. Eloquent yapısını kullanıyorsak, her bir veritabanı tablosu aynı zamanda bir "Model"dir.

* **Model** sayesinde veritabanımızdaki tablo ile etkileşime geçebiliriz. Eloquent sayesinde crud işlemlerini
  Model üzerinden gerçekleştirebiliyoruz.
  
* Eloquent yapısını veya database ile ilgili diğer işlemlerden önce **config/database.php** dosyamızda kendi veritabanı
  ayarlarımızı yapmamız gerekiyor.
  
* Eloquent Model'ler **app\Models** klasöründe bulunur ve **Illumibate\Database\Eloquent\Model** sınıfından extend edilmiştir.
  * **make:model** Artisan komutunu kullanarak bir Model oluşturabiliriz.

```
php artisan make:model Product
```

* Veritabanı tabloları ile ilgili işlemleri **Migration** dosyaları ile gerçekleşitirebiliriz. Model oluşturuken Artisan komutuna
  **-m** eklersek, Model oluşturulurken o Model'e ait Migration dosyası da oluşuturulur.
  
```
php artisan make:model Product -m
```

* make:model komutunu kullanarak oluşturduğumuz Model dosyları app/Models klasörü içerisinde bulunur.

* Laravel Eloquent yapısında, model isminin sonuna "s" takısı ekleyerek database'de hangi tabloya karşılık geldiği bulunur.
  * Migration dosyaları tamamen küçük ve _ işareti ile ayrılarak ve "s" takısı kullanılarak isimlendirme yapılır.
  * Model dosyaları baş harfleri büyük ve "s" takısı kullanılmadan isimlendirilir.

* Eğer bu isimlendirme kurallarına uymayan istisna yapılar varsa, Model dosyamızın içinde **table** özelliğini
  kullanarak tablo adını belirtebiliriz.
  
```php
class Flight extends Model
{
  protected $table = 'my_flights';
}
```

* Eloquent default olarak Model'in tablosunda **created_at** ve **updated_at** sütunlarının mevcut olmasını beklemektedir.
  * Model **create** veya **update** işlemlerinde bu sütunlara veri ekleme işlemi yapar.
  * Bu özelliği kullanmayacaksak Model'in içerisinde **timestamps** özelliğine **false** değerini vermemiz gerekir.

```php
  public $timestamps = false;
```

* created_at ve updated_at sütunlarının isimlerini değiştirmek için **const** tanımlayıp istediğimiz text'i verebiliriz.

```php
const CREATED_AT = 'creation_date';
const UPDATED_AT = 'update_date';
```

* Model dosyası oluşturulduğunda attribute'ları boş gelir. Model'in instance'ını oluşturduğumuzda kullanmak istediğimiz sütunları
  **attributes** property'sinde belirtmemiz gerekiyor.
  
```php
protected $attributes = [
  'product_name',
  'product_description'
]
```
  
* Model dosyası oluşturup veritabanı ile bağlantısını kurduktan sonra veriyi çekebilir duruma geliriz.
  * **all** metodunu kullanarak o tabloda bulunan bütün verileri çekebiliriz.

* Eloquent metotlarına statik olarak erişebiliyoruz. 

```php
$products = Product::all();
```

* Sorgu oluşturmak için zincirleme olarak metotları kullanabiliyoruz, query builder gibi.

* **all** ve **get** fonksiyonları verileri **Collection** şeklinde bize verir. Collection **Illuminate\Database\Eloquent\Collection**
  instance'ıdır.
  * Collection sınıfı Laravel'in base sınıfı olan **Illuminate\Support\Collection**'ı extends eder.
  * Bu sınıf içerisinde Collection'lar içerisinde kullanabileceğimiz çeşitli kullanışlı metotlar bulunur.

* **find**, **first** veya **firstWhere** metotlarını kullanarak bir kaydın verilerine ulaşabiliriz.

```php
$product = Product::find(1);
$product = Product::where('')->first();
$product = Product::firstWhere();
```

* **findOrFail** veya **firstOrFail** metotlarını kullanarak Model'in bulunamaması durumunda 
  **Illuminate\Database\Eloquent\ModelNotFoundException** döndürülmesini sağlayabiliriz.
  
* **firstOrCreate** metodu ile bir değerin var olup olmadığını kontrol edebilir, yoksa oluşturabiliriz.
  * **firstOrNew** metodu aynı firstOrCreate gibidir, yalnızca manuel olarak **save** metodunu zincirlememiz gerekir.

* Bir kayıt eklemek için **new Model** diyerek Model'imizin instance'ını oluşturup, 
  * $model->property = value, şeklinde property'lerine değerleri atayıp,
  * $model->save() ile veritabanına kaydını gerçekleştirebiliriz.

```php
  $flight = new Flight;
  $flight->name = $request->name;
  $flight->save();
```

* Property'lere bu şekilde erişebilmek için Model dosyamızda **fillable** property'sinde bu property'leri
  eklememiz gerekmektedir.
  
* Save metodu created_at ve updated_at sütunlarına veri ekler.

* **updateOrCreate** metodu ile bir kaydı güncelleyebilir veya oluşturabiliriz.
  * İlk parametre where, ikinci parametre değişecek değerler olacaktır.

```php
$flight = Flight::updateOrCreate(
  ['departure' => 'Oakland', 'destination' => 'San Diego'],
  ['price' => 99, 'discounted' => 1]
);
```

* Bir kaydı silmek için find veya where ile model'i bulup **delete** metodunu çalıştırmamız gerekir.
  * Veya **destroy** metodunu kullanarak bir veya birden çok id'ye bağlı modeli silebiliriz.
 
```php
$product = Product::find(1);
$product->delete();

Product::destroy(1);
Product::destroy([1, 2]);
```

* Bir tablodaki bütün değerleri silmek için **truncate** metodu kullanılır.
  * Bu auto increment değerini de sıfırlar.

```php
Product::truncate();
```

* **SoftDelete**, yapısını kullanmak için Model'imizin içinde **use SoftDeletes** eklememiz gerekiyor.

```php
class Flight extends Model
{
  use SoftDeletes;
}
```

* SoftDeletes yapısında veriler silindiği zaman veritabanından silinmez, **deleted_at** değeri güncellenir.
  * **all** veya **get** metotları çağırıldığında **deleted_at** değeri olan kayıtlar getirilmez.
  * Silinmiş kayıtlara erişebilmek için **withTrashed()** metodu kullanılır.
  * **onlyTrashed** ile sadece silinmiş kayıtlara erişilebilir.
  
* Silinmiş bir kaydı geriye almak için **restore** metodu kullanılır.

* Bir kaydı kalıcı olarak silmek için **forceDelete** metodu kullanılır.

* **Scope** oluşturup kayıtları çekerken filtreleme işlemi yapabiliriz ancak o ileriki konularda.

* Bir Model'in eventlerinde (saved, created, updated vb...) **Observer**'lar çalışır, bu metotların içerisinde
  kontroller ve formatlama işlemi yapabiliriz.
  
Bu ders notu [Laravel'in Orjinal Dökümantasyonu](https://laravel.com/docs/8.x/)'ndaki [Eloquent](https://laravel.com/docs/8.x/eloquent) bölümündeki veriler kullanılarak hazırlanmıştır.
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
