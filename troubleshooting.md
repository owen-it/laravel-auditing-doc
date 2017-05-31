# Troubleshooting
This is a list of common problems and their respective solutions.

## Auditing not working
You followed the documentation to the letter and yet, the package doesn't work?

This package relies on Eloquent [events](https://laravel.com/docs/5.4/eloquent#events) and if they don't fire, there's no way to know something happened.

The most common mistake is doing an `update` or `delete` using a `Builder` instance, rather than an Eloquent `Model`.

Usually, something like this happens:
```php
Post::where('id', $id)->update($data);
```

Instead, what should be done is:
```php
Post::find('id', $id)->update($data);
```

## Audit table
While the package comes with a default migration file which should cover most use cases, that does not mean it cannot be tweaked.

### User ID
Some people like to have the relation between tables properly set, by enforcing foreign keys.
If that's your case, you can add the following to the migration:

```php
$table->foreign('user_id')
    ->references('id')
    ->on('users')
    ->onDelete('cascade')
    ->onUpdate('cascade');
```

### UUID
Some developers prefer to use a [UUID](https://en.wikipedia.org/wiki/Universally_unique_identifier) instead of an auto-increment id in their tables.
If that's your case, make sure you update the `audits` migration like so:

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

> {note} Make sure the `user_id`/`auditable_id` column types match the ones used in the other tables!

### URL
Sometimes, the audited URLs are longer than 255 characters, so the `url` column must be updated from `VARCHAR(255)`

```php
$table->string('url')->nullable();
```

to `TEXT`

```php
$table->text('url')->nullable();
```

### Error: Call to undefined method Closure::__set_state()
This error happens when Laravel is set to cache the configuration.

By default, the `User` resolver is a `Closure` in the `config/audits.php` file, which causes this issue.
To overcome this, the resolver should be set as a FQCN instead.

Read the resolver [documentation](/docs/{{version}}/general-settings) for more information.

### Attributes are considered modified, when they're not

When updating a model with **BOOLEAN** or **DATE** attributes, the following happens:

In the Eloquent `$attributes` attributes `array`
- `true` stays `true`
- `false` stays `false`
- `YYYY-MM-DD` stays `YYYY-MM-DD`

In the Eloquent `$original` attributes `array`
- `true` becomes `1`
- `false` becomes `0`
- `YYYY-MM-DD` becomes `YYYY-MM-DD 00:00:00`

This makes the `getDirty()` and `isDirty()` methods to consider wrongful attribute changes.

According to a [maintainer](https://github.com/laravel/framework/issues/16823#issuecomment-267573840), this is an **expected behaviour**.

As a workaround, consider passing `1` and `0`, instead of `true` and `false`. For date values, append ` 00:00:00`.

At the time of writing, [this](https://github.com/laravel/internals/issues/349) is the only open issue about this subject.
