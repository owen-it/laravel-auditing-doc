# Behavior Settings

The Auditing behavior settings are carried out with the declaration of attributes in the model. See the examples below:

* Disable / enable auditing: `$auditEnabled = false`
* Clear older history: `$auditLimit = 100`
* Indicate which fields should not be audited: `$dontKeepLogOf = ['field']`
* Indicate which fields are to be audited: `$keepAuditOf = ['field']`

> Note: This implementation is optional, you can make these customizations where desired.

```php
<?php

namespace App;

use OwenIt\Auditing\Auditable;
use Illuminate\Database\Eloquent\Model;

class Invoice extends Model 
{
    use Auditable;

    // Disables the auditing in this model.
    protected $auditEnabled  = false;

    // Clear the oldest audits after 100 records.
    protected $auditLimit = 100; 

    // Fields that you do NOT want to audit.
    protected $dontKeepAuditOf = ['created_at', 'updated_at'];

    // Tell what actions you want to audit.
    protected $auditableTypes = ['created', 'saved', 'deleted'];
}
```
