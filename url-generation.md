##URL Generation

* Laravel'in **url** helper'ı ile url oluşturabiliriz.

```php
  echo url("/posts");
```

* **Illuminate\Routing\UrlGenerator** instance'ı dönen **url** helper'ını kullanarak şu anki url'i, query stringli url'i ve bir önceki url'i görebiliriz.
  * Aşağıdaki fonksiyonlar **Facade** yardımı ile **URL::current()** şeklinde de kullanılabilir.

```php
  echo url()->current();
  echo url()->full();
  echo url()->previous();
```

* İsimlendirilmiş route'lara **route** helper'i ile erişerek url'leri alabiliriz.
  * Parametre gerektiren route'larda dizi içerisinde parametreler eklenebilir.
  
```php
  echo route('route-name');
  echo route('post.show', ['post' => 1]);
```


Bu ders notu Laravel'in dökümantasyonundaki URL Generation sayfasından alınan bilgilerden oluşturulmuştur.
