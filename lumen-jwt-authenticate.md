## Lumen JWT Authenticate

* Lumen, Laravel'in microframework'üdür. API oluşturmak için kullanılabilir. Temelde Laravel ile aynı ancak kod yazımları farklı olabiliyor.

* Lumen kurulumu için terminalde bu komutları çalıştırabiliriz.

```
composer global require "laravel/lumen-installer"

lumen new auth-app

cd auth-app

php -S localhost:8000 -t public
```

* **.env** dosyasına kendi database ayarlarımızı ekleyelim.

* **bootstrap/app.php** içerisinde 

```php
// $app->withFacades();

// $app->withEloquent();

// comment satırlarını kaldıralım

$app->withFacades();
$app->withEloquent();
```

* Kullanıcılar için migration oluşturalım 

```
php artisan make:migration create_users_table --create=users
```

```php
public function up()
{
  Schema::create('users', function (Blueprint $table) {
    $table->bigIncrements('id');
    $table->string('name');
    $table->string('email')->unique()->notNullable();
    $table->string('password');
    $table->timestamps();
  });
}
```

```
php artisan migrate
```

* **routes/web.app** route'larımızı oluşturalım.

```php
// API route group
$router->group(['prefix' => 'api'], function () use ($router) {
   // Matches "/api/register
   $router->post('register', 'AuthController@register');
});
```

* **app/Http/Controllers/AuthController.php** Controller'ımızı oluşturalım.

```php
namespace App\Http\Controllers;

use Illuminate\Http\Request;
use  App\User;

class AuthController extends Controller
{
  /**
   * Store a new user.
   *
   * @param  Request  $request
   * @return Response
   */
  public function register(Request $request)
  {
    //validate incoming request 
    $this->validate($request, [
      'name' => 'required|string',
      'email' => 'required|email|unique:users',
      'password' => 'required|confirmed',
    ]);

    try {
      $user = new User;
      $user->name = $request->input('name');
      $user->email = $request->input('email');
      $plainPassword = $request->input('password');
      $user->password = app('hash')->make($plainPassword);
      $user->save();

      //return successful response
      return response()->json(['user' => $user, 'message' => 'CREATED'], 201);
    } catch (\Exception $e) {
      //return error message
      return response()->json(['message' => 'User Registration Failed!'], 409);
    }
  }
}
```

* **JWT** paketini projemize dahil edelim.

```
composer require tymon/jwt-auth:dev-develop
```

* **bootstrap/app.php** dosyasındaki değişiklikleri yapalım.

```php
// $app->register(App\Providers\AppServiceProvider::class);
$app->register(App\Providers\AuthServiceProvider::class);
// $app->register(App\Providers\EventServiceProvider::class);
$app->register(Tymon\JWTAuth\Providers\LumenServiceProvider::class);
```

* JWT tokenimizi oluşturalım.

```
php artisan jwt:secret
```

* **config/app.php** dosyasını oluşturalım.

```php
return [
  'defaults' => [
    'guard' => 'api',
    'passwords' => 'users',
  ],

  'guards' => [
    'api' => [
      'driver' => 'jwt',
      'provider' => 'users',
    ],
  ],

  'providers' => [
    'users' => [
       'driver' => 'eloquent',
       'model' => \App\User::class
    ]
  ]
];
```

* **User** model'ini oluşturalım.

```php
namespace App;

use Illuminate\Auth\Authenticatable;
use Laravel\Lumen\Auth\Authorizable;
use Illuminate\Database\Eloquent\Model;
use Illuminate\Contracts\Auth\Authenticatable as AuthenticatableContract;
use Illuminate\Contracts\Auth\Access\Authorizable as AuthorizableContract;


use Tymon\JWTAuth\Contracts\JWTSubject;

class User extends Model implements AuthenticatableContract, AuthorizableContract, JWTSubject
{
  use Authenticatable, Authorizable;

  /**
   * The attributes that are mass assignable.
   *
   * @var array
   */
  protected $fillable = [
    'name', 'email'
  ];

  /**
   * The attributes excluded from the model's JSON form.
   *
   * @var array
   */
  protected $hidden = [
    'password',
  ];

  /**
   * Get the identifier that will be stored in the subject claim of the JWT.
   *
   * @return mixed
   */
  public function getJWTIdentifier()
  {
    return $this->getKey();
  }

  /**
   * Return a key value array, containing any custom claims to be added to the JWT.
   *
   * @return array
   */
   public function getJWTCustomClaims()
   {
      return [];
   }
}
```

* **bootstrap/app.php** dosyasını düzenleyelim.

```php
//before
// $app->routeMiddleware([
//   'auth' => App\Http\Middleware\Authenticate::class,
// ]);

//After
$app->routeMiddleware([
  'auth' => App\Http\Middleware\Authenticate::class,
]);
```

* **routes/web.php** dosyasını güncelleyelim.

```php
// API route group
$router->group(['prefix' => 'api'], function () use ($router) {
  // Matches "/api/register
  $router->post('register', 'AuthController@register');

  // Matches "/api/login
  $router->post('login', 'AuthController@login');
});
```

* **Controller.php** dosyamıza global token fonksiyonumuzu yazalım.

```php
//import auth facades
use Illuminate\Support\Facades\Auth;

//Add this method to the Controller class
protected function respondWithToken($token)
{
  return response()->json([
    'token' => $token,
    'token_type' => 'bearer',
    'expires_in' => Auth::factory()->getTTL() * 60
  ], 200);
}
```

* **AuthController** dosyamıza **login** metodunu ekleyelim.

```php
//import auth facades
use Illuminate\Support\Facades\Auth;

/**
 * Get a JWT via given credentials.
 *
 * @param  Request  $request
 * @return Response
*/
    
public function login(Request $request)
{
  //validate incoming request 
  $this->validate($request, [
    'email' => 'required|string',
    'password' => 'required|string',
  ]);

  $credentials = $request->only(['email', 'password']);

  if (! $token = Auth::attempt($credentials)) {
    return response()->json(['message' => 'Unauthorized'], 401);
  }

  return $this->respondWithToken($token);
}
```

* **routes/web.php** dosyamızı düzenleyelim.

```php
// API route group
$router->group(['prefix' => 'api'], function () use ($router) {
  // Matches "/api/register
  $router->post('register', 'AuthController@register');
  // Matches "/api/login
  $router->post('login', 'AuthController@login');

  // Matches "/api/profile
  $router->get('profile', 'UserController@profile');

  // Matches "/api/users/1 
  //get one user by id
  $router->get('users/{id}', 'UserController@singleUser');

  // Matches "/api/users
  $router->get('users', 'UserController@allUsers');
});
```

* **UserController** dosyasını son haline getirelim.

```php
**namespace App\Http\Controllers;

use Illuminate\Support\Facades\Auth;
use  App\User;

class UserController extends Controller
{
  /**
   * Instantiate a new UserController instance.
   *
   * @return void
   */
  public function __construct()
  {
    $this->middleware('auth');
  }

  /**
   * Get the authenticated User.
   *
   * @return Response
   */
   public function profile()
   {
     return response()->json(['user' => Auth::user()], 200);
   }

  /**
   * Get all User.
   *
   * @return Response
   */
   public function allUsers()
   {
     return response()->json(['users' =>  User::all()], 200);
   }

  /**
   * Get one user.
   *
   * @return Response
   */
   public function singleUser($id)
   {
     try {
       $user = User::findOrFail($id);
       return response()->json(['user' => $user], 200);
     } catch (\Exception $e) {
       return response()->json(['message' => 'user not found!'], 404);
     }
   }
}
```

Bu ders notu [Build a JWT Authenticate API With Lumen](https://dev.to/ndiecodes/build-a-jwt-authenticated-api-with-lumen-2afm) adresindeki bilgiler kullanılarak hazırlanmıştır.
