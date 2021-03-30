** Laravel Klasör Yapısı

* **App** klasörü : Core kodların bulunduğu klasördür. 
  * **Broadcasting** klasörü : "broadcast channel" sınıflarının bulunduğu klasördür. Bu sınıflar **make:channel** komutuyla oluşturulan
  sınıflar bu dosyada bulunur. Varsayılan Laravel dosyalarında bu klasör bulunmamaktadır.
  * **Console** klasörü : Kendi yazdığımız artisan komutlarının bulunduğu klasördür. Bu klasördeki komutlar **make:command** komutuyla
  oluşturulan komutları barındırır.
  * **Events** klasörü : Varsayılan dosyalarda bu klasör bulunmaz. **event:generate** ve **make:event** komutularıyla oluştuırulan
  event class'lar bu klasörde bulunur. Event'lar bir işlem gerçekleştiğinde yapılacak başka işlemler olarak adlandırılabilir, projenin
  esnek olmasını ve coupling'in azalmasını sağlar.
  * **Exceptions** klasörü : Projede kullanacağımız Exception'larıbu klasörde tanımlarız. **Handler** class'ında değişiklikler yapmamız
  gerekmektedir.
  * **Http** klasörü : **Controller, Middleware ve Form Requsest**'ler bu klasörde bulunur. Proje geliştirme yaparken en çok kullanacağımız
  klasörlerden bir tanesidir.
  * **Jobs** klasörü : Varsayılan olarak bu klasör bulunmaz. **make:job** komutuyla oluşturulan Job'lar bu klasörde bulunur. Joblar
  queue'lara alınabilir ve yaşam döngüsü içinde senkron olarak çalıştırılabilir.
  * **Listeners** klasörü : Varsayılan olarak bu klasör bulunmaz. **event:generate** veya **make:listener** komutuyla oluşturulan 
  listener'lar bu klasörde bulunur. Event Listener'lar bir Event ve Event oluştuğunda çalıştırılacak handler'lar içinde bulunur. 
  Örneğin : UserRegistered event'i ve onu handle edeceğimiz SendWelcomeEmail listener ekleriz.
  * **Mail** klasörü : Varsayılan olarak bu klasör bulunmaz. **make:mail** komutuyla oluşturulan Mail class'ları bu klasörde bulunur.
  **Mail::send** metoduyla email gönderme işlemini gerçekleştirebiliriz.
  * **Models** klasörü : Eloquent ORM'deki Eloquent Model Class'ları bu klasörde bulunur. Projenin veri katmanı bu klasördür.
  * **Notifiacations** klasörü : Varsayılan olarak bu klasör bulunmaz. **make:notification** komutuyla oluşturulan Notification'lar
  bu klasörde bulunur. Notification'lar email, Slack, SMS ve database kayıtları gibi özellikleri soyutlar.
  * **Policies** klasörü : Varsayılan olarak bu klasör bulunmaz. **make:policy** komutuyla oluşturulan Policy'ler bu klasörde tutulur.
  Policy'ler Authorization işlerinde kullanılır, kullanıcı için doğrulama ve güvenlik kontrollerini kullanabiliriz.
  * **Providers** klasörü : Service Provider'ların bulunduğu klasördür.
  * **Rules** klasörü : Varsayılan olarak bu klasör bulunmaz. **make:rule** komutuyla oluşturulan Rule'lar bu klasörde bulunur. Rule'lar
  bir obje için validasyon işlerini encapsulate etmek için kullanılır.
* **Bootstrap** klasörü : app.php'nin ve cache klasörünün bulunduğu klasördür.
* **Config** klasörü : Projenin konfigürasyon dosyalarının bulunduğu klasördür.
* **Database** klasörü : **Migration, Model Factory ve Seed**'lerin bulunduğu klasördür.
* **Public** klasörü : index.php dosyası, **assets** klasörü ve o klasör içinde **images, JavaScript ve CSS** dosyalarını barındırır.
* **Resources** klasörü : **View** dosyaları, **Language** dosyaları ve compile edilmemiş asset'ler bu klasörde bulunur.
* **Routes** klasörü : Varsayılan route dosyaları bu klasörde bulunur; **web.php**, **api.php**, **console.php** ve **channels.php**
klasörü bu dosyada bulunur.
* **Storage** klasörü : **Log**'lar, compile edilmiş **Blade Template**'leri, **Session**'lar, **File Cache**'ler ve framework
tarafından oluşturulan dosyalar bulunur. **app** klasörü uygulama tarafından oluşturulmuş dosyaları saklamak için kullanılır, 
**framework** klasörü framework ve cache tarafından oluşturulan dosyalarını saklamak için kullanılır, **logs** klasörü uygulamanın
log dosyalarını saklamak için kullanılır.
  * storage/app/public klasörü kullanıcı tarafından oluşturulmuş dosyaları saklamak içn kullanılır, **php artisan storage:link**
  komutuyla storage içine link oluşturabiliriz.
* **Tests** klasörü : **Test**'lerin bulunduğu klasördür. **php artisan test** komutuyla testleri çalıştırabiliriz.
* **Vendor** klasörü : **Composer Dependency**'leri barındıran klasördür.

Bu not Laravel'in resmi dökümantasyonundaki [Structure](https://laravel.com/docs/8.x/structure) sayfasındaki veriler kullanılarak hazırlanmıştır.
