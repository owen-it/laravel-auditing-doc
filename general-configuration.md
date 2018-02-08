# General Configuration
The default auditing behavior can be altered by updating the settings in the configuration file, which can be found in `config/audit.php`.

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

## User Keys & Model
Specify the `User` keys and model.

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
