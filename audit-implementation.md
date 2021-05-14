# Audit Implementation
Starting from version **4.1.0**, support for custom `Audit` models was added.
This lets the user extend implementations other than the traditional `Illuminate\Database\Eloquent\Model`.

> {tip} `Audit` models must implement the `OwenIt\Auditing\Contracts\Audit` interface!

## MongoDB Audit model example
Start by installing the [jenssegers/mongodb](https://github.com/jenssegers/laravel-mongodb) package:
```sh
composer require jenssegers/mongodb
```

Implementation:

```php
<?php
namespace App\Models;

use Jenssegers\Mongodb\Eloquent\Model;

class MongoAudit extends Model implements \OwenIt\Auditing\Contracts\Audit
{
    use \OwenIt\Auditing\Audit;

    /**
     * {@inheritdoc}
     */
    protected $guarded = [];

    /**
     * {@inheritdoc}
     */
    protected $casts = [
        'old_values'   => 'json',
        'new_values'   => 'json',
    ];

    /**
     * {@inheritdoc}
     */
    public function auditable()
    {
        return $this->morphTo();
    }

    /**
     * {@inheritdoc}
     */
    public function user()
    {
        return $this->morphTo();
    }
}
```

> {tip} The bulk of the `Audit` logic is in the `OwenIt\Auditing\Audit` trait.

## Defining the Audit model

In the `config/audit.php` file, set the `implementation` value as the [FQCN](http://php.net/manual/en/language.namespaces.rules.php) of the `Audit` model you wish to use.
If the value is missing from the configuration, the `audits()` relation method of the `Auditing` trait will default to `OwenIt\Auditing\Models\Audit`.

Here's how to set the `MongoAudit` implementation above:
```php
return [
    // ...

    'implementation' => App\Models\MongoAudit::class,

    // Uncomment below if you need to specify the details of the mongo database connection (useful in hybrid setups with multiple DBs)
    // 'drivers' => [
    //     'database' => [
    //         'table' => 'audits',
    //         'connection' => 'mongodb'
    //     ]
    // ],    
    
    // ...
];
```
