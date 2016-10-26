
# Installation

Laravel Auditing can be installed with [Composer](http://getcomposer.org/doc/00-intro.md). More details about this package in Composer can be found [here](https://packagist.org/packages/owen-it/laravel-auditing).

Run the following command in your project to get the latest version of the package:

```
composer require owen-it/laravel-auditing
```

You can use Laravel Auditing with **Laravel** as well as **Lumen**.

### With Laravel
Open the file ```config/app.php``` and then add following service provider.

```php
'providers' => [
    // ...
    OwenIt\Auditing\AuditingServiceProvider::class,
],
```
> Note: This step is required for the publication of configuration files.

After completing the step above, use the following command to publish configuration settings:

```
php artisan vendor:publish --provider="OwenIt\Auditing\AuditingServiceProvider"
```

Finally, execute the migration to create the ```audits``` table in your database. This table is used to save audit the logs.

```
php artisan auditing:table

php artisan migrate
```

### With Lumen 
> Note: Implementation with lumen is available from the 2.3.7 version of Laravel Auditing.

Open the file ```bootstrap/app.php``` and then add following service provider. This step is required.

```php
$app->register(OwenIt\Auditing\AuditingServiceProvider::class);
```

You should also uncomment the $app->withFacades() and $app->withEloquent() call in your `bootstrap/app.php` file.

```php
// ...

$app->withFacades();

$app->withEloquent();

// ...
```

Then execute these commands to create the ```logs``` table in your database. This table is used to save the audit logs.

```
php artisan auditing:table

php artisan migrate
```

Once Laravel Auditing is installed, you can [start using it](/docs/{{version}}/introduction) in your models.
