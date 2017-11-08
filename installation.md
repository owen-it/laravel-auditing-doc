# Installation

The Laravel Auditing package should be installed via [Composer](http://getcomposer.org/doc/00-intro.md).
To get the latest package version, run the following command from your project root:

```sh
composer require owen-it/laravel-auditing
```

> {tip} This package supports **Laravel** and **Lumen** from version 5.2 onwards.

# Upgrading
If you're [upgrading](upgrading), make sure the `audits` table schema is up to date!

>{tip} Changes to the table schema are needed when upgrading from versions `3.x.y` or `4.0.x`!

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

> {tip} If you're on version **5.5** or higher, you can skip this part of the setup in favour of the Auto-Discovery feature.

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

The `vendor:publish` command doesn't exist in Lumen, so an extra package must be installed:

```sh
composer require laravelista/lumen-vendor-publish
```

After the package is installed, the command must be registered in `app/Console/Kernel.php`:

```php
protected $commands = [
    \Laravelista\LumenVendorPublish\VendorPublishCommand::class,
];
```

> {note} The service provider registration is mandatory in order for the configuration to be published!

# Publishing
After configuring your framework of choice, use the following command to publish the configuration settings:

```sh
php artisan auditing:install
```

This will create the `config/audit.php` configuration file and a migration for the `audits` table in the `database/` directory.

You can read more about the configuration options in the [General Configuration](general-configuration) section.

# Database
If needed, the migration file can be customised. 
Have a look at the [Troubleshooting](troubleshooting) section for some of the changes that can be performed.

Once done, execute the migrate `artisan` command to create the `audits` table in your database:

```sh
php artisan migrate
```

This is where the `Audit` records will be stored, by default.

# Resolver
Finally, it's time to implement the `User` resolver.
Follow the instructions in the [General Configuration](general-configuration) section, and you're done!
