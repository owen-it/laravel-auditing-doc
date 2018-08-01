# Attribute Modifiers
Version **8.0.0** brings the `AttributeModifier` contract, improving the way `Auditable` attributes can be modified, when creating an `Audit`.

## Attribute Redactors
**Attribute Redactors** (previously called **Audit Redactors**) were introduced in version **6.1.0**, making it easy to mask sensitive information when auditing.

Two redactors are included, out of the box.

### LeftRedactor
The `LeftRedactor` will mask part of the data, from the left.

Value before being redacted | Value after being redacted
:---------------------------|:--------------------------
`How To Audit Eloquent Models` | `#########################els`

### RightRedactor
The `RightRedactor` will work in the same fashion as the previous one, but like the name implies, it will mask the data from the right.

Value before being redacted | Value after being redacted
:---------------------------|:--------------------------
`How To Audit Eloquent Models` | `How#########################`

> {tip} Depending on the length, both redactors should mask ~90% of the data.

### Custom redactors
If the two default redactors don't suit your needs, you're free to implement your own.

A redactor is a class that implements the `\OwenIt\Auditing\Contracts\AttributeRedactor` interface.

> {tip} The included redactors are a good starting point to get ideas for a new custom attribute redactor implementation.

### Usage

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
     * Attribute modifiers.
     *
     * @var array
     */
    protected $attributeModifiers = [
        'title' => LeftRedactor::class,
    ];

    // ...
}
```

> {note} [Auditable Transition](auditable-transition) won't work if an `AttributeRedactor` is set, since redacted data can't be used when transitioning states.

## Attribute Encoders
The `AttributeEncoder` interface was introduced in version **8.0.0**. By default, one encoder is included.

### Base64Encoder
As the name implies, the `Base64Encoder` will encode attributes in [Base64](https://en.wikipedia.org/wiki/Base64).

Value before being encoded | Value after being encoded
:---------------------------|:--------------------------
`How To Audit Eloquent Models` | `SG93IFRvIEF1ZGl0IEVsb3F1ZW50IE1vZGVscw==`

> {tip} This comes in handy when dealing with binary data, that would otherwise break the underlying `Eloquent` model, when [casting](https://laravel.com/docs/5.6/eloquent-mutators#array-and-json-casting) to JSON.

### Custom encoders
In the same fashion as a custom `AttributeRedactor`, feel free to create your own `AttributeEncoder` implementations.

Just create a class implementing the `\OwenIt\Auditing\Contracts\AttributeEncoder` interface.

> {tip} Use the included encoder as a starting point for your implementation.

### Usage

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
     * Attribute modifiers.
     *
     * @var array
     */
    protected $attributeModifiers = [
        'title' => Base64Encoder::class,
    ];

    // ...
}
```

> {tip} When using the `getModified()` method to retrieve data from an `Audit`, any encoded attributes will be returned in their decoded form.
