# Troubleshooting
This is a compilation of common problems and their respective solutions and/or workarounds.

## Auditing does not work
You followed the documentation to the letter and yet, `Audit` records are not being created?

### Builder vs. Eloquent
Bear in mind that this package relies on Eloquent [events](https://laravel.com/docs/5.6/eloquent#events) and if they don't fire, an `Audit` cannot be created.

The most common mistake is performing an `update` or `delete` using a `Builder` instance, rather than an using an Eloquent `Model`.

Using the `Builder` won't trigger an `Audit`:
```php
Article::where('id', $id)->update($data);
```

But using `Eloquent` will:
```php
Article::find($id)->update($data);
```

### Console/CLI and Jobs
Eloquent events fired from a **Job** or from the **console** (i.e. migrations, tests, commands, Tinker, ...), **WILL NOT** be audited by default.

Please refer to the [General Configuration](general-configuration) for more information. 

## Attributes are considered modified, when they're not
False positives cause Audit records to be created.
This happens when a model with boolean/date attributes gets updated, regardless of change in those attributes.

> {tip} This behaviour has been [fixed](https://github.com/laravel/framework/pull/18400) in Laravel 5.5+, but it's still present in older versions. 

The internal data of the Eloquent model will be as follows:

In the **$attributes** array attribute:
- `true` stays `true`
- `false` stays `false`
- `YYYY-MM-DD` stays `YYYY-MM-DD`

In the **$original** array attribute:
- `true` becomes `1`
- `false` becomes `0`
- `YYYY-MM-DD` becomes `YYYY-MM-DD 00:00:00`

This makes the `getDirty()` and `isDirty()` methods to consider wrongful attribute changes when comparing data.

> {tip} For Laravel versions prior to 5.5, use this [trait](https://gist.github.com/crashkonijn/7d581e55770d2379494067d8b0ce0f6d), courtesy of [Peter Klooster](https://github.com/crashkonijn)! 

Other discussions about this [subject](https://github.com/laravel/internals/issues/349).

## Argument 1 passed to Illuminate\Database\Eloquent\Model::serializeDate() must implement interface DateTimeInterface, null given
This might happen in version **4.1.x**, because the `updated_at` column values in the `audits` table are set to `NULL`.

After upgrading the table schema for **4.1.x**, don't forget to set the `updated_at` values to match the ones from `created_at`.

Read the [Upgrading](upgrading) documentation for more details.

## Empty old/new values being audited
An `Audit` record is more than just **old** and **new** values.

There's metadata like `event`, `user_*`, `url`, `ip_address`, `user_agent` and `tags`, which in some cases is more than enough for accountability purposes.

Still, if you don't want to keep track of such information when the `old_values` and `new_values` are empty, register the following observer in the `Audit` model's `boot()` method:

```php
Audit::creating(function (Audit $model) {
    if (empty($model->old_values) && empty($model->new_values)) {
        return false;
    }
});
```

> {note} Keep in mind that the `old_values` and `new_values` of a `retrieved` event, will always be empty!

## PHP Fatal error:  Maximum function nesting level of '512' reached, aborting!
This error happens when an `Audit` is being created for a `retrieved` event on a `User` model.
It boils down to the `UserResolver`, retrieving a `User` record, which will fire a new `retrieved` event, leading to a new resolve cycle and so on.

To avoid this, make sure the `User` model isn't configured for retrieval audits, like so:

```php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;
use OwenIt\Auditing\Contracts\Auditable;

class User extends Model implements Auditable
{
    use \OwenIt\Auditing\Auditable;

    protected $auditEvents = [
        'created',
        'updated',
        'deleted',
        'restored',
    ];

    // ...
}
```
