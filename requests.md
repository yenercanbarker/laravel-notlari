## Request / İstekler

* İsteğin geldiği path'i öğrenmek için  **path()** fonksiyonu kullanılır.
  * http://example.com/foo/bar'dan gelen istek sonucu dönen değer : foo/bar

```php
$uri = $request->path();
```

* Bir isteğin geldiği route ve path'i istediğimiz değer mi diye kontrol etmek için : 
  * route name'i verilmiş olmalı.

```php
if ($request->is('admin/*')) {
    //
}

if ($request->routeIs('admin.*')) {
    //
}
```

* İsteğin geldiği url'i direkt almak için **url** fonksiyonunu kullanırız.
  * Parametreleri de istiyorsak **fullUrl()** kullanırız.

```php
$url = $request->url();
$urlWithQueryString = $request->fullUrl();
```

* İstersek **fullUrlWithQuery** fonksiyonunu kullanarak full url ve istediğimiz dizi şeklinde parametreleri de url'e ekleyebiliriz.

```php
$request->fullUrlWithQuery(['type' => 'phone']);
```

* Gelen isteğin hangi methodla geldiğini bulmak için **method** fonksiyonu kullanılır.
  * **isMethod** özelliği ile sorgulama yapabiliriz.

```php
$method = $request->method();

if ($request->isMethod('post')) {}
```

* **Illuminate\Http\Request**'in **header** methodunu kullanarak isteğin geldiği header'ı alabiliriz. Header gelmediyse null döner, ikinci parametre ekleyerek
null yerine bir değer döndürebiliriz.

```php
$value = $request->header('header-name');
$value = $request->header('header-name', 'default value');
```

* **hasHeader** kullanarak gelen request'te bu header var mı yok mu sorgulayabiliriz.

```php
if($request->hasHeader('header-name')) {}
```

* **bearerToken** methodu **Authorization** header'dan  bir bearer token getirmek için kullanılır.  Eğer böyle bir header yoksa, empty string döner.

```php
$token = $request->bearerToken();
```

* İsteğin geldiği ip adresini almak için **ip** fonksiyonu kullanılır.

```php
$ipAddress = $request->ip();
```

* Laravel **Accept** header'ında gelen istek türlerini inceler. **getAcceptableContentTypes** methodu ile kabul edilen header'ları görebiliriz.

```php
$contentTypes = $request->getAcceptablecontentTypes();
```

* **accepts** methodu, dizi şeklinde gelebilecek content type'ları alır. Bu type'lardan biri gelmişse true, gelmemişse false döndürür.
  * Benzer olarak **prefers** methodunu da kullanabiliriz, eşleşmezse null döndürür.

```php
if($request->accepts(['text/html', 'application/json'])) {}

$prefered = $request->prefers(['text/html', 'application/json']);
```

* Sadece **html** ve **json** gelecekse, **expectsJson** methodunu kullanabiliriz.

```php
if($request->expectsJson()) {}
```

* Input datasını array olarak almak için **all** methodunu kullanabiliriz.

```php
$input = $request->all();
```

* Bir input'un ismiyle direkt değerine erişebiliriz. **input** methodunu kullanırız.
  * ikinci parametre ekleyerek input data boş gelirse kullanabileceğimiz değeri ekleyebiliriz.

```php
$name = $request->input('name');

$name = $request->input('name', 'Sally');
```

* Query String'deki değerleri key, value şeklinde bir array olarak döndürmek için **query** methodu kullanılır.

```php
$query = $request->query();
```

* Query String'den bir veri almak için **query** methodu ve parametre olarak query string değişkeni kullanılır.

```php
$name = $request->query('name');
```

* Direkt bir id/name üzerinden bir dataya erişmek için **->id/name** kullanılır.

```php
$name = $request->name;
```

* **only** ve **expect** methodlarını kullarak gelebilecek dataları sınırlandırabiliriz.

```php
$input = $request->only(['username', 'password']);
$input = $request->except(['credit_card']);
```

* Input datasının gelip gelmediğini kontrol etmek için **has** methodu kullanılır.
  * Dizi şeklinde çoklu sorgulama yapabiliriz.

```php
if($request->has('name')) {}
```

* **whenHas** methodu ile, bir input'un olduğunda o input üzerinde oynamalar yapabiliriz.

```php
$request->whenHas('name', function($input) {})
```

* **has** methodu yerine **hasAny00 methodunu kullanabiliriz, herhangi bir input varsa true döndürür.

```php
if($request->hasAny(['name', 'email'])) {}
```

* **filled** methodu ile bir input'un gelip gelmediği ve değerinin olup olmadığı kontrol edilebilir.

```php
if($request->filled('name')) {}
```

* **whenFilled** methodu ile whenHas gibi kullanımı mevcut.

* **missing** methodu ile gelmeyen bir inputu kontrol edebiliriz. 

```php
if($request->missing('name')) {}
```

* Form'u gönderdiğimizde, form validation işlemi başarısız olursa Laravel bize gönderdiğimiz verileri tekrar kullanıp kullanıcıyı gösterebilmek için
**flash** fonksiyonunu kullanmamıza olanak sağlar.
  * **flash**, form verilerini session'a al.
  * **flashOnly**, forma istediğimiz(dizi şeklinde veriyoruz) verileri session'a al.
  * **flashExcept**, forma istemediğimiz değerler(dizi şeklinde veriyoruz) hariç kalan verileri session'a al.



```php
$request->flash();

$request->flashOnly(['username', 'email']);

$request->flashExcept('password');
```

* Redirect methodu ile birleştirerek **withInput** fonksiyonu ile redirect ve input verilerini kullanabiliriz.

```php
return redirect('form')->withInput(
    $request->except('password')
);
```

* Session'a aldığımız form değerlerini **old** methodu ile alabiliriz.
 
```php
$request->old('username');
```

* Laravel'in sağladığı **old** global helper'ı kullanarak blade içerisinde session'daki form verisini basabiliriz.

```php
<input type="text" name="username" value="{{ old('username') }}">
```

* Request ile dosya yüklemeleri de yapılabilir. **file** methodunu kullanarak dosyaları alabiliriz.

```php
$file = $request->file('image');
$file = $request->image;
```

* Bir input isminden dosya olup olmadığını anlamak için **hasFile** methodunu kulanabiliriz.
  * **isValid** methoduyla dosyanın gelip gelmediğini kontrol edebiliriz.

```php
if($request->hasFile('image')) {}

if($request->file('image')->isValid()) {}
```

* **path** methdou ile dosyanın yerini ve **extension** ile dosyanın uzantısını alabiliriz.

```php
$path = $request->photo->path();
$extension = $request->photo->extension();
```

 Bu not [Laravel 8](https://laravel.com/docs/8.x)'in orjinal dökümantasyonundaki, [HTTP Request](https://laravel.com/docs/8.x/requests) sayfasındaki bilgiler kullanılarak oluşturulmuştur.
















