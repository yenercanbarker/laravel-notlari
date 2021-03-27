## Excel Çıktısı Import / Export 

* Laravel'in kendi **export** özelliğini veya ek bir paket olarak **rap2hpoutre/fast-excel** paketini kullanabiliriz.
  * fast-excel paketi daha hızlı çıktı veriyor ve belleği daha az kullanıyor.

* Ek paketi kullanmak için paketin sayfasına gidip, installation adımlarını yapıyoruz.
  * [Paketin Sayfası](https://github.com/rap2hpoutre/fast-excel)

* Composer kodunu alıp terminalde çalıştırıyoruz.
* Daha sonra modelden veri alacaksak modelimizi oluşturuyoruz.

* Önce paketi kullanacağımız sayfaya dahil edelim.

```php 
 use Rap2hpoutre\FastExcel\FastExcel;
```

* Çıktı alma işlemi aşağıdaki örnekteki gibi yapılıyor.
  * public klasörü içine file.xlsx adında bir Excel dosyası oluşturup, verdiğimiz parametreyi(Collection veya Model) Excel formatına çevirerek içine ekler.

```php
(new FastExcel($users))->export('file.xlsx');
```

Bu ders notu [Laravel Daily](https://www.youtube.com/channel/UCTuplgOBi6tJIlesIboymGA) youtube kanalında **Povilas Korop** tarafından hazırlanan 
[FastExcel Package Laravel](https://www.youtube.com/watch?v=ULk-8kdKNsI) adlı videodaki bilgiler kullanılarak oluşturulmuştur.
