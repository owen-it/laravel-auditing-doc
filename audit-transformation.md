# Audit transformation

If needed, the `Audit` data can be transformed before being stored.

```php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Support\Arr;
use OwenIt\Auditing\Contracts\Auditable;

class User extends Model implements Auditable
{
    use \OwenIt\Auditing\Auditable;

    // ...

    /**
     * {@inheritdoc}
     */
    public function transformAudit(array $data): array
    {
        if (Arr::has($data, 'new_values.role_id')) {
            $data['old_values']['role_name'] = Role::find($this->getOriginal('role_id'))->name;
            $data['new_values']['role_name'] = Role::find($this->getAttribute('role_id'))->name;
        }

        return $data;
    }
}
```
