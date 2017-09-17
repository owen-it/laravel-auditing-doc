# General settings

The default auditing behavior can be changed by overriding the settings in the configuration file, which can be found in `config/audit.php`.

## Audit implementation
From version **4.1**, the audit implementation can be configured.

By default, the package will use `OwenIt\Auditing\Models\Audit`.

```
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
        // ...
        'primary_key' => 'id',
        'foreign_key' => 'user_id',
        // ...
    ],
    // ...
];
```

> {note} The `Audit` **resolveData()** method will still use `user_id` and other `user_` prefixed keys for any `User` data, regardless of the foreign key that was set.

### Model
The model value should be set to the fully qualified `User` class name the application is using.

```php
return [
    // ...
    'user' = [
        // ...
        'model' => App\User::class,
        // ...
    ],
    // ...
];
```

### Resolver
A resolver can be a `callable` or a [FQCN](http://php.net/manual/en/language.namespaces.rules.php) implementing `OwenIt\Auditing\Contracts\UserResolver`.

> {note} The next major version won't support the resolver logic as a `callable`.

#### Fully Qualified Class Name

This is the preferred way to configure a resolver, since using a `callable` causes problems when caching the configuration.
The class implementing `OwenIt\Auditing\Contracts\UserResolver` only needs a `resolveId()` method with the logic to resolve a logged `User`.

The `resolveId()` method should return the **ID** of the currently logged `User`, or `null` if the `User` could not be resolved.

Configuration:
```php
return [
    // ...
    'user' = [
        // ...
        'resolver' => App\User::class,
        // ...
    ],
    // ...
];
```

Implementation:
```php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;
use OwenIt\Auditing\Auditable;
use OwenIt\Auditing\Contracts\Auditable as AuditableContract;
use OwenIt\Auditing\Contracts\UserResolver;

class User extends Model implements AuditableContract, UserResolver
{
    use Auditable;

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

#### Callable
While still supported, using a `Closure` as a resolver isn't encouraged, due to problems with configuration caching.

The `callable` should return the **ID** of the currently logged `User`, or `null` if the `User` could not be resolved.

```php
return [
    // ...
    'user' = [
        // ...
        'resolver' => function () {
            return Auth::check() ? Auth::user()->getAuthIdentifier() : null;
        },
        // ...
    ],
    // ...
];
```

> {tip} When using other authentication mechanisms like [Sentinel](https://github.com/cartalyst/sentinel), change the resolver logic accordingly.

## Audit driver

Being the only driver provided, the `Database` driver is set as default.

```php
return [
    // ...
    'default' => 'database',
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
