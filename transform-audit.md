# Transform audit


Transform the audit data array before it is passed into the database.


```php

use Illuminate\Support\Arr;
use OwenIt\Auditing\Auditable;
use Illuminate\Database\Eloquent\Model;

class User extends Model;
{
  use Auditable;
  
  // ...
  
  public function transformAudit(array $data)
  {
      if(Arr::has('new.role_id')){
        Arr::set($data, 'new.role',  $this->role->name)
      }
    
      return $data;
  }
}

```
