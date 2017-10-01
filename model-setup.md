# Model Setup

Setting up a model for auditing couldn't be simpler.
Just _use_ the `OwenIt\Auditing\Auditable` trait in the model you wish to audit and _implement_ the `OwenIt\Auditing\Contracts\Auditable` interface.

```php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;
use OwenIt\Auditing\Auditable;
use OwenIt\Auditing\Contracts\Auditable as AuditableContract;

class User extends Model implements AuditableContract
{
    use Auditable;

    // ...
}
```

By default, the `Database` audit driver will be used.
If needed, you can also implement your own [Audit Driver](/docs/{{version}}/audit-drivers).
