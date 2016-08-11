# Introduction

Laravel Auditing allows you to record changes to an Eloquent model's set of data by simply adding its trait to your model. Laravel Auditing also provides a simple interface for retreiving an audit trail for a piece of data and allows for a great deal of customization in how that data is provided.

To register the change log, use the trait `OwnerIt\Auditing\AuditingTrait` in the model you wish to audit

```php
// app/Team.php
namespace App;

use Illuminate\Database\Eloquent\Model;
use OwenIt\Auditing\AuditingTrait;

class Team extends Model 
{
    use AuditingTrait;
    //
}
```
Alternatively, you may also extend the `OwnerIt\Auditing\Auditing` class to enable auditing.

Example:

```php
// app/Team.php
namespace App;

use OwenIt\Auditing\Auditing;

class Team extends Auditing 
{
    // 
}
```

Before that, you should [install the package](/docs/{{version}}/installation).
