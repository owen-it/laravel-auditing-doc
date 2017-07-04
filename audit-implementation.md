# Audit Implementation
From version **4.1** onward, an `Audit` class can extend other implementations besides `Illuminate\Database\Eloquent\Model`.

## MongoDB auditing

In the following example, the `Audit` class implementation extends `Jenssegers\Mongodb\Eloquent\Model`.

```php
<?php
namespace App\Models;

use Jenssegers\Mongodb\Eloquent\Model;
use OwenIt\Auditing\Audit as AuditTrait;
use OwenIt\Auditing\Contracts\Audit as AuditContract;

class Audit extends Model implements AuditContract
{
    use AuditTrait;

    /**
     * {@inheritdoc}
     */
    protected $guarded = [];

    /**
     * {@inheritdoc}
     */
    protected $casts = [
        'old_values' => 'json',
        'new_values' => 'json',
    ];
}
```

## Defining which Audit implementation to use

In order to use an `Audit` model, the FQCN must be set in the `config/audit.php` configuration.
If the value is missing from the configuration, the package will default to `OwenIt\Auditing\Models\Audit`.

Example, of how to set the previous `Audit` implementation for MongoDB:
```
return [
    // ...

    'implementation' => App\Models\Audit::class,

    // ...
];
```
