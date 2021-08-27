## Email Verification

* Larvel'in hazır olarak bize sunduğu eposta doğrulama servisini kullanabilmek için **User** modelimizde **Illuminate\Contracts\Auth\MustVerifyEmail** Contract'ını implement
  etmemiz gerekiyor.
 
```php
<?php

namespace App\Models;

use Illuminate\Contracts\Auth\MustVerifyEmail;
use Illuminate\Foundation\Auth\User as Authenticatable;
use Illuminate\Notifications\Notifiable;

class User extends Authenticatable implements MustVerifyEmail
{
    use Notifiable;

    // ...
}
```

* Bu Interface'i implement ettikten sonra, uygulamamıza kayıt olan her kullanıcıya eposta onay maili gönderiliyor.
  * **App\Providers\EventServiceProvider*** içerisinde Laravel zaten **SendEmailVerificationNotification** Notification'unu **Illuminate\Auth\Events\Registered** Event'ine
    bağlamış oluyor.
  * [Starter Kit](https://laravel.com/docs/8.x/starter-kits) yerine kendimiz implement etmek istiyorsak; **Illuminate\Auth\Events\Registered** içerisine ekleyelim.
  
  
```php
event(new Registered($user));
```

* Email Verification için Laravel database içerisinde Users tablosuna email_verification_at sütununu ekliyor, projeyi çektiğimizde **php artisan migrate** ile varsayılan
  veritabanı yapısını elde edebiliriz.
  
* Bu yapıyı kullanabilmek için **Rotuting** yapılandırmasını yapmamız gerekiyor.
  * 1- Kullanıcı kayıt olduktan sonra mail gönderildiğini ve tıklaması gerektiğini kullanıcıya bildireceğimiz route
  * 2- Kullanıcı Email Verification mailine tıkladığında gideceği route
  * 3- Kullanıcıya yeniden verification mail'i gönderecek olan route
  
* 1- Kullanıcı kayıt olduktan sonra mail gönderildiğini ve tıklaması gerektiğini bildiren route:
  * Bu route'un adı **verification.notice** olmalıdır. Laravel'in **verified** middleware'i kullanıcı mail adresini onaylamazsa otomatik bu adrese yönlendirir.

```php
Route::get('/email/verify', function () {
    return view('auth.verify-email');
})->middleware('auth')->name('verification.notice');
```
  
* 2- Kullanıcı Email Verification mailine tıkladığında gideceği rotue:
  * Bu route'un adı **verification.verify** olmalıdır ve middleware olarak **auth** ve **signed**'i kullanmalıdır.
  * Bu route'un fonksiyonunda **Illuminate\Http\Request** yerine **EmailVerificationRequest** kulllanılmaktadır. Bu Larvel'in hazır olarak sunduğu **Form Request**'lerden
    biridir. Request'i validate ederken **id** ve **hash** validasyonunu otomatik olarak kendisi yapar.
  * Sonrasındaki **fullfill** metodu, **markEmailAsVerified** metodunu tetikler ve **Illuminate\Auth\Events\Verified** Event'ini tetikler. **markEmailAsVerified** metodu
    **App\Models\User** modelinde **Illuminate\Foundation\Auth\User** Base Class'ı vasıtayıla kullanılabilir.
  * Kullanıcı mail'i onayladıktan sonra yönlendirme işlemi yapılır.
  
```php
use Illuminate\Foundation\Auth\EmailVerificationRequest;

Route::get('/email/verify/{id}/{hash}', function (EmailVerificationRequest $request) {
    $request->fulfill();

    return redirect('/home');
})->middleware(['auth', 'signed'])->name('verification.verify');
```

* 3- Kullanıcıya yeniden verification mail'i gönderecek route:
  * Bazı senaryolarda kullanıcı mail'i görmemiş, yanlış mail girmiş veya mail gitmemiş olabilir. Bu gibi durumlarda gönderimin tekrarlanması gerekmektedir.
  
```php
use Illuminate\Http\Request;

Route::post('/email/verification-notification', function (Request $request) {
    $request->user()->sendEmailVerificationNotification();

    return back()->with('message', 'Verification link sent!');
})->middleware(['auth', 'throttle:6,1'])->name('verification.send');
```
  
* Email adresini Verify etmiş kullanıcılar için oluşturulmuş default **verified** middleware'ini kullanarak, email adresini verify eden kullanıcıların girebileceği
  route'lar tanımlayabiliriz.
  * Verify olmayan kullanıcılardan biri bu route'a erişmeye çalıştığında **verification.notice** isimli route'a yönlendirme yapılır.
  
```php
Route::get('/profile', function () {
    // Only verified users may access this route...
})->middleware('verified');
```
  
* Laravel'in sağladığı default Emial Verification yapısı genel olark yeterli olsa da Laravel bu yapıyı düzenlememize izin vermektedir.
  * **Illuminate\Auth\Notifications\VerifyEmail** Notification'unun **toMailUsing** metodunu kullanarak mail gönderimini yönetebiliriz.
    * Bu method Notifiable modeli ve Email'in doğrulanacağı url'in adresini parametre olarak alır.
    * Bu method return değerinde **Illuminate\Notifications\Messages\MailMessage** dönmelidir.
    * **App\Providers\AuthServiceProvider**'ın **boot** metodunda **toMailUsing** metodunu kullanmamız gerekmektedir.
  
```php
use Illuminate\Auth\Notifications\VerifyEmail;
use Illuminate\Notifications\Messages\MailMessage;

/**
 * Register any authentication / authorization services.
 *
 * @return void
 */
public function boot()
{
    // ...

    VerifyEmail::toMailUsing(function ($notifiable, $url) {
        return (new MailMessage)
            ->subject('Verify Email Address')
            ->line('Click the button below to verify your email address.')
            ->action('Verify Email Address', $url);
    });
}
```
  
* Laravel Starter Kits'i kullanıyorsak, Email Verification işlemlerinde Event'leri tetiklemektedir. Manuel olarak bu sistemi entegre etmek istiyorsak verification işlemlerinden
  sonra Event'leri kullanmamız gerekmektedir.
  * Bu işlem için **EventServiceProvider** içerisinde Listener'ları tanımlamamız gerekmektedir.
  
```php
/**
 * The event listener mappings for the application.
 *
 * @var array
 */
protected $listen = [
    'Illuminate\Auth\Events\Verified' => [
        'App\Listeners\LogVerifiedUser',
    ],
];
```
  
Bu not [Laravel'in Orjinal Dökümantasyonu](https://laravel.com/docs/8.x)'ndaki [Email Verification](https://laravel.com/docs/8.x/verification) bölümündeki veriler kullanılarak 
hazırlanmıştır.
  
  
  
  
  
  
  
  
  
  
  
  
  
