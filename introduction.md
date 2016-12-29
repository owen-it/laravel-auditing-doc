# Introduction

This package will help you understand changes in your Eloquent models, by providing information about possible discrepancies and anomalies that could indicate business concerns or suspect security breaches.

Laravel Auditing allows you to record changes of an Eloquent model's set of data by simply using a trait in your model.
It also provides you with a simple interface to retrieve the audited data, making it straightforward to display it in various ways.

If you have [installed the package](/docs/{{version}}/installation) already, lets start using it.

Setting a model for auditing is simple.
Just `use` the `OwnerIt\Auditing\Auditable` trait in the model you wish to audit and `implement` the `OwenIt\Auditing\Contracts\Auditable` interface.

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

By default, Laravel Auditing will use the Database Auditor to record the changes in the model.
If needed, you can implement your own [Audit Driver](/docs/{{version}}/audit-drivers).
