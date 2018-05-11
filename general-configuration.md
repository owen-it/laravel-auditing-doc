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

## User
Version **7.0.0** brings significant changes to the `User` configuration.
The `primary_key`, `foreign_key` and `model` are no longer necessary, giving way to `morph_prefix` and `guards`.

### Morph prefix
The `User` relation has been modified from `BelongsTo` to `MorphTo`, allowing for multiple user types.
By default, the column names used are `user_id` and `user_type`. For different column names, change the prefix value and update the [migration](audit-migration) accordingly.

```php
return [
    // ...

    'user' = [
        'morph_prefix' => 'user',
    ],

    // ...
];
```

> {note} The `Audit` **resolveData()** method will still use `user_id` and other `user_` prefixed keys for any `User` data, regardless of the morph prefix set.

### Auth Guards
Specify which authentication guards the `UserResolver` should check in order to resolve a `User`.

```php
return [
    // ...

    'user' = [
        'guards' => [
            'web',
            'api',
        ],
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

## Console/CLI and Jobs

Eloquent events fired from a **Job** or from the **console** (i.e. migrations, tests, commands, Tinker, ...), **WILL NOT** be audited by default.
To enable console auditing, set the `console` value to `true`.

```php
return [
    // ...

    'console' => true,

    // ...
];
```

> {note} Resolving a `User` in the console may not work.
