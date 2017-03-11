# Troubleshooting
This is a list of common problems and their respective solutions.

## Auditing not working
You followed the documentation to the letter and yet, the package doesn't work?

So you know, this package relies on Eloquent [events](https://laravel.com/docs/5.4/eloquent#events) and if they don't fire, the package has no way to know something happened.

The most common mistake is when someone tries to `update` or `delete` using a `Builder` instance, rather than an Eloquent model.

Usually, something like this happens:
```php
Post::where('id', $id)->update($data);
```

Instead, you should do:
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

Just make sure the `id` column type of the `users` table matches the `user_id` column type in `audits`.

### URL
Sometimes, the audited URLs are longer than 255 characters, so the `url` column must be changed from `VARCHAR(255)` to `TEXT`.

```php
$table->text('url')->nullable();
```
