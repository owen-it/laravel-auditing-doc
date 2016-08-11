# Getting Started

## With Trait

To register the change log, use the trait `OwnerIt\Auditing\AuditingTrait` in the model you wish to audit

```php
// app/Team.php
namespace App;

use Illuminate\Database\Eloquent\Model;
use OwenIt\Auditing\AuditingTrait;

class Team extends Model 
{
    use AuditingTrait;
    //...
}

```

## With Legacy Class

It is also possible to have your model extend the `OwnerIt\Auditing\Auditing` class to enable auditing. Example:Alternatively, you may also extend the `OwnerIt\Auditing\Auditing` class to enable auditing.

Example:

```php
// app/Team.php
namespace App;

use OwenIt\Auditing\Auditing;

class Team extends Auditing 
{
    //...    
}
```
