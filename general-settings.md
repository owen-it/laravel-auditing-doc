# General settings
You can change the package's core behavior by changing the configuration file. Available settings are:

* Specify the model used to represent the current user..
* Specify a different database connection for audit.
* Specify the table name for the audit records. (When DatabaseAuditor)
* Enable / Disable audit when application is performed by the console.
    
The configuration file can be found at `config/auditing.php`.

> {tip} These settings are optional and can be changed when necessary.
> 

```php
// config/auditing.php
return [

    // Authentication Model
    'model' => App\User::class,

    // Database Connection
    'connection' => null,

    // Table Name
    'table' => 'logs',

    // Audit via console
    'audit_console' => false,
];
```

