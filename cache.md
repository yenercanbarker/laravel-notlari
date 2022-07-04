## Cache

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

* 





























