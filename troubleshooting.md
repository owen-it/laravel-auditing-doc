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

> {tip} Make sure the `id` column type of the `users` table matches the `user_id` column type in `audits`.

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
