# Installation

The Laravel Auditing package should be installed via [Composer](http://getcomposer.org/doc/00-intro.md).

To get the latest package version, run the following command from your project root:

```sh
composer require owen-it/laravel-auditing
```

> {tip} This package supports **Laravel** and **Lumen** from version 5.1 onwards.

# Configuration

The Laravel and Lumen configurations vary slightly, so here are the instructions for each of the frameworks.

## Laravel
Edit the `config/app.php` file and add the following line to register the service provider:

```php
'providers' => [
    // ...
    OwenIt\Auditing\AuditingServiceProvider::class,
    // ...
],
```

## Lumen
Edit the `bootstrap/app.php` file and add the following line to register the service provider:

```php
$app->register(OwenIt\Auditing\AuditingServiceProvider::class);
```

You will also need to enable `Facades` and `Eloquent` in `bootstrap/app.php`:

```php
// ...

$app->withFacades();

$app->withEloquent();

// ...
```

> {note} The service provider registration is mandatory in order for the configuration to be published!

# Publishing
After configuring your framework of choice, use the following command to publish the configuration settings:

```sh
php artisan auditing:install
```

# Database
Finally, execute the migrate `artisan` command to create the `audits` table in your database:

```sh
php artisan migrate
```

This is where the Eloquent model audits will be stored, by default.
