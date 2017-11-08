# Audit Migration
While the package comes with a pretty standard migration file which covers most use cases, the default table schema might not be suitable for everyone.

With that in mind, here are a few tweaks that can be performed.

## User ID foreign key relation
Some developers like to have the relation between tables properly set, by enforcing foreign keys.
At the end of the **up()** method, add the following:

```php
$table->foreign('user_id')
    ->references('id')
    ->on('users')
    ->onDelete('cascade')
    ->onUpdate('cascade');
```

## Using a different column name for the User ID
Instead of the typical `user_id` column, a different name can be used:

```php
$table->unsignedInteger('owner_id')->nullable();
```

Just make sure the `foreign_key` value in the configuration is also updated, to reflect the change:

```php
return [
    'user' = [
        'foreign_key' => 'owner_id',
    ],
];
```

> {tip} Read more about this and the `User` model ID in the [General Configuration](general-configuration) section.

## UUID over auto-incrementing ids
Some developers prefer to use a [UUID](https://en.wikipedia.org/wiki/Universally_unique_identifier) instead of auto-incrementing ids.
If that's the case, make sure to update the **up()** method like so:

For the `User`, change from
```php
$table->unsignedInteger('user_id')->nullable();
```

to

```php
$table->uuid('user_id')->nullable();
```

For the `Auditable` model, change from
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

> {note} Make sure the `user_id` and/or `auditable_id` column types match the ones used in their respective tables.

## URL values with more than 255 characters
Sometimes, the audited URLs are longer than 255 characters, so the `url` column must be updated from `string`

```php
$table->string('url')->nullable();
```

to `text`

```php
$table->text('url')->nullable();
```

> {tip} From version 4.1.3 onwards, the default migration creates the `url` column as `text` instead of `string`.
