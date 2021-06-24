## Storage 

* Laravel, **Filesystem** PHP paketini kullanır.

* Filesystem konfigürasyon ayarları **config/filesystem.php** içerisinde belirtilmiştir.
  * Bu dizinde filesystem 'disk'lerimizi ayarlayabiliriz.
  * Her disk farklı bir driver ve storage location barındırır.

* **local** driver lokal sunucuda depolanan dosyalara, **s3** driver'ı ise Amazon'un S3 bulut depolarına yazmak için kullanılır.

* **Local** driver kullandığımızda dosyalar, **filesystem**'de belirttiğimiz **root** konumuyla bağlantılıdır.
  * **Root** tanımlanmadıysa default olarak **storage/app** klasörü kullanılır. 
  * Örneğin aşağıdaki örnek **storage/app/example.txt** dosyasını oluşturur.

```php
use Illuminate\Support\Facades\Storage;

Storage::disk('local')->put('example.txt', 'Contents');
```

* **Public** disk verilerin genel olarak erişilebileceği dosyaların konumlandırılması için kullanılır.
  * Filesystem içerisinde bulunur.
  * Varsayılan olarak **public** disk, local driver'ı kullanır ve dosyaları **storage/app/public** içerisinde saklar.

* Public disk'e yazdığımız dosyalara erişebilmek için **sembolik link**'e ihtiyacımız var.
  * Symbolic link, **public/storage**'dan **storage/app/public**'e yönlendirme yapacak linktir.

* Symbolic link oluturmak için **storage:link** Artisan komutunu kullanabiliriz.

```
php artisan storage:link
```

* Bir dosya saklandıktan ve symbolic link oluşturulduktan sonra **asset** helper'ını kullanarak o dosyaya erişim linki oluşturabiliriz.

```php
echo asset('storage/file.txt');
```

* Symbolic link'leri filesystem konfigürasyon dosyasında kontrol edebiliriz.
  * Aşağıdaki örnekte storage link artisan komutu çalıştırıldığında iki link de oluşturulur.

```php
'links' => [
  public_path('storage') => storage_path('app/public'),
  public_path('images') => storage_path('app/images'),
],
```

* **S3** ve **SFTP** kullanmadan önce composer üzerinden paketlerini indirmemiz gerekiyor.

```
Amazon S3: composer require league/flysystem-aws-s3-v3 "~1.0"
SFTP: composer require league/flysystem-sftp "~1.0"
CachedAdapter: composer require league/flysystem-cached-adapter "~1.0"
```

* **Storage** Facade'ını kullanarak disklerimizi kullanarabiliriz. 
  * Storage Facade'ın **disk** metodu kullanılmazsa otomatik olarak default disk kullanılır.
  * Birden fazla disk kullanıyorsak, dosyanın hangi disk'e yazılacağını belirtmemiz gerekir.

```php
Storage::put('avatars', $content); // avatars klasöründe, $content içerisindeki değer saklanır.
Storage::disk('s3')->put('avatars/1', $content); // s3'e yüklenir.
```

* Bazı zamanlarda filesystem içinde değil de runtime da disk oluşturup kullanabiliriz, bunun için:
  * **Storage** Facade'ının **build** metodunu kullanabiliriz.

```php
use Illuminate\Support\Facades\Storage;

$disk = Storage::build([
  'driver' => 'local',
  'root' => '/path/to/root', // dosyaların saklanacağı dosyanın konumunu belirtiriz.
]);

$disk->put('image.jpg', $content);
```

* **get** metodunu kullanarak dosyalara erişebiliriz.
  * Bu metot içerisinde filesystem'de belirttiğimiz **root** konumunda belirtilen dosyayı arar.

```php
$contents = Storage::get('file.jpg');
```

* **exists** metodu ile dosyanın mevcut olup olmadığını sorgulayabiliriz.

```php
if (Storage::disk('s3')->exists('file.jpg')) {
  // ...
}
```

* **missing** metodu ile dosyanın mevcut olmadığını sorgulayabiliriz.

```php
if (Storage::disk('s3')->missing('file.jpg')) {
  // ...
}
```

* **download** metodu ile dosya indirme işlemini gerçekleştirebiliriz.
  * Bu metot ikinci parametre olarak dosyanın görüntülenecek ismini içerir.
  * Üçüncü parametre olarak HTTP kodlarını içerir.

```php
return Storage::download('file.jpg'); 
return Storage::download('file.jpg', $name, $headers);
```

* **url** metodunu kullanarak dosyaya ait url'i döndürebiliriz.
  * Local driver verilen path'in önüne **/storage** ekleyerek link oluşturacaktır.
  * s3 driver'ı kullanıyorsak full remote url döndürülür.

* Local driver ile link oluştururken dikkat etmemiz gereken bir diğer nokta ise dönen değerin **urlencoded** olmamasıdır.
  * Dolayısıyla türkçe karakter kullanmamız gerekiyor, dosya ismini link yapısına uygun oluşturumamız gerekiyor.

* Geçiçi url oluşturmak için **temporaryUrl** metodunu kullanabiliriz. 
  * ikinci parametre olarak **DateTime** alabilir. 
  * Üçüncü parametre olarak dizi şeklinde s3 parametrelerini alabilir. 

* Disk konfigürasyon dizisinde **url** değerine linklerde ön path belirtebiliriz.
