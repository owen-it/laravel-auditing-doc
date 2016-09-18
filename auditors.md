
# Auditors

The auditor's role is to audit when the data in the model changes. The methodologies to audit the models can be developed by developer himself.

## Creating Auditors

In Laravel Auditing, each auditor is represented by a single class (typically stored in the `app/Auditors`) directory. To create your auditors run the following command:

`php artisan make:auditor MyAuditor`

This command will place a fresh auditor class template in your `app/Auditors` directory. Each auditor class contains a `audit` method responsible for auditing the [Eloquent model](https://laravel.com/docs/master/eloquent).


```php
<?php
namespace App\Auditors;

use OwenIt\Auditing\Contracts\Dispatcher as AuditingContract

class MyAuditor implements AuditingContract
{
	public function audit ($auditable)
	{
		// Implement your audit logic
	}
}
```

> {tip} The return of the `audit()` method will be sent as a parameter to the event `AuditReport`. Check [Events](/docs/{{version}}/events) for more information.
