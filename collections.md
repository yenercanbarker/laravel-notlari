## Collections

* **Illuminate\Support\Collection**, dizi şeklindeki verilerle işlemler yapmayı kolaylaştıran sınıftır.

* Örneğin **map** işlemi ile dizi içerisindekileri elemeanları formatlamak veya istemediğimiz verileri diziden çıkartmak basit bir şekilde yapılıabilir :

```php
$collection = collect(['bir','iki',null])->map(function ($element) {
  return strtoupper($element);
})->reject(function() {
  return empty($element);
});
```

* Collection bize zincirleme metot kullanabilme özelliğini, map ve reject özelliği ile elemanları dönüştürme ve eleman silme işlemlerini gerçekleştirmeyi
  sağlar.
  
* Genelde her Collection bize bir Collection instance'ı döndürür.

* Yukarıdaki örnekteki gibi **collect** helper'ını dizilerle kullanarak **Illuminate\Support\Collection** instance'ı oluşturabiliriz.

```php
$collection = collect([1, 2, 3]);
```

* Collection'lar Collection sınıfını oluştururken kullanabileceğimiz ekstra metotlar oluşturabilen **macroable** yapısını kullanır.
  * **Illuminate\Support\Collection** sınıfının **macro** metodunu kullanarak makro oluşturup istediğimiz zaman tetikeleyebileceğimiz bir yapı oluşturabiliriz.

```php
use Illuminate\Support\Collection;
use Illuminate\Support\Str;

Collection::macro('toUpper', function () {  // makro şeklinde toUpper oluşturuyoruz.
  return $this->map(function ($value) {  // collection elemanlarına map ile dönüşüm uyguluyoruz.
   return Str::upper($value);     // bütün elemanları büyük karaktere çeviriyoruz.
  });
});

$collection = collect(['first', 'second']);  // collection'ımızı oluşturuyoruz.

$upper = $collection->toUpper();  // elemanları büyük karaktere çeviren makromuzu çalıştırıyoruz.
```

* Genelde bu makro kodlarını **AppServiceProvider** class'ı içerisindeki **boot** metoduna yazarak, proje genelinde kullanabilir hale getirebiliriz.

* Collection'larda kullanılabilecek çok sayıda [metot](https://laravel.com/docs/8.x/collections#available-methods) var.

* **all()** collection içerisindeki bütün elemanları döndürür.

```php
collect([1, 2, 3])->all(); // method kullanımları bu şekilde, hepsini yazmamıza gerek yok.
```

* **avg()** collection içerisindeki elemanların ortalama değerini döndürür.
  *  Parametre olarak key alabilir, key arasındaki değerlerin ortalamasını döner.

* **chunk()** collection'ı parametrede verilen sayılar halinde böler.
  *  Parametre olarak eleman sayısı alır. Eleman sayısına göre dizi küçük parçalar haline getirilir.
  *  Boostrap ile beraber kullanılması mantıklı. 

```php
@foreach ($products->chunk(3) as $chunk)
  <div class="row">
    @foreach ($chunk as $product)
      <div class="col-xs-4">{{ $product->name }}</div>
    @endforeach
  </div>
@endforeach
```

* **chunkWhile()** koşul sağlanınca collection'ı böler.

```php
$collection = collect(str_split('AABBCCCD'));

$chunks = $collection->chunkWhile(function ($value, $key, $chunk) {
  return $value === $chunk->last();
});

$chunks->all();
```

* **collapse()** birden fazla elemanlı collection'ı tek bir collection'a dönüştürür.

* **collect()** collection oluşturmaya veya kopyalamaya yarar.

* **combine** iki collection'ı ilk değerler key, ikinci değerler value olacak şekilde birleştirir.

```php
$collection = collect(['name', 'age']);

$combined = $collection->combine(['George', 29]);

$combined->all();

// ['name' => 'George', 'age' => 29]
```

* **concat()** iki collection'ın değerlerini birleştirir.
  * Parametre olarak dizi alır, zincirleme kullanılabilir.

* **contains()** collection içerisinde aranan değerin var olup olmadığını döndürür.
  * true/false döndürür.
  * string şeklinde verilen bir sayı ile integer şeklinde verilen bir sayıyı aynı kabul eder.

```php
$collection->contains(function ($value, $key) {
    return $value > 5;
});

$collection->contains('Desk');

$collection = collect([
    ['product' => 'Desk', 'price' => 200],
    ['product' => 'Chair', 'price' => 100],
]);

$collection->contains('product', 'Bookcase');
```

* **containsStrict()** contains'in aynısı ancak burada tip kontrolü de var.

* **count()** collection'daki eleman sayısını verir.

* **countBy()** collection'daki hangi elemandan kaç tane olduğunu döndürür.
  * Çeşitli fonksiyonlarla beraber kullanarak ekstra verim alabiliriz.

```php
$collection = collect(['alice@gmail.com', 'bob@yahoo.com', 'carlos@gmail.com']);

$counted = $collection->countBy(function ($email) {
    return substr(strrchr($email, "@"), 1);
});

$counted->all();

// ['gmail.com' => 2, 'yahoo.com' => 1]
```

* **crossJoin()** iki collection değerleri arasında çaprazlama şekilde yeni collectionlar oluşturur.

* **dd()** collection'ın içeriğini ekrana basmak için kullanılır.
  * Program bu kodda çalışmayı durdurur, eğer çalışmaya devam etmek istiyorsak **dump** metodunu kullanabiliriz.

* **diff()** iki collection kullanılır, ilk collection'da olup ikincide olmayan elemanları döndürür.

* **diffAssoc()** iki collection'ı karşılaştırır ve ortak olan elemanları key valu şeklinde döndürür.

* **diffKeys()** ilk collection'da olup ikinci collection'da olmayan keyleri döndürür.

* **duplicates()** tekrar eden elemanları ve tekrar eden elemanın son indexini döndürür.
  * **duplicatesStrict()** tür kontrolü de yapar.

* **each()** fonksiyonla beraber kullaılır, bütün elemanlar sırayla dolaşılır, return false ile işlemi durdurabiliriz.

```php
$collection->each(function ($item, $key) {
  if (/* condition */) {
    return false;
  }
});
```

* **eachSpread** iç içe dizilerde bütün elemanlara ulaşılır, return false ile işlem durdurulabilir.

```php
$collection = collect([['John Doe', 35], ['Jane Doe', 33]]);

$collection->eachSpread(function ($name, $age) {
  //
});
```

* **every()** bütün elemanların belirtilen koşula uyup uymadığını kontrol etmek için kullanılır.
  * Eğer collection boşsa true döner.

```php
collect([1, 2, 3, 4])->every(function ($value, $key) {
  return $value > 2;
});
```

* **except()** bir collection'da belirtilen key/keyler haricindeki değerleri döndürür.
  * Çoklu keyler dizi şeklinde verilebilir.

* **filter()** bir collection'da belirtilen koşula uyan veriler döndürülür.
  * Eğer koşul belirtilmezse false, null ve boşluklar silinir/döndürülmez.
  
* **first()** bir collection'un ilk değerini döndürür.

* **firstWhere()** belirtilen koşula göre collection'daki ilk değeri döndürür.
  * Parametre olarak value veya key-value alır.
  * Parametre içerisinde koşul da kullanabiliriz.

* **flatMap()** çoklu dizileri tek collection'a çevirir ve map'leyebiliriz.

Bu ve bunlar gibi metotlara aşağıdaki linkten ulaşabiliriz.

Bu ders notu [Laravel'in orjinal dökümantasyonu](https://laravel.com/docs/8.x)'ndaki [Collections](https://laravel.com/docs/8.x/collections) bölümündeki
veriler kullanılarak hazırlanmıştır.
