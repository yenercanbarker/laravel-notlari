## Localization

* Laravel'in **Localization** özelliği, farklı dillerdeki metinleri alabilmemizi ve uygulamayı çoklu dil yapısıyla oluşturabilmemizi sağlar.

* Laravel lokalizasyon işlemleri için iki özellik sağlar.
  * Dil dosyaları **resources/lang** klasörü içerisinde bulunur. Bu klasörler içerisine **subfolder**'lar oluşturarak çoklu dil yapısı elde edebiliriz.
  * Lang klasörü içerisine yeni klasörler açıp, php sayfalarında **array** return edecek şekilde verileri ekleyebiliriz.
    * Veya bunun yerine direkt lang klasörü içerisine .json uzantılı dil dosyaları oluşturabiliriz.

* Uygulamanın default dili **config/app.php** dosyasında bulunur.
  * **locale** özelliğinde belirtilen dil kullanılır.
  * Dil dosyalarında herhangi bir eksik olduğunda **fallback_locale** dosyasındaki veriler kullanılır.

* Runtime'da uygulamanın dilini değiştirmek istiyorsak **App** Facade'ının **setLocale** metodunu kullanabiliriz. Aşağıdaki örnekte parametre olarak gelen dil seçeneğine göre
  uygulamanın dili değiştiriliyor.
  
```php
use Illuminate\Support\Facades\App;

Route::get('greetings/{locale}', function($locale) {
  if(!in_array(['en','es','fr','tr'])) {
    abort(404);
  }
  
  App::setLocale($locale);
})
```

* Uygulamanın kullandığı dili öğrenmek için **App** Facade'ının **currentLocale** ve **isLocale** metodlarını kullanabiliriz.

```php
$currentLocale = App::currentLocale();

if(App::isLocale() == 'en') {
  // uygulama ingilizce dilinde
}
```

* Çok fazla çevirisi yapılabilen metin olduğu zaman her bir string'e bir key bulmak zor olabilir. Bu yüzden Laravel bize keyler yerine **default string** dediğimiz
  çevirilmemiş metin kullanabilmemizi sağlar.
  * Bu tür metinleri içeren dil dosyaları **resources/lang** klasörü içerisindeki **JSON** dosyaları içerisinde bulunur.
  * Örneğin İspanyolca dil desteği eklemek için **resources/lang/es.json** dosyasını oluşturup, bu JSON dosyası gibi metinleri tanımlayabiliriz.

```json
{
  "I love programming.": "Me encanta programar."
  
  // __('I love programming.')
}
```

* **Translation String Key** kullanırken dikkat etmemiz gereken nokta, bu stringler ile aynı isimde bir dil dosyası olmamasıdır.
  * Key ve aynı isimde dosya kullanırsak; Örneğin Lokal dilimiz 'NL' seçili olsun, nl/action.php varsa ve nl.json dosyası bulunmuyorsa bizim istediğimiz gibi
    json dosyasından translation string key ile erişim yapamayacağımız için **nl/action.php** dosyasına erişmiş oluruz.
    
* Dil dosyalarındaki Translation String Key ve değerlerine erişebilmek için **__** helper'ını kullanabiliriz.
  *  Bir subfolder içerisindeki değere erişmek için de yine aynı helper kullanılır, klasörler içine geçişi **.** ile sağlayabiliriz.
     * Aşağıdaki kod parçacığı lokal dil klasörümüzün içindeki **messages** dosyası içindeki dizinin **welcome** elemanını ekrana yazdırır.

```php
echo __('messages.welcome'); veya {{ __('messages.welcome') }}
```

* Translation String bulunamazsa Translation String Key döndürülür.
* Default Translation String kullanıyorsak key yerine o string geri döndürülür.

* Dil metinlerine(Translation String) **:degisken** ile 'degisken' kısmı değiştirilecek şekilde parametre ekleyebiliriz.

```php
'welcome' => 'Welcome, :name',

echo __('messages.welcome', ['name' => 'Matris']);

// name bölümüne verdiğimiz formatta dönüş alırız yani
// name = matris
// Name = Matris
// NAME = MATRİS
```

* Dil dosyaları için oluşturduğumuz metinlere parametreler ile tekil, çoğulluk durumlarına göre metinlerin değişimini sağlayabiliriz.

```php
'apples' => 'There is one apple|There are many apples', 
// veya
{
  "There is one apple|There are many apples": "Hay una manzana|Hay muchas manzanas"
}
```

* Hangi aralıkta hangi mesajın görünmesini istediğimizi belirtmek için :

```php
'apples' => '{0} There are none|[1,19] There are some|[20,*] There are many',
```

* Bu tanıtımları oluşturduktan sonra parametre göndermek için :

```php
echo trans_choice('messages.apples', 10); // parametreler ekleyebiliyoruz.
```

* Trans_choice ile gönderilen sayıyı **:count** default placeholder'ı ile görüntüleyebiliriz.

```php
'apples' => '{0} There are none|{1} There is one|[2,*] There are :count',
```

* Bazı ek paketler kendi dil dosyalarını kullandığı zaman dil dosyalarını bastırabilir. Override edebilmek için dil dosyamızı **resources/lang/vendor/{package}/{locale}** 
  klasörüne ekleyip, sadece çakışan dil değerlerini bu dosya içerisinde düzeltebiliriz, diğer değerler için paketteki dil dosyasındaki metinler kullanılır.
  
Bu ders notu [Laravel'in Orjinal Dökümantasyonu](https://laravel.com/docs/8.x)'ndaki [Localization](https://laravel.com/docs/8.x/localization) bölümüdeki veriler 
kullanılarak hazırlanmıştır.
