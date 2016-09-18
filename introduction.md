# Introduction

This package can help you understand the activities in your eloquent models and provide information about possible discrepancies and anomalies that could indicate business concerns or suspected security breaches. 

Laravel Auditing allows you to record changes to an Eloquent model's set of data by simply adding its trait to your model. It also provides you with a simple interface to retrieve the data audited with a variety of features to display them in various ways. Laravel Auditing simply follows a basic audit process. It will identify changes in your models and appoint an auditor responsible for auditing.

If you have [installed the package](/docs/{{version}}/installation) already, lets start using it.

Making a model auditable is simple. Use the trait `OwnerIt\Auditing\Auditable` in the model you wish to audit

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

By default, Laravel Auditing will use the Database Auditor to record the changes in the model. If needed, you can also [create your own auditors](/docs/{{version}}/auditors).
