## Seeders
 
* Database'e örnek veri eklemek için seeder'ları kullanırız.
  * Seeder'lar **database\seeders** klasörü içerisinde bulunurlar.
  
* Varsayılan olarak **DatabaseSeeder** sınıfı tanımlanmıştır.
  * Bu sınıf içerisindeki **call()** metodu seed komutu girildiğinde çağırılıcak Seeder dosyalarının tanımlandığı bölümdür.
  
* Bir seeder oluşturduğumuzda **DatabaseSeeder** sınıfı içerisindeki **call()** metodunda bu sınıfı tanıtmamız gerekir.

* Seeder oluşturmak için aşağıdaki artisan komutunu kullanabiliriz:

```
php artisan make:seeder UserSeeder
```

* Seeder class'ları **run()** metodu barındırır.
  * **php artisan db:seed** komutu çalıştırıldığında bu metot içerisindeki işlemler gerçekleştirilir.
  
```php
class DatabaseSeeder extends Seeder
{
    public function run()
    {
        DB::table('users')->insert([
            'name' => Str::random(10),
            'email' => Str::random(10).'@gmail.com',
            'password' => Hash::make('password'),
        ]);
        
        // Run içerisinde firstOrCreate fonksiyonuna parametre olarak verileri yollayabiliriz
        // Gelen verileri if sorgularıyla model'lerin kaydı var mı yok mu sorgulayabiliriz
        // Kayıt yoksa yeni kayıt oluşturup, oluşturduğumuz verinin id'si ile ilişkili tablolara da kayıtlar ekleyebiliriz.
    }
}
```

* Çoklu verileri oluşturmak için **Model Factories** yöntemini kullanabiliriz.
  * Aşağıdaki kod, 50 adet kullanıcı ve her kullanıcıya ait bir gönderi oluşturur.

```php
public function run()
{
    User::factory()
            ->count(50)
            ->hasPosts(1)
            ->create();
}
```

* **php artisan db:seed** komutu ile seed işlemini yapabiliriz. Bu durumda bütün seeder'lar çalışır.

* **php artisan db:seed --class=UserSeeder** komutu ile istediğimiz Seeder class'ını çalıştırabiliriz.

* **php artisan fresh --seed** komutu ile veri tabanı tabloları tekrar oluşturulur ve seeder dosyaları çalışarak örnek verileri doldurur.

* Seeder'ları **production** ortamında kullanabilmek için **php artisan db:seed --force** komutunu kullanabiliriz.

Bu not [Laravel'in Orjinal Dökümantasyonu](https://laravel.com/docs/8.x)'ndaki [Seeders](https://laravel.com/docs/8.x/seeding) bölümündeki veriler kullanılarak hazırlanmıştır.
