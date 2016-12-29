# Auditor

The `Auditor` is the class responsible for executing audits and pruning old `Audit` records.

Usually, there's no need to use it directly, since the `AuditableObserver` takes care of things for us.

Yet, should the need to call it manually arise, here are two ways of doing it.

## Using the Auditor Facade

```php
<?php

namespace App\Http\Controllers;

use OwenIt\Auditing\Contracts\Auditor;
use OwenIt\Auditing\Facades\Auditor;

class MyController extends Illuminate\Routing\Controller
{
    public function updateModel(Auditable $model)
    {
        Auditor::execute($model);

        Auditor::prune($model);
    }
}
```

## Injecting the Auditor as a dependency

With the `AuditingServiceProvider` registered, we can use the IoC to resolve and inject the `Auditor` into a method.

```php
<?php

namespace App\Http\Controllers;

use OwenIt\Auditing\Contracts\Auditable;
use OwenIt\Auditing\Contracts\Auditor;

class MyController extends Illuminate\Routing\Controller
{
    public function updateModel(Auditable $model, Auditor $auditor)
    {
        $auditor->execute($model);

        $auditor->prune($model);
    }
}
```
