## Pivot Tablolar ve "Çoktan Çoka" Veri Tabanı İlişkileri

* Veri tabanı geliştirirken dikkat etmemiz gereken noktalardan biri bir tablonun içerisine başka bir tablonun verisini direkt olarak eklememektir. Böyle bir işlem yaptığımız zaman
  eğer tablodaki veri değişirse biz direkt olarak kullandığımız için kayıt tuttuğumuz veri diğer tarafta değişmeyecektir, bu da tutarsızlıklara yol açacaktır.
* Bir tablo içerisinde başka bir tablodan veriyi kullanmak istiyorsak, ana tablomuzda yan tablonun kayıt id'sini tutarak ve sonrasında yan tabloyu join'leyerek istediğimiz
  verileri kullanabilir hale geleceğiz ve kaydı id şeklinde tuttuğumuz için yan tabloda yapılan hiçbir değişiklik ana tabloyu etkilemeyecektir. Peki bu yeterli mi?
* Veri tabanında bir tablonun sadece kendine ait olan verilerinin saklanması hem mantıksal hem de yapısal olarak en doğrusu olacaktır. Bir tablo sadece kendine ait verileri
  barındırmalı, herhangi bir tutarsızlık olmamalıdır.
* Ana tablo ve yan tabloların arasındaki ilişkiyi tanımlamak için ** Pivot Tablo ** dediğimiz tabloları kullanabiliriz.
* Pivot Tablolar, tablondaki kaydın kendi id'si, ana tablodaki kaydın id'si ve yan tablodaki kaydın id'si kullanılarak oluşturulabilir. Bu tablonun içinde sadece id'leri tutarak
  tabloları ilişkilendirebilir ve sonrasında bu kayıtları join'leyerek istediğimiz verileri kullanabiliriz.
  
* Laravel'de **Eloquent ORM** kullanarak, veri tabanındaki tablolara karşılık gelen modeller oluştururuz ve veri katmanı olarak bu modelleri kullanırız.
  * Pivot tablolar için model oluşturmamıza gerek yoktur, Laravel'in bize sağladığı özellikleri kullanarak bu tablolarda CRUD işlemlerini gerçekleştirebiliriz.

* Laravel'de bir pivot tablo oluşturmak için önce migration klasörüne bir php dosyası açıyoruz. Bu isimlendirme anlamlı olmalı ve hangi tabloları birleştirdiğimizi belirtmeli.

* Pivot tablo adlandırması yaparken; (Değişken isimlendirmeye ve standartlara uymaya önem vermeliyiz.)
  1. Bütün karakterler küçük olmalı,
  2. Ana ve yan tablonun adı tekil olmalı,
  3. Tabloların arasına _ karakteri eklenmeli,
  4. Alfabetik sıraya göre tabloların isimleri yazılmalıdır.

```php
Schema::create('role_user', function(Blueprint $table) {
  $table->integer('user_id')->unsigned();
  $table->foreign('user_id')->references('id')->on('users');
  $table->integer('role_id')->unsigned();
  $table->foreign('role_id')->references('id')->on('roles');
});

```

* Pivot tabloları kullanabilmek için relationship özelliklerini kullanmamız gerekiyor, tablolar arasındaki ilişkiyi belirtelim.
  * User Model'in içerisine veya Role Model'in içerisine de ekleyebiliriz, belongsToMany() kullanarak bağlantımızı kuralım.

```php
User Model içerisine bu metodu ekleyelim.

public function roles() {
   return $this->belongsToMany(Role::class);
}

```

* Pivot tablomuzu oluşturup, tablolar arasında ilişkiyi kurduktan sonra, pivot tabloya kayıt eklemek için **attach** fonksiyonunu kullanıyoruz.
  * Aşağıdaki kod id'si 1 olan User'a, id'si 1 olan Rol'ü atar ve bu kaydı role_user pivot tablosuna ekler.

```php
$user = User::find(1);
$user->roles()->attach(1);

```

* Pivot tablodan kayıt silmek için **detach** fonksiyonunu kullanabiliriz.

```php
$user = User::find(1);
$user->roles()->detach(1);

```

* Pivot tablomuza çoklu kayıt eklemek için **sync** fonksiyonunu kullanabiliriz. Bu aynı zamanda güncelleme ve silme işlemini de yapar.
  * Örneğin sync([2, 3]); kullanırsak, pivot tabloda yan tablonun değerinin 2 ve 3 olan kayıtlar yoksa eklenir, eğer 2 ve 3 dışında kayıt varsa silinir. 

```php
$user = User::find(1);
$user->roles()->detach(1);

```

* Pivot tablomuzdaki veriyi çekmek için, aşağıdaki örneği kullanabiliriz.

```php
$user = User::find(1);

foreach($user->roles as $role) {
  echo $role->pivot->name;
}
```

* Pivot tablomuza id'ler dışında başka sütunlar eklemek için ilişkiyi yaptığımız ana tablonun modelini değiştirmemiz gerekmektedir.
  * Aynı zamanda pivot tabloyu migrate ettiğimiz dosyaya da sütunu eklememiz gerekmektedir.

```php
public function roles() {
   return $this->belongsToMany(Role::class)
               ->withTimeStamps(); // Laravel default olan created_at ve updated_at sütunlarını kullanmamız için ekleyebiliriz.
               ->withPivot(['name']); // Herhangi bir sütunu withPivot içerisine vereceğimiz dizi şeklinde isimler ile ekleyebiliriz.
}

```

* Pivot tablomuza birden çok sütunlu kayıt eklemek istersek, aşağıdaki kodlardan yararlanabiliriz.
  * created_at ve updated_at gibi varsayılan değerleri belli olan sütunlar için belirtmemize gerek yok.

```php
$user = User::find(1);
$user->roles()->attach(1, ['name' => 'Bir isim']);

// VEYA

$user = User::find(1);
$user->roles()->sync(1, ['name' => 'Bir isim']);

```

Bu ders notu [Laravel Daily](https://www.youtube.com/channel/UCTuplgOBi6tJIlesIboymGA) youtube kanalında **Povilas Korop** tarafından hazırlanan 
[Pivot Tables and Many-to-Many Relationships in Laravel](https://www.youtube.com/watch?v=akKWC_vP7sE) adlı videodaki bilgiler kullanılarak oluşturulmuştur.
























