## Logging

* Laravel Logging işlemleri **channels**'a dayanır. Her kanalın kendi loglama stili mevcuttur.
  * **single** kanalı loglama işlemini tekil log dosyasına yaparken, **slack** kanalı Slack uygulamasında mesaj olarak loglama yapar.

* Larvel loglama işlemleri için **Monolog** kütüphanesini kullanır.

* Loglama işlemleri ile ilgili ayarlar **config/logging.php** konfigürasyon dosyası içerisinde bulunur.

* Default olarak Laravel loglama işlemlerinde **stack** kanalını kullanır. Stack kanalı tekli log kanalllarını toplayarak tek bir kanal gibi
  kullanılmasını sağlar.

* Monolog defaul olarak bir **channel-name** ister ve bu projenin durumuna göre **production** veya **local** olabilir. Bunu değiştirmek için
  kanal konfigürasyonuna **name** eklememiz gerekir.
  
```php
'stack' => [
  'driver' => 'stack',
  'name' => 'channel-name',
  'channels' => ['single', 'slack'],
],
```

* Her loglama kanalı bir **driver** kullanır. Bu driver loglama işleminin nasıl ve nerede yapılacağını belirtir.

* Slack üzerinden loglama yapmak istiyorsak **url** gereklidir. Slack'ten gelen **WebHook** ile eşleşmelidir. 
  * Slack sadece **critical** ve üzeri olan logları bildirir, bunun ayarını değiştirmek için **config/logging.php** dosyasındaki **level** bölümünü
    değiştirmemiz gerekir.
    
```php
'channels' => [
  'stack' => [
      'driver' => 'stack',
      'channels' => ['syslog', 'slack'],
  ],

  'syslog' => [
    'driver' => 'syslog',
    'level' => 'debug',
  ],
  
  'slack' => [
    'driver' => 'slack',
    'url' => env('LOG_SLACK_WEBHOOK_URL'),
    'username' => 'Laravel Log',
    'emoji' => ':boom:',
    'level' => 'critical',
  ],
],
```

* Stack kanalı, **channels** opsiyonu içerisinde **syslog** ve **slack** kanalına bağlanmıştır. Yani loglama işlemi yapılacağı zaman bu iki kanala da
  loglama yapılacaktır. 
  * Loglama işleminin kritikliğine göre **level** koşulu kullanarak hangi tür hataların loglanacağını belirleyebiliriz.

* Loglama işlemlerinde, işlemin kritikliğine göre bir isimlendirmesi ve ona göre alınan aksiyonlar mevcuttur.
  * Laravelin loglama işlemlerini yapan Monolog'a göre kullanılabilir log seviyeleri:
    * **emergency**, **alert**, **critical**, **error**, **warning**, **notice**, **info**, **debug**

* Laravel'in **Log** Facade'ının metotlarını kullanarak level'a göre loglama işlemi yapabiliriz.
  * Log Facade'ı **Illuminate\Support\Facades\Log** burada bulunur.

```php
Log::debug('An informational message.');
Log::emergency('The system is down!');
```

* Loglama işlemlerine mesaj veya ekstra bilgi eklemek için ikinci parametre olarak dizi şeklinde ekleme yapabiliriz.

```php
Log::debug('An informational message.', ['key' => 'value']);
```

* Spesifik bir kanalda loglama yapmak istiyorsak, log facade'ının channel metodunu kullanbilir ve zincirleme olarak level'ı belirleyebiliriz.
  * Stack ile dizi şeklinde birden fazla kanala loglama yapabiliriz.

```php
Log::channel('slack')->info('Something happened!');
```

```php
Log::stack(['single', 'slack'])->info('Something happened!');
```

* Custom Monolog mesajları oluşturmak için **App\Logging\CustomizeFormatter** kullanarak **single** kanalının **tap** özelliğine eklememiz gerekir.

```php
'single' => [
  'driver' => 'single',
  'tap' => [App\Logging\CustomizeFormatter::class],
  'path' => storage_path('logs/laravel.log'),  
  'level' => 'debug',
],
```

* Bu class içerisinde **__invoke** metodunu kullanarak işlemleri yapabiliriz, **Illuminate\Log\Logger** instance'ı döndürmesi gerekir.
  * Mesajları bu şekilde formatlayabiliriz.

```php
namespace App\Logging;

use Monolog\Formatter\LineFormatter;

class CustomizeFormatter
{
  public function __invoke($logger)
  {
      foreach ($logger->getHandlers() as $handler) 
        $handler->setFormatter(new LineFormatter(
          '[%datetime%] %channel%.%level_name%: %message% %context% %extra%'
        ));
      }
  }
}
```

Bu notlar [Laravel Orjinal Dökümantasyonu](https://laravel.com/docs/8.x)'ndaki [Logging](https://laravel.com/docs/8.x/logging) bölümündeki veriler
kullanılarak hazırlanmıştır.

















