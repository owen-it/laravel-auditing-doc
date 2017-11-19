# General Configuration
The default auditing behavior can be altered by updating the default settings in the configuration file, which can be found in `config/audit.php`.

## Audit implementation
Since version **4.1.0**, the audit implementation can be configured.

By default, the package will use `OwenIt\Auditing\Models\Audit`.

```php
return [
    // ...

    'implementation' => OwenIt\Auditing\Models\Audit::class,

    // ...
];
```

Read more about it in the [Audit Implementation](audit-implementation) section.

## User Keys, Model & Resolver
Specify the `User` keys, model and resolver.

### Primary and Foreign Keys
Sometimes, for whatever reason (legacy database, etc), a different key needs to be used.
The default primary and foreign key values are `id` and `user_id`, respectively.

```php
return [
    // ...

    'user' = [
        'primary_key' => 'id',
        'foreign_key' => 'user_id',
    ],

    // ...
];
```

> {note} The `Audit` **resolveData()** method will still use `user_id` and other `user_` prefixed keys for any `User` data, regardless of the foreign key that was set.

### Model
The model value should be set to the [FQCN](http://php.net/manual/en/language.namespaces.rules.php) of the `User` class used by the application.

```php
return [
    // ...

    'user' = [
        'model' => App\User::class,
    ],

    // ...
];
```

### Resolver
A resolver is a class implementing the `OwenIt\Auditing\Contracts\UserResolver` contract.

The only thing needed is a public static `resolveId()` method with the logic to resolve a logged `User`.

The `resolveId()` method should return the **ID** of the currently logged `User`, or `null` if the `User` could not be resolved.

```php
<?php
namespace App;

use Illuminate\Database\Eloquent\Model;
use OwenIt\Auditing\Contracts\Auditable;
use OwenIt\Auditing\Contracts\UserResolver;

class User extends Model implements Auditable, UserResolver
{
    use \OwenIt\Auditing\Auditable;

    /**
     * {@inheritdoc}
     */
    public static function resolveId()
    {
        return Auth::check() ? Auth::user()->getAuthIdentifier() : null;
    }

    // ...
}
```

> {tip} When using other authentication mechanisms like [Sentinel](https://github.com/cartalyst/sentinel), update the resolver logic accordingly.

The resolver is defined in the `config/audit.php` configuration file. This is done by assigning the FQCN value of class implementing it to the `user.resolver` index.

```php
return [
    // ...

    'user' = [
        'resolver' => App\User::class,
    ],

    // ...
];
```

> {note} Support for `Closure`/`callable` resolvers has been removed from version **5.0.0** onwards.

## Audit driver
Being the only driver provided, the `Database` driver is set as default.

```php
return [
    // ...

    'driver' => 'database',

    // ...
];
```

The `Database` driver allows modifying:
- The database connection.
- The table where the `Audit` records are stored.

```php
return [
    // ...

    'drivers' => [
        'database' => [
            'table'      => 'audits',
            'connection' => null,
        ],
    ],

    // ...
];
```

## Console

Eloquent events that are fired when running in the console, are not audited by default.
To enable console auditing, set the `console` value to `true`.

```php
return [
    // ...

    'console' => true,

    // ...
];
```

> {note} Resolving a `User` in the console may not work.
