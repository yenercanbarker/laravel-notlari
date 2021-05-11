## Session

* Projede session konfigürasyonları **config/session.php** içeriside bulunur.
 
* Session.php içerisindeki **driver** özelliği sessionların nerede tutulacağını belirtir:
  * **file**, sessionlar **storage/framework/sessions** klasöründe tutulur.
  * **cookie**, sessionlar güvenli ve şifrelenmiş cookielerde tutulur.
  * **database**, sessionlar ilişkisel veritabanlarında tutulur.
  * **memcached/redis**, sessionlar ikisinden birinde tutulur. Bunlar hızlı, ön bellek tabanlıdır.
  * **dynamodb**, sessionlar AWS DynamoDB içerisinde tutulur.
  * **array**, sessionlar PHP dizilerinde tutulur ve kalıcı değildir.

* Database driver'ını kullanacağımız zaman sessionlar için bir tablo oluşturmamız gerekir.

```php
  php artisan session::table
  php artisan migrate
```

* Session verilerine erişebilmek için iki yöntem kullanılır.
  * 1- Session helper'ı
  * 2- Request instance'ı

```php
// Session Helper ile Session Değeri
  $value = session('key');

// Default Veri Ekleme
  $value = session('key', 'default');

// Session oturumuna değer ekleme
  session(['key' => 'value']);

// Request Instance ile Session Değeri
  $value = $request->session()->get('key');
```

* Bütün sessionların verilerini almak için :

```php
  $data = $request->session()->all();
```

* Sessionların varlığını sorgulamak için : 

```php
  $request->session()->has('users')  // Session varsa ve null değilse true döner.
```

```php
  $request->session()->exists('users') // Session varsa true döner null olması önemli değil.
```

```php
  $request->session()->missing('users') // Session yoksa veya null'sa true döner.
```

* Sessionlarda veri tutmak :

```php
  $request->session()->put('key', 'value');

  session(['key' => 'value']);
  
  $request->session()->push('user.teams', 'developers'); // Array için . kullanarak ekleyebiliriz.
```

* Session'ı alıp silmek :

```php
  $value = $request->session()->pull('key', 'default');
```

* Session verisini azaltmak/arttırmak :

```php
  $request->session()->increment('count');

  $request->session()->increment('count', $incrementBy = 2);

  $request->session()->decrement('count');

  $request->session()->decrement('count', $decrementBy = 2);
```

* Form değerlerini **flash** ile session'a aktarabiliriz.

```php
  $request->session()->flash('status', 'Task was successful!');
```

```php
  $request->session()->reflash();

  $request->session()->keep(['username', 'email']); // Flash ile tutulacak verileri array ile belirtiriz.
  
  $request->session()->now('status', 'Task was successful!'); // O anki isteği almak istersek
```

* Session silmek için : 

```php
  $request->session()->forget('name');
  $request->session()->forget(['name', 'status']);
  $request->session()->flush();
```

* Session ID'yi yeniden oluşturmak 

```php
  $request->session()->regenerate();
  $request->session()->invalidate();
```

Bu ders notu [Laravel'in orjinal dökümantasyonu](https://laravel.com/docs/8.x)'nun [HTTP Session](https://laravel.com/docs/8.x/session) bölümündeki veriler
kullanılarak hazırlanmıştır.




























