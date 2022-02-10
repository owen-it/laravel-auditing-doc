# Audit Resolvers
A resolver is a class that implements the `\OwenIt\Auditing\Contracts\Resolver` contract, except the UserResolver, which implements `OwenIt\Auditing\Contracts\UserResolver`.

Each resolver must have a **public static** `resolve()` method with the appropriate logic.

The resolvers add (meta-)data to the corresponding column in the audit table. Laravel-audit ships with 4 resolvers by default which adds data to the 'url', 'ip_address', 'user_agent' and user morph columns.

## Custom resolvers
Since version 13.0, you may add or remove resolvers as relevant. For example: 
 - If you do not need the user agent in the audit table, you can remove the user agent from the resolvers array in audit.php config file and entirely drop the column from the table
 - If you want to add another meaningful piece of information to the table, you can create a new resolver, and just add it to the array in audit.php
```php
'resolvers' => [
    'ip_address' => OwenIt\Auditing\Resolvers\IpAddressResolver::class,
    'user_agent' => OwenIt\Auditing\Resolvers\UserAgentResolver::class,
    'url'        => OwenIt\Auditing\Resolvers\UrlResolver::class,
    'tenant_id' => \App\AuditResolvers\TenantIdResolver::class // resolver that sets a tenant_id on the audits
],
```
The resolver key in the array corresponds to the column name in the table. In this example a column name "tenant_id" is needed.

To create a custom resolver, create a class that implements the `OwenIt\Auditing\Contracts\Resolver` interface, and give it a resolve method. `artisan auditing:audit-resolver ` will create one for you.

The auditable eloquent model is passed to the resolver so that you can set values from not only the laravel environment, but also from the specific Auditable.

Here we check if the auditable has a tenant method, which signals to us that it has a tenant_id.
```php
<?php

namespace App\AuditResolvers;

use OwenIt\Auditing\Contracts\Auditable;
use OwenIt\Auditing\Contracts\Resolver;

class TenantIdResolver implements Resolver
{

    public static function resolve(Auditable $auditable = null)
    {
        if(method_exists($auditable, 'tenant')) {
            return $auditable->tenant_id;
        }

        return null;
    }
} 
```

Now records in audit table can be filtered by tenant.

## IP Address Resolver
By default, this resolver uses the `Request::ip()` method to get the current IP address.

Most of the times, this will get the correct value, but for applications running behind a proxy or a [load balancer](https://en.wikipedia.org/wiki/Load_balancing_(computing)), IP addresses must be obtained differently.

Usually, the real IP address will be passed via an **X-Forwarded-For** HTTP header.

```php
<?php
namespace App\Resolvers;

use Illuminate\Support\Facades\Request;

class IpAddressResolver implements \OwenIt\Auditing\Contracts\Resolver
{
    /**
     * {@inheritdoc}
     */
    public static function resolve(): string
    {
        return Request::header('HTTP_X_FORWARDED_FOR', '0.0.0.0');
    }
}
```

Set the custom _IP Address_ resolver in the `config/audit.php` configuration file:

```php
return [
    // ...

    'resolvers' = [
        // ...
        'ip_address' => App\Resolvers\IpAddressResolver::class,
        // ...
    ],

    // ...
];
```

## URL Resolver
This resolver uses the `Request::fullUrl()` method to get the current URL (including any query strings).

Here's a resolver example where query strings are not included.

```php
<?php
namespace App\Resolvers;

use Illuminate\Support\Facades\App;
use Illuminate\Support\Facades\Request;

class UrlResolver implements \OwenIt\Auditing\Contracts\Resolver
{
    /**
     * {@inheritdoc}
     */
    public static function resolve(): string
    {
        if (App::runningInConsole()) {
            return 'console';
        }

        // Just the full URL without query strings
        return Request::url();
    }
}
```

Set the custom _URL_ resolver in the `config/audit.php` configuration file:

```php
return [
    // ...

    'resolvers' = [
        // ...
        'url' => App\Resolvers\UrlResolver::class,
        // ...
    ],

    // ...
];
```

## User Agent Resolver
This resolver uses the `Request::userAgent()` method, which might return `null` if a User Agent isn't available.

The following example will return a default string when the `User-Agent` HTTP header is empty/unavailable.

```php
<?php
namespace App\Resolvers;

use Illuminate\Support\Facades\Request;

class UserAgentResolver implements \OwenIt\Auditing\Contracts\Resolver
{
    /**
     * {@inheritdoc}
     */
    public static function resolve()
    {
        // Default to "N/A" if the User Agent isn't available
        return Request::header('User-Agent', 'N/A');
    }
}
```

Set the custom _User Agent_ resolver in the `config/audit.php` configuration file:

```php
return [
    // ...

    'resolvers' = [
        // ...
        'user_agent' => App\Resolvers\UserAgentResolver::class,
        // ...
    ],

    // ...
];
```

## User Resolver
Out of the box, this resolver uses the Laravel `Auth` facade.

The `resolve()` method must return the `Model` instance of the currently logged user, or `null` if the user cannot be resolved.

When using other authentication mechanisms like [Sentinel](https://github.com/cartalyst/sentinel), a different resolver must be implemented.

```php
<?php
namespace App\Resolvers;

use Cartalyst\Sentinel\Laravel\Facades\Sentinel;

class UserResolver implements \OwenIt\Auditing\Contracts\UserResolver
{
    /**
     * {@inheritdoc}
     */
    public static function resolve()
    {
        return Sentinel::check() ? Sentinel::getUser() : null;
    }
}
```

Set the custom _User_ resolver in the `config/audit.php` configuration file:

```php
return [
    // ...

    'user' = [
        // ...
        'resolver' => App\Resolvers\UserResolver::class,
        // ...
    ],

    // ...
];
```
> {tip} The resolved `User` should implement the `Illuminate\Contracts\Auth\Authenticatable` interface, or at least have a `getAuthIdentifier()` method that returns the `id`.
