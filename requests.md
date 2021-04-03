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



