
# Auditors

## Creating Auditors

In Laravel Auditing, each auditor is represented by a single class (typically stored in the `app/Auditors`) directory. To create your auditors run the following command:

`php artisan make:auditor MyAuditor`

This command will place a fresh auditor class in your `app/Auditors` directory. Each auditor class contains a `audit` method responsible for auditing the [eloquent model](https://laravel.com/docs/5.3/eloquent).


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