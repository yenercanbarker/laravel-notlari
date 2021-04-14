## Guidelines / Yönergeler

* **Consistency is key.** - Tutarlılık anahtardır.

* Çoğu proje tek bir kişi tarafından oluşturulmaz veya düzenlenmez. Bunun yerine kendi fikirleri, tercihleri olan birden çok insan topluluk halinde bir projede 
bulunurlar. Her insanın kendine özgü alışkanlıkları ve fikirleri vardır. Bir projede bir insan kendi düşüncesi ve alışkanlıklarıyla hareket ederse ve projenin
büyük ölçekli olduğunu düşünürsek işin içinden çıkılamayacak bir duruma gelmek neredeyse kaçınılmazdır.

* Projenin ekibinde bulunan insanların konuşarak, tartışarak, fikir alışverişinde bulunarak bir standarta bağlaması ve projenin içerisinde bu standartlara 
uyulması tutarlılık sağlar ve bakım sürecini kolaylaştırır.

* İnsanlar, takımlar, ekipler ve tercihler zaman içerisinde değişebilir. Aynı zamanda standartlar da projelere göre değişebilir.
Bir projeye başlamadan önce standartların belirlenmesi önemlidir. Geliştiriciler bu kurallara uyarsa proje çok daha güzel bir yapıda oluşur.

* **Spatie** takımı kendilerine bir standartlar oluşturmuşlar. PHP ve Laravel özelinde aşağıdaki maddeler best practices olarak kullanılabilir.
  * Değişken tanımlarken camelCase kullanıyorlar.
  
  * Nullable olabilecek değerler için kısaltılmış yazım kullanılıyor.
  
  ```php
  public ?string $variable;
  ```
  
  * Void döndürecek methodlar/fonksiyonlara : void ekleyerek dönüş tipini belirtiyorlar.
  
  ```php
  public function method(): void
  {
  
  }
  ```
  
  * Bir değişkenin tipini belirleyebiliyorsalar tanımlarken yazıyorlar.
  
  ```php
  class Foo
  {
    public string $bar;
  }
  ```
  
  * Ekstra bilgi vermeleri gereken method/fonksiyonlar hariç **docblocks** (yorum satırı) yazmıyorlar.
    * Bir methodun alacağı parametre önüne tipini, döneceği değeri : ile fonksiyonun sonuna ve ismini fonksiyonu açıklayacak şekilde verdikten sonra
    yorum satırı yazmamıza zaten gerek kalmıyor.
   
  ```php
  public static function fromString(string $url): Url
  {
      // ...
  }
  ```
  
  * Eğer dockblocks kullanacaklarsa, kullanılacak değerlerin full path'ini veriyorlar.
  
  * Çoklu ve boşluklu yorum satırları fazla dikkat çekiyor, eğer tek satırda yazılmaları mümkünse tek satırda yazıyorlar.

  * Bir class içerisinde property oluştururken, Constructorda tanımlanacakları direkt Constructor içerisinde tanımlıyorlar.

  ```php
  class MyClass {
    public function __construct(
        protected string $firstArgument,
        protected string $secondArgument,
    ) {}
  }
  ```
  
  * Bir değişkeni string'in içerisinde yazarken . yerine { } kullanıyorlar.

  ```php
  $greeting = "Hi, I am {$name}.";
  ```
  
  * Ternary Operator kullanacakları zaman, uzunluk farketmeksizin tek satırda yazıyorlar.

   ```php
   $name = $isFoo ? 'foo' : 'bar';
   ```
   
  * If koşullarını her zaman { } ile kullanıyorlar.

  ```php
  if ($condition) {
   
  }
  ```
  
  * İç içe if-else kullanmak okunurluluğu azaltmaktadır. Olumsuz koşulları önce yazıp, return ile hatayı döndürüyoruz. 
  Böylece iç içe if-else yerine düzenli if koşulları kullanarak kodun okunulabilirliğini arttırabiliriz.
  
  ```php
  if (! $goodCondition) {
    throw new Exception;
  }
  ```
  
  * Değişkenlerin, fonksiyonların veya methodların açıklamasını yapmak için uzun yorum satırları yazıyorsak, değişkenlere verdiğimiz
  isimleri değiştirmeye ve daha anlamlı isimler vererek yorum satırlarını ortadan kaldırmayı veya kısaltmayı sağlayabiliriz.
  
  * Kod blokları arasında boşluklar bırakmaya özen gösteriyorlar.
    * Değişkenlerin tanımlanmasından sonra bir satır boşluk var.
    * If blokları, döngü blokları gibi bloklardan sonra bir satır boşluk var.
    * return'den önce bir satır boşluk var.
  
  * Configuration dosyaları kebab-case, Configuration key'leri snake_case şeklinde yazılıyor.

  * Artisan komutlarını kebab-case ile kullanıyorlar.

  * Route url'ler kebab-case olmalı.

  * Url boş olmadığı sürece / kullanmıyorlar (en başta).

  * Controller ismi plural olmalı. (s / es almalı)

  * View dosyaları camelCase olmalı.

  * Laravel validation kullanırken | ile sıralamak yerine dizi içerisinde kullanıyorlar.

  ```php
  public function rules()
  {
    return [
        'email' => ['required', 'email'],
    ];
  }
  ```
  
  
  
Bu notlar [Spatie](https://spatie.be/)'nin [PHP & Laravel Guidelines](https://spatie.be/guidelines/laravel-php) sayfasındaki bilgiler kullanılarak oluşturulmuştur.
  
  
  
  
  
  
