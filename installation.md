# Installation

Laravel Auditing can be installed with [Composer](http://getcomposer.org/doc/00-intro.md), more details about this package in Composer can be found [here](https://packagist.org/packages/owen-it/laravel-auditing).

Run the following command to get the latest version package:

```
composer require owen-it/laravel-auditing
```

## With Laravel
Open the file ```config/app.php``` and then add the service provider, this step is required.

```php
'providers' => [
    // ...
    OwenIt\Auditing\AuditingServiceProvider::class,
],
```
> Note: This provider is important for the publication of configuration files.

Only after complete the step before, use the following command to publish configuration settings:

```
php artisan vendor:publish --provider="OwenIt\Auditing\AuditingServiceProvider"
```
Finally, execute the migration to create the ```logs``` table in your database. This table is used to save audit the logs.

```
php artisan migrate
```

## With Lumen 
> Note: Implementation with lumen is available from the 2.3.7 version of Laravel Auditing.

Open the file ```bootstrap/app.php``` and then add the service provider, this step is required.

```php
$app->register(OwenIt\Auditing\AuditingServiceProvider::class);
```
You should uncomment the $app->withFacades() and $app->withEloquent() call in your `bootstrap/app.php` file.
```php
// ...
$app->withFacades();

$app->withEloquent();
// ...
```

Then execute the migration to create the ```logs``` table in your database. This table is used to save audit the logs.
```
php artisan migrate
```

Once Laravel Auditing is installed, you can [get started](/docs/{{version}}/introduction).
