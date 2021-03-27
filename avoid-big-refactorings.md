## Büyük Değişimlerden Kaçınmak

Bir proje geliştirmeye başladığımızda başlangıçta her şey güzel gidiyor gibi gözükebilirken sonrasından işler çıkmaza girebilir. Buna neden olan sorunlar problemin yanlış anlaşımlması ,
yapının yanlış kurulması, gereksinimlerin değişmesi gibi nedenler olabilir. Bu gibi durumlarda projelerimizde değişiklik yapmamız kaçınılmaz olacaktır. Bu durumun önüne geçmek aşağıdaki
unsurlara dikkat etmemiz gerekmektedir.

* **Planlama** : Kod yazmaya başlamadan önce projenin ne olduğu, işlevsel olan/olmayan gereksinimlerin belirlenmesi, problemin ne olduğunun açıkça belirlenmesi gerekmektedir. 
Gereksinimlerin toplanmasından sonra veri tabanı oluşturup, hangi verileri hangi ilişkilerle kullanacağımızı belirleyebiliriz.
  * Planlama aşamasında müşterinin isteklerini belirlerken biraz teknik konularda müşteriye sorular sormak istekleri daha açık bir hale getirebilir. Örneğin projenin çoklu dil
  desteği olacak mı? Bunu baştan sorarsak yapıyı ona göre kurabiliriz, eğer sonradan böyle bir istek gelirse projede köklü değişiklikler yapmamız gerekecetir.
  
* Kod yazmaya başlamadan önce veri tabanı şemalarını oluşturmak daha iyi olabilir.

* **Laravel Özel** : Veri tabanındaki kayıtları çekerken where koşulu ile sorgulayacaksak bu işlemi Laravel **scope** kullanarak model içerisine ekleyebilir ve daha sonra bu
  metodu çağırarak arama koşullarını kullanabiliriz.
  * Bu şekilde bir kullanım yaparsak sorguyu çektiğimiz her yerde where koşulu içeriğini değiştirmek yerine, tek bir merkezden değiştirerek bütün sorgularda kullanabiliriz.
  
```php
public function scopeActive($query) {
   return $query->where('active', 1);
}

public function scopePopular($query) {
   return $query->where('votes', '>', 100);
}
```

* Scope'ları kullanırken :
```php
use App\Models\User;

$users = User::popular()->active()->get();
```

Bu ders notu [Laravel Daily](https://www.youtube.com/channel/UCTuplgOBi6tJIlesIboymGA) youtube kanalında **Povilas Korop** tarafından hazırlanan 
[Two Tips to Avoid Big Refactorings in Laravel](https://www.youtube.com/watch?v=m0nUNRKfspo) adlı videodaki bilgiler kullanılarak oluşturulmuştur.


