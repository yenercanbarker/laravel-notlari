## Error Handling

* Bir Laravel projesi oluşturduğumuzda, Laravel bizim için error ve exception handling'i hazırlamış olur.
  * Uygulamanın logladığı ve kullanıcıya gösterdiği hatalar **App\Exceptions\Handler** sınıfı içerisinde bulunur.

* **config/app.php** dosyasındaki **debug**, projede oluşan hataların ne kadar gösterilip gösterilmeyeceğini belirten özelliktir.
  * Varsayılan olarak **.env** dosyasındaki **APP_DEBUG** global değişkeninindeki değer kullanılır.

* Eğer lokalde çalışıyorsak APP_DEBUG değerini **true** olarak kullanmamız hataları yakalama ve düzeltmede işimizi kolaylaştıracaktır. Ancak projeyi
  **production** ortamına aldığımız zaman bu değeri **false** olarak düzeltmemiz gerekmektedir.
  
* Bütün hatalar **App\Exceptions\Handler** sınıf içerisinde handle edilir. 
  * Bu class özel hataları yakalama ve callback olaylarını yöneten **register** metodunu içerir.

* Farklı hata türlerine farklı şekilde raporlamak istiyorsak **reportable** metodunu kullanabiliriz.

```php
use App\Exceptions\InvalidOrderException; // InvalidOrderException olduğunda aşağıdaki metod raporlama yapar.

public function register()
{
  $this->reportable(function (InvalidOrderException $e) {
    //
  });
}
```

* **reportable** kullanarak hata raporlama yapsak bile Laravel default olarak loglama yapacaktır. Bunun önüne geçmek için zincirleme **stop** methodu
  kullanabiliriz veya **return false** ifadesini döndürebiliriz.
  
```php
$this->reportable(function (InvalidOrderException $e) {
    //
})->stop();

$this->reportable(function (InvalidOrderException $e) {
    return false;
});
```

* Hata mesajları loglandığında eğer kullanılabilir durumdaysa Larvel, şu anki kullanıcının id'sini de loglar.

* **App\Exceptions\Handler** sınıfı içindeki **context** metodunu kullanarak global olarak data ekleyebiliriz.
  * Bu data her loglama işleminde mesajlar arasına eklenecektir.

```php
protected function context() {
  return array_merge(parent::context(), [
    'key' => 'value'
  ]);
}
```

* Bazen özel hata durumları için özel ekstra mesajlar eklememiz gerekebilir. Özel mesaj eklemek istediğimiz Exception sınıfının **context** metodunu
  kullanarak loglama işlemlerinde ekstra mesaj kullanabiliriz.
  
```php
namespace App\Exceptions;

use Exception;

class InvalidOrderException extends Exception
{
  public function context()
  {
    return ['order_id' => $this->orderId];
  }
}
```

* Bazı durumlarda hatayı raporlayıp normal akışa devam etmesini isteyebiliriz. Bu durumlarda kullanıcıya hata mesajı gösterilmez. Laravel **report** global
  helper'ını kullanarak loglama işlemini gerçekleştirebiliriz.
  
```php
public function isValid($value)
{
  try {
  
  } catch (Throwable $e) {
    report($e);
    return false;
  }
}
```

* Eğer bazı hataları raporlamak istemiyorsak, Exception Handler sınıfında **$dontReport** değişkeni içerisinde tanımladığımız diziye exception
  isimlerini ekleyebiliriz.

```php
use App\Exceptions\InvalidOrderException;

protected $dontReport = [
    InvalidOrderException::class,
];
```

* Varsayılan olarak Laravel, bir hata oluştuğunda bunu bizim için HTTP response'una dönüştürür. **renderable** metodunu kullanarak hata mesajlarını
  kullanıcıya göstermek için renderlayabiliriz.
  
* Renderable methodu, **response** helper'ı ile oluşturulan **lluminate\Http\Response**'un bir instance'ı olmalıdır.

```php
public function register()
{
  $this->renderable(function (InvalidOrderException $e, $request) {
    return response()->view('errors.invalid-order', [], 500);
  });
}
```

* Hata yakalayıcı classlarda register metodunda tipe göre işlem yapmak yerine, report ve render metodunu tetikleyerek direkt işlem yapabiliriz.

```php
namespace App\Exceptions;

use Exception;

class InvalidOrderException extends Exception
{
  public function report()
  {

  }

  public function render($request)
  {
    return response(...);
  }
}
```

* Eğer raporlama işlemini kendimiz yapıyorsak ve Laravel'in default loglamasını istemiyorsak report metodunu false döndürebiliriz.

```php
public function report()
{
  return false;
}
```

Bu ders notu [Laravel'in Orjinal Dökümantasyonu](https://laravel.com/docs/8.x)'ndaki [Error Handling](https://laravel.com/docs/8.x/errors) bölümündeki bilgiler kullanılarak hazırlanmıştır.















