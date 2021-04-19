## Yanıtlar / Responses

* Route'lar ve Controller'lar kullanıcının browser'ına göndermek için bir **response** döndürürler.
* Laravel, Response işlemleri için birden fazla seçenek sunmaktadır ve en basit olanlarından biri de text döndürmektir.
  * Aynı şekilde json formatıyla da gönderebiliyoruz.
  
```php
Route::get('/index', function () {
  return 'Hello World';
});
```

* Genelde yukarıdaki şekilde kullanmayız. Text veya array döndürmek yerine **Response** (Symfony\Component\HttpFoundation\Response) objesi döndürebiliriz.
  * Response instance'i döndürmek bize, **HTTP Status Code** ve **headers** değerlerini değiştirmemizi sağlar.

```php
Route::get('/home', function () {
    return response('Hello World', 200)
                  ->header('Content-Type', 'text/plain');
});
```

* Eloquent ORM Model'leri ve Collections döndürebiliriz. Laravel bu değerleri json formatında ekranda gösterir.
  * Eloquent Model'deki hidden değerler görüntülenmez.

```php
Route::get('/user/{user}', function (User $user) {
    return $user;
});
```

* Response'larda **header** methodu zincirleme olarak kullanılabilir. Zincirleme header yerine **withHeaders** methodunu kullanıp dizi şeklinde header'ları ekleyebiliriz.

```php
return response($content)
            ->header('Content-Type', $type)
            ->header('X-Header-One', 'Header Value')
            ->header('X-Header-Two', 'Header Value');

return response($content)
            ->withHeaders([
                'Content-Type' => $type,
                'X-Header-One' => 'Header Value',
                'X-Header-Two' => 'Header Value',
            ]);
```

* **Illuminate\Http\Response** instance'ı ile cookie methodunu kullanarak cookie oluşturabiliriz.

```php
return response('Hello World')->cookie(
    'name', 'value', $minutes
);
```

* Cookie facade'ını kullanarak cookie oluşturabiliriz. Queue methodu ile cookie'yi kuyruğa alırız. Bu cookie'ler Response tarayıcıya iletilmeden önce response'a eklenecek.

```php
use Illuminate\Support\Facades\Cookie;

Cookie::queue('name', 'value', $minutes);
```

* **withoutCookie** ve **forget** methodlarını kullanarak cookie'leri kaldırabiliriz.

```php
return response('Hello World')->withoutCookie('name');

Cookie::queue(Cookie::forget('name'));
```

* Global **redirect** hepler'ını kullanarak direkt yönlendirme yapabiliriz.

```php
Route::get('/test', function () {
  return redirect('/buraya-git');
});
```

* back helper'ını kullanarak geriye döndürebiliriz ve withInput ile formdaki verileri de döndürebiliriz.

```php
Route::post('/user/profile', function () {
    return back()->withInput();
});
```

* **Redirect** ve **route** methodlarını zincirleyerek, bir route'a yönlendirme yapabiliriz.
  * route methoduna ikinci parametre olarak dizi şeklinde route parametrelerini gönderebiliriz.

```php
return redirect()->route('login');
return redirect()->route('profile', ['id' => 1]);
```

* Redirect ile **away** methodunu zincirleyerek, farklı bir domain'e yönlendirme yapabiliriz.

```php
return redirect()->away('https://www.google.com');
```

* Redirect ile **with** methodunuz zincirleyerek, data gönderebiliriz.
  * Bu değerleri **session** ile alabiliriz.

```php
return redirect('dashboard')->with('status', 'Profile updated!');
```

* View döndürebiliriz, JSON döndürebiliriz, download ile dosya indirmesi yapılabilir.

```php
return response()
            ->view('hello', $data, 200)
            ->header('Content-Type', $type);

return response()->json([
    'name' => 'Abigail',
    'state' => 'CA',
]);

return response()->download($pathToFile);
```

* File ile dosya döndürüp, tarayıcıda görüntüleyebiliriz.

```php
return response()->file($pathToFile);
```

Bu ders not [Laravel 8](https://laravel.com/docs/8.x)'in orjinal dökümantasyonundaki [Responses](https://laravel.com/docs/8.x/responses) bölümündeki veriler kullanılarak oluşturulmuştur.


