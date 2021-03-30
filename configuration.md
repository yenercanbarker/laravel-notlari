## Laravel Konfigürasyon Dosyası

* Laravel Framework'ünün konfigürasyon dosyaları **config** klasörü içerisinde bulunur. Bu konfigürasyon özelliklerinden bazıları :
  * Veri tabanı bağlantısı,
  * Mail servislerinin bağlantısı,
  * Application timezone ve encryption key vb.

* Laravel'in varsayılan .env dosyası bazı hazır konfigürasyon değerlerini barındırır.

* .env dosyalarının erişimi güvenlik sorunları oluşturabilir, proje dosyalarını paylaşırken dikkatli olmalıyız.

* .env dosyasına bir sabit tanımlayacaksak ve boşluk içeriyorsa "  " arasına yazılır.

* Proje tarayıcıda açıldığında yani yüklendiğinde .env dosyasındaki değişkenlere **$_ENV** süper-global PHP değişkeni ile erişebiliriz. 
  * Veya **env()** helper'ını kullanabiliriz.

* Konfigürasyon verilerine erişmek için **config()** hepler'ını kullanabiliriz.
  * Örneğin : config('app.timezone');

* Konfigürasyon verilerini değiştirmek için config() fonksiyonuna dizi içinde key => value şeklinde elemanları gönderebiliriz.
  * Örneğin : config(['app.timezone' => 'Turkey/Istanbul']);

* Laravel uygulamamızı hızlandırmak için, Konfigürasyon dosyalarımızı cache'e alabiliriz. Bunu yapmak için aşağıdaki artisan komutunu
  kullanabiliriz ;

```
php artisan config:cache 
```

* Konfigürasyon dosyalarında yaptığımız değişiklikler cache'e takılabilir o yüzden değişikliklerden sonra cache'i temizleyebiliriz.

* Laravel projemizi bakım moduna almak için aşağıdaki artisan komutunu çalıştırabiliriz :

```
php artisan down
```
* Laravel projemizi bakım moduna alıp ve sonrasında HTTP Header değerini eklemek için, aşağıdaki komut çalıştırılabilir :

```
php artisan down --retry=600
```

* Bakım moduna scret key ekleyerek, bu key ile bakım modundayken siteye erişebiliriz :
  * example.com / secretkey ile siteye erişirsek otomatik olarak / route'una gideriz.

```
php artisan down --secret="1630542a-246b-4b66-afa1-dd72a4c43515"
```

* Laravel sayfası renderlanmadan önce hata döndürmek için :

```
php artisan down --render="errors::503"
```

* Proje bakım modundayken sayfaya erişildiğinde direkt linke göndermek için : 

```
php artisan down --redirect=/
```

* Projeyi bakım modundan çıkarmak için :

```
php artisan up
```

Bu not Laravel'in resmi dökümantasyonundaki [Configuration](https://laravel.com/docs/8.x/configuration) sayfasındaki veriler
kullanılarak hazırlanmıştır.
