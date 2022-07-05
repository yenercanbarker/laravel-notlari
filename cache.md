# Cache

* Cache'leme yaparak, verilere çok daha hızlı şekilde erişebiliriz.

* Laravel tarafında Redis (Predis) ile DB Cache veya View Cache yapabiliriz. Page-Cache paketi ile sayfaları otomatik cache'lemeyi yapabiliriz.

* Bir Laravel projesinde **Cache** configürasyonları, **config/cache.php** içerisinde tutulur.
  * Bu dosya içerisinde uygulamamızın default cache motorunu belirtebiliriz.

* Default bir Laravel projesinde cache driver'ı **File**'dır.

* Laravel Cache için kullanılabilecek üç farklı yöntem vardır.
  * 1- File
  * 2- Database
  * 3- Redis/Memcached/DynamoDB

* Database cache istiyorsak **php artisan cache:table** ile Schema oluşturabiliriz.

* **Redis** cache kullanmak istiyorsak **predis** paketini eklememiz gerekmektedir. (composer require predis/predis)

* Cache'ler **key** - **value** pair olarak tutulur. 
  * Value içerisinde çoklu değerler için **json_encode** kullanılabilir.

En çok kullanılan cache metotları :

* **get** : Cache'de tutulan key'e ait değeri getirir.
  * Key bulunamazsa default olarak **null** döner, istenilirse ikinci parametre ile default değer girilebilir.
  * İstenilen durumlarda ikinci parametre fonksiyon bile olabilir, içerisinde db verileri falan alınabilir.

```php
Cache::get('key');
```

* **store** : Birden fazla Cache driver kullanabilmek için store kullanabiliriz.

```php
Cache::store('redis')->get('');
Cache::store('database')->put('key', 'value', minute);
```

* **has** : Key'in var olup olmadığını kontrol eder.
  * Eğer key bulunamazsa **false** döner, eğer varsa ve değeri null ise de **false** döner.

```php
Cache::has('key');
```

* **increment** : Cache'de tutulan bir değeri arttırmak için kullanılır.
* **decrement** : Cache'de tutulan bir değeri azaltmak için kullanılır.
  * İkinci parametre ile değer belirtilebilir, tek parametre var ise değer otomatik olarak bir olur.

```php
Cache::increment('key');
Cache::increment('key', amount);
Cache::decrement('key');
Cache::decrement('key', amount);
```

* **remember** : Cache'de tutulan key var ise verisini döndürür, yok iste callback fonksiyon içindeki veriyi key ile beraber ekler.
  * **rememberForever** : **remember**'ın aynısı sadece zaman parametresi yok direkt kalıcı olarak tutuluyor.

```php
$value = Cache::remember('key', $seconds, function () {
 return DB::table('users')->get();
});

$value = Cache::rememberForever('users', function () {
    return DB::table('users')->get();
});
```

* **pull** : Cache'de key var ise değerini çeker, sonra cache'den siler.

```php
$value = Cache::pull('key');
```

* **put** : Cache'e key value eklemek / güncellemek için kullanılır.
  * $seconds değeri 3. parametre olarak eklenebilir, parametre yoksa forever olarak geçer. Saniye bazlı veri alabilir.

```php
Cache::put('key', 'value', $seconds);
Cache::put('key', 'value');
```

* **add** : Cache'e veri eklemek için kullanılır. Eğer key mevcut değil ise, key value olarak kaydeder. Key mevcut ise false döner.
*   * $seconds değeri 3. parametre olarak eklenebilir, parametre yoksa forever olarak geçer. Saniye bazlı veri alabilir.

```php
Cache::add('key', 'value', $seconds);
```

* **forever** : Cache'e veri eklemek için kullanılır. Zaman sınırı yoktur, kalıcı olarak eklenir.
  * **forever** kullanılarak eklenen veriler **forget** ile silinebilir.

```php
Cache::forever('key', 'value');
```

* **forget** : Cache'den veri silmek için kullanılır. 

```php
Cache::forget('key');
```

* Veri silmek için bir diğer yöntem, **put** kullanarak, $seconds parametresine 0 veya negatif bir değer verilebilir.

```php
Cache::put('key', 'value', 0);
Cache::put('key', 'value', -5);
```

* **flush** : Bütün cache'i silmek için kullanılır.

```php
Cache::flush();
```

* Cache fonksiyonları **Cache::** veya **cache()** ile kullanılabilir.

## Cache Tags

* Cache Tag'ları **file**, **database** veya **dynamodb** driver'larında kullanılmamaktadır.

* Yukarıdaki fonksiyonlar burada da geçerli, sadece her tag'a özel key'ler barındırabiliyoruz.
  * Örneğin aşağıdaki kod; **a** ve **b** olarak iki tag açar, **a.key** = **a.value** olur, **b.key** = **b.value** olur.
  * Bunun avantajı, her tag'a özel keyler üretebiliyor olmamız. Örneğin sadece bir tag içerisindeki bütün verileri, **tags** ve **flush** ile silebiliriz, diğer tag
  lardaki veriler aynı kalabilir.

```php
Cache::tags(['a', 'b'])->put('key', 'value', $seconds);
```

Bu ders notu [Laravel'in orjinal dökümantasyonu](https://laravel.com/docs/9.x)'ndaki [Cache](https://laravel.com/docs/9.x/cache) bölümündeki
veriler kullanılarak hazırlanmıştır.





















































