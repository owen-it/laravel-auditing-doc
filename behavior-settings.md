# Auditing Behavior

The auditing process can behave differently, depending on the values some `Auditable` attributes may have.

## Include attributes

The `$auditInclude` property acts as an attribute **white list**, meaning that only the property names in that `array` will be audited.

```php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;
use OwenIt\Auditing\Auditable;
use OwenIt\Auditing\Contracts\Auditable as AuditableContract;

class Post extends Model implements AuditableContract;
{
    use Auditable;

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
use OwenIt\Auditing\Auditable;
use OwenIt\Auditing\Contracts\Auditable as AuditableContract;

class Post extends Model implements AuditableContract;
{
    use Auditable;

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
When set to `true`, the `$auditStrict` property will use the `$hidden` and `$visible` in the same fashion as the `$auditExclude` and `$auditInclude`, respectively.

```php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;
use OwenIt\Auditing\Auditable;
use OwenIt\Auditing\Contracts\Auditable as AuditableContract;

class Post extends Model implements AuditableContract;
{
    use Auditable;

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
use OwenIt\Auditing\Auditable;
use OwenIt\Auditing\Contracts\Auditable as AuditableContract;

class Post extends Model implements AuditableContract;
{
    use Auditable;

    /**
     * Should the timestamps be audited?
     *
     * @var bool
     */
    protected $auditTimestamps = true;
}
```

## Number of Audit records

Out of the box, the number of `Audit` records that are kept for an `Auditable` model is unlimited.
To impose a limit, set the `$auditThreshold` property to a positive `int` of your choice.

```php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;
use OwenIt\Auditing\Auditable;
use OwenIt\Auditing\Contracts\Auditable as AuditableContract;

class Post extends Model implements AuditableContract;
{
    use Auditable;

    /**
     * Audit threshold.
     *
     * @var int
     */
    protected $auditThreshold = 10;
}
```

The above configuration, will keep the `10` latest `Audit` records, purging everything else.

> {tip} By default, the value is set to `0` (zero), which stands for no limit.


## Auditable events

On a default configuration, the `created`, `updated`, `deleted` and `restored` Eloquent events will trigger an audit action.

In case the auditing process only needs to be executed for certain events, the `auditableEvents` property can be set with the events that matter.

```php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;
use OwenIt\Auditing\Auditable;
use OwenIt\Auditing\Contracts\Auditable as AuditableContract;

class Post extends Model implements AuditableContract;
{
    use Auditable;

    /**
     * Auditable events.
     *
     * @var array
     */
    protected $auditableEvents = [
        'deleted',
    ];
}
```
