# Audit transformations

If needed, the `Audit` data can be transformed before being stored.

```php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Support\Arr;
use OwenIt\Auditing\Auditable;
use OwenIt\Auditing\Contracts\Auditable as AuditableContract;

class User extends Model implements AuditableContract;
{
    use Auditable;
  
    // ...

    /**
     * {@inheritdoc}
     */
    public function transformAudit(array $data)
    {
        if (Arr::has($data, 'new_values.role_id')) {
            Arr::set($data, 'new_values.role_name',  $this->role->name);
        }

        return $data;
    }
}
```
