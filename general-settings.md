# General settings

The default auditing behavior can be changed by overriding the settings in the configuration file, which can be found at `config/audit.php`.

## User Model & Resolver

Specify the `User` model and resolver `callable`.

### Model
The model value should be set to the fully qualified `User` class name the application is using.

```php
return [
    'user' = [
        'model' => App\User::class,
    ],
];
```

### Resolver
A resolver is a `callable` that returns the **ID** of the currently logged `User`, or `null` if the `User` could not be resolved.

The default resolver uses the Laravel `Auth`.

```php
return [
    'user' = [
        'resolver' => function () {
            return auth()->check() ? auth()->user()->getAuthIdentifier() : null;
        },
    ],
];
```

When using other authentication mechanisms like [Sentinel](https://github.com/cartalyst/sentinel), change the resolver accordingly.

## Audit driver

Being the only driver provided, the `Database` driver is set as default.

The `Database` driver allows modifying:
- The database connection.
- The table where the `Audit` records are stored.

## Console

Eloquent events that are fired when running in the console, are not audited by default.
To enable console auditing, set the `'console'` value to `true`.

```php
return [
    'console' => true,
];
```

> {note} Resolving a `User` in the console may not work.
