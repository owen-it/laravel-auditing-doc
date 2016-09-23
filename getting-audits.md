# Displaying the Audits

Using [Eloquent](https://laravel.com/docs/master/eloquent), we can fetch the logs very easily.

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

To include the user responsible for the change, use `with()`. Following example fetches all the changes made by the logged in user in auditable models.

```php

$audits = Auditing::with(['user'])->get();

```

Following code will fetch the changes made by logged in user in `Invoice` model.

```php

use App\Invoice;

$audits = Invoice::audits->with(['user'])->get();

```

> {note} If your authentication model is different than `User`, you can define in the file `config/auditing.php`.

```php

    ...

    'model' => App\User::class,

    ... 
```

