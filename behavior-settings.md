# Behavior Settings

The Auditing behavior settings are carried out with the declaration of attributes in the model. See the examples below:

* Number of records to maintain before deleting older entries: `$auditLimit = 100`
* Determine which fields should not be audited: `$dontKeepAuditOf = ['field']`
* Or determine which fields should be audited: `$keepAuditOf = ['field']`
* Determine the actions to be audited: `$auditableTypes = ['created', 'saved', 'deleted']`

> {tip} This step is optional. you can make these customizations only where desired.

```php
<?php

namespace App;

use OwenIt\Auditing\Auditable;
use Illuminate\Database\Eloquent\Model;

class Invoice extends Model 
{
    use Auditable;

    // Clear the oldest audits after 100 records.
    protected $auditLimit = 100; 

    // Fields that you do NOT want to audit.
    protected $dontKeepAuditOf = ['created_at', 'updated_at'];

    // Specify what actions you want to audit.
    protected $auditableTypes = ['created', 'saved', 'deleted'];
}
```
