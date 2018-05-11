# Audit Resolvers
A resolver is a class that implements one of the following contracts:
- `OwenIt\Auditing\Contracts\IpAddressResolver`
- `OwenIt\Auditing\Contracts\UrlResolver`
- `OwenIt\Auditing\Contracts\UserAgentResolver`
- `OwenIt\Auditing\Contracts\UserResolver`

Each resolver must have a **public static** `resolve()` method with the appropriate logic.

## IP Address Resolver
By default, this resolver uses the `Request::ip()` method to get the current IP address.

Most of the times, this will get the correct value, but for applications running behind a proxy or a [load balancer](https://en.wikipedia.org/wiki/Load_balancing_(computing)), IP addresses must be obtained differently.

Usually, the real IP address will be passed via an **X-Forwarded-For** HTTP header.

```php
<?php
namespace App\Resolvers;

use Illuminate\Support\Facades\Request;

class IpAddressResolver implements \OwenIt\Auditing\Contracts\IpAddressResolver
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

    'resolver' = [
        // ...
        'ip_address' => App\Resolvers\IpAddressResolver::class,
        // ...
    ],

    // ...
];
```

## URL Resolver
This resolver uses the `Request::fullUrlWithQuery()` method to get the current URL (including any query strings).

Here's a resolver example where query strings are not included.

```php
<?php
namespace App\Resolvers;

use Illuminate\Support\Facades\App;
use Illuminate\Support\Facades\Request;

class UrlResolver implements \OwenIt\Auditing\Contracts\UrlResolver
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
        return Request::fullUrl();
    }
}
```

Set the custom _URL_ resolver in the `config/audit.php` configuration file:

```php
return [
    // ...

    'resolver' = [
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

class UserAgentResolver implements \OwenIt\Auditing\Contracts\UserAgentResolver
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

    'resolver' = [
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

    'resolver' = [
        // ...
        'user' => App\Resolvers\UserResolver::class,
        // ...
    ],

    // ...
];
```
