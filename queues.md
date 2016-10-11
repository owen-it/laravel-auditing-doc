# Audit Queues

While not strictly required to use Auditing, you should strongly consider configuring a [queue driver](https://laravel.com/docs/master/queues) before using the library. Running a queue worker will allow Auditing to queue all operations that audit your model auditable, providing much better response times for your application's web interface.

Once you have configured a queue driver, set the value of the queue option in your config/auditing.php configuration file to true:

```php
'queue' => true,
```
