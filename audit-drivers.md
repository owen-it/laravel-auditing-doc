# Audit Drivers

Drivers have the audit logic for `Auditable` models.
Out of the box, the Laravel Auditing package includes a `Database` driver.

Besides storing model attribute changes, drivers also handle pruning when an audit threshold is set.

While for most use cases the `Database` driver should suffice, the need for something custom is also catered for.

## Creating a custom Driver

A driver is just a class that implements the `AuditDriver` interface.
Usually, the custom drivers are stored in the `app/AuditDrivers` folder of a Laravel/Lumen project.

To create a new driver, run the following command:

```sh
php artisan make:audit-driver MyCustomDriver
```

The above command will place a file called `MyCustomDriver.php` in the `app/AuditDrivers` folder with the following content:

```php
<?php

namespace App\Drivers;

use OwenIt\Auditing\Contracts\Auditable;
use OwenIt\Auditing\Contracts\AuditDriver;
use OwenIt\Auditing\Models\Audit;

class MyCustomDriver implements AuditDriver
{
    /**
     * Perform an audit.
     *
     * @param \OwenIt\Auditing\Contracts\Auditable $model
     *
     * @return \OwenIt\Auditing\Models\Audit
     */
    public function audit(Auditable $model)
    {
        // Implement audit logic
    }

    /**
     * Remove older audits that go over the threshold.
     *
     * @param \OwenIt\Auditing\Contracts\Auditable $model
     *
     * @return bool
     */
    public function prune(Auditable $model)
    {
        // Implement pruning logic
    }
}
```

> {tip} The `Database` driver is a good starting point to get ideas for a new custom driver implementation.

## Using a custom driver

Once the audit logic is put in place, here is how to set the `MyCustomDriver` driver as the default driver in an `Auditable` model:

```php
<?php
namespace App\Models;

use App\AuditDrivers\MyCustomDriver;
use Illuminate\Database\Eloquent\Model;
use OwenIt\Auditing\Auditable;
use OwenIt\Auditing\Contracts\Auditable as AuditableContract;

class SomeModel extends Model implements AuditableContract
{
    use Auditable;

    /**
     * Custom Audit Driver
     *
     * @var \App\AuditDrivers\MyCustomDriver
     */
    protected $auditDriver = MyCustomDriver::class;

    // ...
}
```
