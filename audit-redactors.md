# Audit Redactors
Redactors were introduced in version **6.1.0**, and are an easy way to mask sensitive information when auditing.

Two redactors are included, out of the box.

## LeftRedactor
The `LeftRedactor` class will mask part of the data, from the left to the right.

Value before being redacted | Value after being redacted
:---------------------------|:--------------------------
`How To Audit Eloquent Models` | `#########################els`

## RightRedactor
The `RightRedactor` will work in the same fashion as the previous one, but like the name implies, it will mask the data from the right to the left.

Value before being redacted | Value after being redacted
:---------------------------|:--------------------------
`How To Audit Eloquent Models` | `How#########################`

> {tip} Depending on the length, both redactors should mask ~90% of the data.

## Custom redactors
If the two default redactors don't suit your needs, you're free to implement your own.

A redactor is a class that implements the `\OwenIt\Auditing\Contracts\AuditRedactor` interface.

> {tip} The included redactors are a good starting point to get ideas for a new custom redactor implementation.

## Usage

```php
<?php
namespace App\Models;

use Illuminate\Database\Eloquent\Model;
use OwenIt\Auditing\Contracts\Auditable;
use OwenIt\Auditing\Redactors\LeftRedactor;

class Article extends Model implements Auditable
{
    use \OwenIt\Auditing\Auditable;

    /**
     * Audit redactors.
     *
     * @var array
     */
    protected $auditRedactors = [
        'title' => LeftRedactor::class,
    ];

    // ...
}
```

> {note} [Auditable Transition](auditable-transition) won't work if a redactor is set, since redacted data should not be used when transitioning states.
