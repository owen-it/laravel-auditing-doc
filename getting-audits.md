# Getting the Audits

What you will see now is nothing less than powerful Eloquent, to learn more click [here](https://laravel.com/docs/master/eloquent).

```php
<?php

namespace App\Http\Controllers;

use App\Repositories\InvoiceRepository;

class MyAppController extends BaseController 
{
    public function index(InvoiceRepository $invoiceRepository)
    {
        // Get invoice
        $invoice = $invoiceRepository->find(1); 
        
        // Get all audits
        $invoice->audits; 
        
        // Get first 
        $invoice->audits->first(); 
        
        // Get last 
        $invoice->audits->last();  
        
    }
    //...
}
```

Getting audits with user responsible for the change.

```php

$audits = Auditing::with(['user'])->get();

```

or

```php

use App\Invoice;

$audits = Invoice::audits->with(['user'])->get();

```

> {note} Remember to properly define the user model in the file `config/auditing.php`.

```php

    ...

    'model' => App\User::class,

    ... 
```

