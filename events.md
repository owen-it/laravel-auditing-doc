# Reviewing audit.

We may revise the model before it is audited and say whether it needs to or not be audited.


```php
<?php
namespace App\Listeners;

use OwenIt\Auditing\Events\AuditReview;

class ReviewInvoiceAudits
{
    /**
     * Create the event listener.
     *
     * @return void
     */
    public function __construct()
    {
        //
    }

    /**
     * Handle the event.
     *
     * @param  AuditReview $event
     * @return void
     */
    public function handle(AuditReview $event)
    {
        // Your implamentation 
    }
}
```

# Audit report

In some cases it may be interesting to take immediate action depending on the audit report. Suppose that the company in question that we are auditing handles an X value for your purchases and sales, this value ranges from 0 to 10000. Our example will ensure that the company's manager is notified if any invoice is issued to the different value of the value that we specify.

```php
namespace App\Listeners;

use OwenIt\Auditing\Events\AuditReport;

class ReportInvoiceAudits
{

    protected $company;
    
    /**
     * Create the event listener.
     *
     * @return void
     */
    public function __construct(Company $company)
    {
        $this->company = $company;
    }

    /**
     * Handle the event.
     *
     * @param AuditReport $event
     * @return void
     */
    public function handle(AuditReport $event)
    {
        if($event->auditable->value > 10000.00){
            $this->company->notify()
        }
    }
}
```


> For more information about events visit the official documentation of [Laravel](https://laravel.com/docs/master/events)
