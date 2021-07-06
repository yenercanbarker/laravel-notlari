 ## Observers
 
* Bir model'de gerçekleşen birden fazla olayı kuualnıyorsak, model içerisindeki bütün işlemleri tek bir sınıfta toplayabiliriz.
  * Observer sınıfları Eloquent eventlerin dinlemesini için isimlendirilmiştir.
  * Her event model'de o event tetiklendiği zaman çalışır.

* Observer oluşturmak için **make:observer UserObserver --model=User** artisan komutunu kullanabiliriz.
  * --model bölümüne eventlerini dinleyeceğimiz model'in ismini yazıyoruz.
  * Bu komut **App\Observers** klasörü içerisine yeni Observer'ı ekler. Böyle bir klasör yoksa artisan oluşturur.

```
php artisan make:observer UserObserver --model=User
```

* Observer oluşturduğumuz zaman karşımıza böyle bir dosya gelir:

```php
namespace App\Observers;

use App\Models\User;

class UserObserver
{
  public function created(User $user)
  {
    //
  }

  public function updated(User $user)
  {
    //
  }

  public function deleted(User $user)
  {
    //
  }

  public function forceDeleted(User $user)
  {
    //
  }
}
```

* Bir Observer'ı tanıtmak için istediğimiz model'in **observe** metodunu kullanabiliriz. Observer'ları **App\Providers\EventServiceProvider** içerisindeki **boot** metodunda
  tanıtabiliriz.
  
```php
use App\Models\User;
use App\Observers\UserObserver;

public function boot()
{
  User::observe(UserObserver::class);
}
```

* Modeller Database Transaction ile oluşturulursa Observer'a sadece transaction commitlendikten sonra eventleri dinlemesini söyleyebiliriz. **$afterCommit** property'sini
  kullanarak tanımlayabiliriz.
  * Eğer database transaction çalışmıyorsa, event'ler direkt execute edilir.

```php
namespace App\Observers;

use App\Models\User;

class UserObserver
{
  public $afterCommit = true;

  public function created(User $user)
  {
    //
  }
}
```

* Bazı durumlarda geçici olarak event'leri susturmak yani dinlemesini engellememiz gerekebilir.
  * **withoutEvents** metodunu kullanarak gerçekleştirebiliriz. 

```php
use App\Models\User;

$user = User::withoutEvents(function () use () {
  User::findOrFail(1)->delete();

  return User::find(2);
});
```

* Bazı durumlarda bir model'i **save** ederken eventleri tetiklememek isteyebiliriz.
  * Bu durumda **saveQuietly** metodunu kullanabiliriz.

```php
$user = User::findOrFail(1);
$user->name = 'Victoria Faith';
$user->saveQuietly();
```

Bu not [Laravel'in Orjinal Dökümantasyonu](https://laravel.com/docs/8.x)'ndaki [Observers](https://laravel.com/docs/8.x/eloquent#observers) bölümündeki veriler
kullanılarak hazırlanmıştır.
