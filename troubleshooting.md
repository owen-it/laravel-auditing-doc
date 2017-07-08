# Troubleshooting
This is a compilation of common problems and their respective solutions and/or workarounds.

## Auditing not working
You followed the documentation to the letter and yet, audit records aren't being created?

This package relies on Eloquent [events](https://laravel.com/docs/5.4/eloquent#events) and if they don't fire, there's no way to know something happened.

The most common mistake is doing an `update` or `delete` using a `Builder` instance, rather than an using an Eloquent `Model`.

Usually, something like this is used:
```php
Post::where('id', $id)->update($data);
```

When in fact, it should be:
```php
Post::find($id)->update($data);
```

## The default table schema doesn't suit me
While the package comes with a pretty standard migration file which should cover most use cases, that does not mean it cannot be tweaked.

### User ID foreign key relation
Some developers like to have the relation between tables properly set, by enforcing foreign keys.
At the end of the **up()** method, add the following:

```php
$table->foreign('user_id')
    ->references('id')
    ->on('users')
    ->onDelete('cascade')
    ->onUpdate('cascade');
```

### Using a different column name for the User ID
A different column name can be used, instead of the typical `user_id`:

```php
$table->unsignedInteger('owner_id')->nullable();
```

Just make sure the `foreign_key` value in the configuration is also updated, to reflect that change:

```php
return [
    'user' = [
        'foreign_key' => 'owner_id',
    ],
];
```

> {tip} Read more about this and the `User` model ID in the [General Settings](general-settings) section.

### UUID over auto-incrementing integer
Some developers prefer to use a [UUID](https://en.wikipedia.org/wiki/Universally_unique_identifier) instead of auto-incrementing integer ids.
If that's your case, make sure to update the **up()** method like so:

For the `User`, change from
```php
$table->unsignedInteger('user_id')->nullable();
```

to

```php
$table->uuid('user_id')->nullable();
```

For the `Auditable`, change from
```php
$table->morphs('auditable');
```

to

```php
$this->uuid('auditable_id');
$this->string('auditable_type');
$this->index([
    'auditable_id', 
    'auditable_type',
]);
```

> {note} Make sure the `user_id` and/or `auditable_id` column types match the ones used in the other tables.

### URL values with more than 255 characters
Sometimes, the audited URLs are longer than 255 characters, so the `url` column must be updated from a `VARCHAR(255)`

```php
$table->string('url')->nullable();
```

to a `TEXT`

```php
$table->text('url')->nullable();
```

## Error: Call to undefined method Closure::__set_state()
This error happens when Laravel is set to cache the configuration.

By default, the `User` resolver is a `Closure` in the `config/audits.php` file, which causes this issue.
To overcome this, the resolver should be set as a FQCN instead.

Read the resolver [documentation](/docs/{{version}}/general-settings) for more information.

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
