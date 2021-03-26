## Yorum Satırları

PHPStorm CTRL + / ile tek satır comment oluşturabiliriz, veya // /* */

* Kod yazacağımız projelerde kodlamaya geçmeden önce o sayfada neler yapacağımızı, geliştireceğimiz kod parcacağının ne gibi yerlerde nasıl kullanılacağını yorum satırlarıyla 
  önceden yazarsak (todo list gibi düşünebiliriz) bu kod yazarken bize kolaylık sağlayacaktır.

* Laravelde veya herhangi bir dilde yorum satırları yazarken özellikle fonksiyon/method geliştiriyorsak fonksiyonun ne iş yaptığını, alacağı parametreleri ve geri dönüş değerinin
  tipini belirtmek hem okunaklığı arttıracaktır, hem de projenin açıkça anlaşılabilmesini sağlayacaktır.

```
/* Bu fonksiyon bu işlevi yapmaktadır.
 * @parameter int $sayi1
 * @parameter int $sayi2
 * @returns int $toplam
 */

 function($sayi1, $sayi2) {
   $toplam = $sayi1 + $sayi2;
   return $toplam;
 }
```
  
