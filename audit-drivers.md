# Audit Drivers
Drivers have the audit logic for `Auditable` models.
Out of the box, the Laravel Auditing package includes a `Database` driver.

Besides storing model attribute changes, drivers also handle pruning when an audit threshold is set.

While for most use cases the `Database` driver should suffice, the community has been working to provide alternative drivers as well. Finally, should you need to write a completely custom driver - this is also catered for.

## Community Drivers
 Package                              | Repository                                              | Description
:-------------------------------------|:--------------------------------------------------------|:-----------
 betapeak/laravel-auditing-filesystem | https://github.com/betapeak/laravel-auditing-filesystem | Allows you to store your audits into a CSV file, using any storage disk in your Laravel application.
 Iconscout/laravel-auditing-elasticsearch | https://github.com/Iconscout/laravel-auditing-elasticsearch | This driver provides the ability to save your model audits in elasticsearch.

## Creating a custom Driver
A driver is just a class that implements the `AuditDriver` interface.
Usually, a custom driver resides in the `app/AuditDrivers` folder of a Laravel/Lumen project.

To create a new driver, run the following command:

```sh
php artisan make:audit-driver MyCustomDriver
```

The above command will place a file called `MyCustomDriver.php` in the `app/AuditDrivers` folder with the following content:

```php
<?php
namespace App\AuditDrivers;

use OwenIt\Auditing\Contracts\Audit;
use OwenIt\Auditing\Contracts\Auditable;
use OwenIt\Auditing\Contracts\AuditDriver;

class MyCustomDriver implements AuditDriver
{
    /**
     * Perform an audit.
     *
     * @param \OwenIt\Auditing\Contracts\Auditable $model
     *
     * @return \OwenIt\Auditing\Contracts\Audit
     */
    public function audit(Auditable $model): Audit
    {
        // TODO: Implement the audit logic
    }

    /**
     * Remove older audits that go over the threshold.
     *
     * @param \OwenIt\Auditing\Contracts\Auditable $model
     *
     * @return bool
     */
    public function prune(Auditable $model): bool
    {
        // TODO: Implement the pruning logic
    }
}
```

> {tip} The `Database` driver is a good starting point to get ideas for a new custom driver implementation.

## Using a custom driver
There are two ways of enabling a different audit driver.

### Globally
This is done in the `config/audit.php` configuration file.

```php
return [
    // ...

    'driver' => App\AuditDrivers\MyCustomDriver::class,

    // ...
];
```

In this example, the previously created `MyCustomDriver`, is set as the default audit driver for all `Auditable` models.

### Locally
This is done on a per `Auditable` model basis, by assigning the `FQCN` value of the driver to the `$auditDriver` attribute.

```php
<?php
namespace App\Models;

use App\AuditDrivers\MyCustomDriver;
use Illuminate\Database\Eloquent\Model;
use OwenIt\Auditing\Contracts\Auditable;

class Article extends Model implements Auditable
{
    use \OwenIt\Auditing\Auditable;

    /**
     * Custom Audit Driver
     *
     * @var \App\AuditDrivers\MyCustomDriver
     */
    protected $auditDriver = MyCustomDriver::class;

    // ...
}
```

> {tip} A locally defined driver **always** takes precedence over any globally defined one.
