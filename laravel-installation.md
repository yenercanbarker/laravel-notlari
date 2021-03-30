## Laravel Kurulumu

* Laravel'in **Composer** ile kurulması için aşağıdaki adımları uyguluyoruz.
  * Öncelikle [Composer'ı](https://getcomposer.org/download/) indirelim.

* Composer kurulumu tamamlandıktan sonra Terminal'den Laravel'i kurmak istediğimiz dosyaya geçiyoruz. Sırasıyla aşağıdaki komutları çalıştırıyoruz.

```
composer create-project laravel/laravel example-app
```
```
cd example-app
```
```
php artisan serve
```

* Bu kurulum Laravel'in kaynak kodlarından dosyaları çeker ve projemiz için bir klasör oluşturur.
  * Bunun yerine bir defa Laravel kodlarını çekip kısayoldan projeler oluşturabiliriz.

* Laravel'i global olarak kurmak için aşağıdaki komutları çalıştırmamız gerekir.

```
composer global require laravel/installer
```
```
laravel new example-app
```
```
cd example-app
```
```
php artisan serve
```

* Global kurulum yaptığımızda $PATH değişkenimizde composer vendor klasörünü görmemiz lazım yoksa proje oluşturamayız.

* Laravel kurulumu yaparken aynı zamanda **Git Repository** oluşturabiliriz, branch seçebiliriz.
```
laravel new example-app --git
```
```
laravel new example-app --git --branch="main"
```

* Eğer **GitHub CLI** kuruluysa, giriş yapılmışsa ve bilgisayarımızda **git** kuruluysa, Laravel projesi oluştururken **GitHub Repository**'de oluşturabiliriz.
```
laravel new example-app --github="--public"
```
