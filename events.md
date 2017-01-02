# Events

During and after the audit process, two events are fired.

## Auditing

This event is fired while the auditing is being processed. If needed, the `Audit` can be canceled by returning `false` from the event listener `handle()` method.


```php
<?php

namespace App\Listeners;

use OwenIt\Auditing\Events\Auditing;

class AuditingListener
{
    /**
     * Create the Auditing event listener.
     */
    public function __construct()
    {
        // ...
    }

    /**
     * Handle the Auditing event.
     *
     * @param \OwenIt\Auditing\Events\Auditing $event
     * @return void
     */
    public function handle(Auditing $event)
    {
        // Implement logic
    }
}
```

## Audited

This other event, happens when the `Audit` has just been created.
In case some immediate action needs to take place after the event, this is where it should go.

```php
<?php

namespace App\Listeners;

use OwenIt\Auditing\Events\Audited;

class AuditedListener
{
    /**
     * Create the Audited event listener.
     */
    public function __construct()
    {
        // ...
    }

    /**
     * Handle the Audited event.
     *
     * @param \OwenIt\Auditing\Events\Audited $event
     * @return void
     */
    public function handle(Audited $event)
    {
        // Implement logic
    }
}
```

> {tip} For more information about events, check Laravel's official [documentation](https://laravel.com/docs/master/events)!
