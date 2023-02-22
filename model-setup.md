# Model Setup
Setting up a model for auditing could not be simpler.
Just _use_ the `OwenIt\Auditing\Auditable` trait in the model you wish to audit and _implement_ the `OwenIt\Auditing\Contracts\Auditable` interface.

```php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;
use OwenIt\Auditing\Contracts\Auditable;

class User extends Model implements Auditable
{
    use \OwenIt\Auditing\Auditable;

    // ...
}
```

By default, the `Database` audit driver will be used.
If needed, you can also implement your own [Audit Driver](audit-drivers.md).
