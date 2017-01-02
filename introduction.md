# Introduction

This package will help you understand changes in your Eloquent models, by providing information about possible discrepancies and anomalies that could indicate business concerns or suspect activities.

Laravel Auditing allows you to keep a history of model changes by simply using a trait.
Retrieving the audited data is straightforward, making it possible to display it in various ways.

If the package is already [installed](/docs/{{version}}/installation), lets start using it!

Setting up a model for auditing couldn't be simpler.
Just _use_ the `OwnerIt\Auditing\Auditable` trait in the model you wish to audit and _implement_ the `OwenIt\Auditing\Contracts\Auditable` interface.

```php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;
use OwenIt\Auditing\Auditable;
use OwenIt\Auditing\Contracts\Auditable as AuditableContract;

class User extends Model implements AuditableContract;
{
    use Auditable;

    // ...
}
```

By default, the `Database` audit driver will be used.
If needed, you can also implement your own [Audit Driver](/docs/{{version}}/audit-drivers).
