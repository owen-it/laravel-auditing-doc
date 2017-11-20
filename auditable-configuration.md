# Auditable Configuration
The auditing process can behave differently, depending on what has been set.

## Include attributes
The `$auditInclude` property acts as an attribute **white list**, meaning that only the property names in that `array` will be audited.

```php
<?php
namespace App;

use Illuminate\Database\Eloquent\Model;
use OwenIt\Auditing\Contracts\Auditable;

class Article extends Model implements Auditable
{
    use \OwenIt\Auditing\Auditable;

    /**
     * Attributes to include in the Audit.
     *
     * @var array
     */
    protected $auditInclude = [
        'title',
        'content',
    ];

    // ...
}
```

> {tip} The default value is an empty `array`, which treats all model properties as OK for auditing.

## Exclude attributes
On the other hand, the `$auditExclude` acts as a **black list**, excluding all the properties in the `array` from being audited.

```php
<?php
namespace App;

use Illuminate\Database\Eloquent\Model;
use OwenIt\Auditing\Contracts\Auditable;

class Article extends Model implements Auditable
{
    use \OwenIt\Auditing\Auditable;

    /**
     * Attributes to exclude from the Audit.
     *
     * @var array
     */
    protected $auditExclude = [
        'published',
    ];

    // ...
}
```

> {tip} The default value is an empty `array`, which treats all model properties as OK for auditing.

## Strict audits
By default, the `$hidden` and `$visible` values of a model aren't considered when including/excluding audited properties.
When the `$auditStrict` property is set to `true`,  the `$hidden` and `$visible` properties will be used in the same fashion as the `$auditExclude` and `$auditInclude`, respectively.

### Globally
This is done in the `config/audit.php` configuration file.

```php
return [
    // ...

    'strict' => true,

    // ...
];
```

### Locally
This is done on a per `Auditable` model basis, by assigning a `bool` value to the `$auditStrict` attribute.

> {tip} A locally defined strict value **always** takes precedence over a globally defined one.

```php
<?php
namespace App;

use Illuminate\Database\Eloquent\Model;
use OwenIt\Auditing\Contracts\Auditable;

class Article extends Model implements Auditable
{
    use \OwenIt\Auditing\Auditable;

    /**
     * Should the audit be strict?
     *
     * @var bool
     */
    protected $auditStrict = true;

    // ...
}
```

## Audit timestamps
The standard auditing behaviour is to ignore all default timestamp attributes (`created_at`, `updated_at` and `deleted_at`).
If there's a need to include them in the `Audit` data, just set the `$auditTimestamps` attribute to `true`.

### Globally
This is done in the `config/audit.php` configuration file.

```php
return [
    // ...

    'timestamps' => true,

    // ...
];
```

### Locally
This is done on a per `Auditable` model basis, by assigning a `bool` value to the `$auditTimestamps` attribute.

> {tip} A locally defined timestamps value **always** takes precedence over a globally defined one.

```php
<?php
namespace App;

use Illuminate\Database\Eloquent\Model;
use OwenIt\Auditing\Contracts\Auditable;

class Article extends Model implements Auditable
{
    use \OwenIt\Auditing\Auditable;

    /**
     * Should the timestamps be audited?
     *
     * @var bool
     */
    protected $auditTimestamps = true;

    // ...
}
```

## Number of Audit records
Out of the box, there isn't a limit for the number of `Audit` records that are kept for an `Auditable` model.
To keep the records to a minimum, set the `$auditThreshold` property to a positive `int` of your choice.

### Globally
This is done in the `config/audit.php` configuration file.

```php
return [
    // ...

    'threshold' => 10,

    // ...
];
```

### Locally
This is done on a per `Auditable` model basis, by assigning an `int` value to the `$auditThreshold` attribute.

> {tip} A locally defined threshold **always** takes precedence over a globally defined one.

```php
<?php
namespace App;

use Illuminate\Database\Eloquent\Model;
use OwenIt\Auditing\Contracts\Auditable;

class Article extends Model implements Auditable
{
    use \OwenIt\Auditing\Auditable;

    /**
     * Audit threshold.
     *
     * @var int
     */
    protected $auditThreshold = 10;

    // ...
}
```

The above configuration, will only keep the `10` latest `Audit` records.

> {tip} By default, the `$auditThreshold` value is set to `0` (zero), which stands for no limit.

## Audit events
On a default configuration, the `created`, `updated`, `deleted` and `restored` Eloquent events will trigger an audit.

When not all events are required for auditing, the `auditEvents` property can be set with the ones that are.

### Globally
This is done in the `config/audit.php` configuration file.

```php
return [
    // ...

    'events' => [
        'deleted',
        'restored',
    ],

    // ...
];
```

### Locally
This is done on a per `Auditable` model basis, by assigning an `array` value to the `$auditEvents` attribute.

> {tip} Locally defined events **always** take precedence over globally defined ones.

```php
<?php
namespace App;

use Illuminate\Database\Eloquent\Model;
use OwenIt\Auditing\Contracts\Auditable;

class Article extends Model implements Auditable
{
    use \OwenIt\Auditing\Auditable;

    /**
     * Auditable events.
     *
     * @var array
     */
    protected $auditEvents = [
        'deleted',
        'restored',
    ];

    // ...
}
```

The above example only takes the `deleted` and `restored` events into account.

### Custom event handlers
Here's how a custom method for the `restored` event can be defined:

```php
protected $auditEvents = [
    'deleted',
    'restored' => 'myRestoredEventHandler',
];
```

The `deleted` event is handled by the default `auditDeletedAttributes()` method, while the `restored` event will be handled by a custom `myRestoredEventHandler()` method.

### Event wildcards
Wildcards can be used for event names, making it easy to define the same handler to multiple events.

```php
protected $auditEvents = [
    '*ated' => 'myCreatedUpdatedEventHandler',
];
```

The `created` and `updated` events will be handled by a custom `myCreatedUpdatedEventHandler()` method

> {note} Support for custom handlers and wildcards has been present since version 4.1.4.

### Retrieved event
Eloquent **5.5** brings a new `retrieved` event, which is supported, although **not** enabled by default.

The main reason is to avoid creating **lots** of `Audit` records, specially on busy applications, so enable it with care.

> {note} When caching is enabled and depending on how it's configured, `retrieved` events might not fire as often!
