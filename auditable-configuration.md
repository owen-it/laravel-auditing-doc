# Auditable Configuration

The auditing process can behave differently, depending on the values some `Auditable` attributes may have.

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
        'category_id',
    ];
}
```

> {tip} The default value is an empty `array`, which treats all model properties as good for auditing.

## Strict audits

By default, the `$hidden` and `$visible` values of a model aren't considered when including/excluding audited properties.
When the `$auditStrict` property is set to `true`,  the `$hidden` and `$visible` will be used in the same fashion as the `$auditExclude` and `$auditInclude`, respectively.

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
}
```

## Audit timestamps

The standard auditing behaviour is to ignore all timestamp related attributes (`created_at`, `updated_at` and `deleted_at`).
If there's a need to include them in the `Audit` data, just set the `$auditTimestamps` attribute to `true`.

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
}
```

## Number of Audit records

Out of the box, there isn't a limit for the number of `Audit` records kept for an `Auditable` model.
To keep records to a minimum, set the `$auditThreshold` property to a positive `int` of your choice.

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
}
```

The above configuration, will only keep the `10` latest `Audit` records.

> {tip} By default, the `$auditThreshold` value is set to `0` (zero), which stands for no limit.


## Auditable events

On a default configuration, the `created`, `updated`, `deleted` and `restored` Eloquent events will trigger an audit.

In case the auditing process only needs to execute on certain events, the `auditableEvents` property can be set with the events that matter.

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
    protected $auditableEvents = [
        'deleted',
        'restored',
    ];
}
```

The above example only takes the `deleted` and `restored` events into account.

### Custom event handlers
Here's how a custom method for the `restored` event can be defined:

```php
protected $auditableEvents = [
    'deleted', 
    'restored' => 'myRestoredEventHandler', 
];
```

The `deleted` event is handled by the default `auditDeletedAttributes()` method, while the `restored` event will be handled by the custom `myRestoredEventHandler()` method.

### Event wildcards
Wildcards can be used for event names, making it easy to define the same handler to multiple events.

```php
protected $auditableEvents = [
    '*ated' => 'myCreatedUpdatedEventHandler',
];
```

The `created` and `updated` events will be handled by the custom `myCreatedUpdatedEventHandler()` method

> {note} Support for custom handlers and wildcards has been present since version 4.1.4.
