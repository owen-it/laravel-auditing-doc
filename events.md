# Events

## Registering Events

Typically, events should be registered via the `EventServiceProvider` `$listen` array; however, you may also register events manually with the event dispatcher using either the `Event` facade or the `Illuminate\Contracts\Events\Dispatcher` contract implementation:

```php
    /**
     * Register any other events for your application.
     *
     * @param  \Illuminate\Contracts\Events\Dispatcher  $events
     * @return void
     */
    public function boot(DispatcherContract $events)
    {
        parent::boot($events);
    
        $events->listen('auditing.created', function ($model) {
            //
        });
    }
```
## Wildcard Event Listeners

You may even register listeners using the `*` as a wildcard, allowing you to catch multiple events on the same listener. Wildcard listeners receive the entire event data array as a single argument:

```php
    $events->listen('auditing.*', function ($model) {
        //
    });
```

> For more information about events visit the official documentation of [Laravel](https://laravel.com/docs/5.2/events)
