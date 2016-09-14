# Introduction

The audit can help you understand the activity in the eloquent models and information about discrepancies and anomalies that could indicate business concerns or suspected security breaches. 

Laravel Auditing allows you to record changes to an Eloquent model's set of data by simply adding its trait to your model. It also provides us with a simple interface to retrieve the data audited with a variety of features to customize the display of audits.

Laravel Audit expressed simply a basic audit process. It will identify changes in their models and appoint an auditor responsible for auditing.

To make an auditable model is simple, use the trait `OwnerIt\Auditing\Auditable` in the model you wish to audit

```php

namespace App;

use OwenIt\Auditing\Auditable;
use Illuminate\Database\Eloquent\Model;

class Invoice extends Model
{
    use Auditable;

    // ...
}
```
By default Laravel-Audit will use the database auditor to record the changes in the model. But you can [create your own auditors](/docs/{{version}}/auditors).

Before that, you should [install the package](/docs/{{version}}/installation).