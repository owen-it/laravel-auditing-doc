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

### Console and Jobs
Eloquent events triggered from the console or from a Job won't be audited by default.

Check the [General Configuration](general-configuration) to know how to overcome that. 

## Attributes are considered modified, when they're not
False positives cause Audit records to be created.
This happens when model attributes are updated with **BOOLEAN** and/or **DATE** values.

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

According to a [maintainer](https://github.com/laravel/framework/issues/16823#issuecomment-267573840), this is an **expected behaviour**.

As a workaround, consider passing `1` and `0`, instead of `true` and `false`. For date values, append ` 00:00:00`.

At the time of writing, [this](https://github.com/laravel/internals/issues/349) is the only open issue about this subject.

## Argument 1 passed to Illuminate\Database\Eloquent\Model::serializeDate() must implement interface DateTimeInterface, null given
This might happen in version **4.1.x**, because the `updated_at` column values in the `audits` table are set to `NULL`.

After upgrading the table schema for **4.1.x**, don't forget to set the `updated_at` values to match the ones from `created_at`.

Read the [Upgrading](upgrading) documentation for more details.
