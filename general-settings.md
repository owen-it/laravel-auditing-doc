# General settings
You can change the package's core behavior by changing the configuration file. Available settings are:

* Specify a different database connection for audit.
* Specify the table name for the audit records. (When using DatabaseAuditor)
* Decide whether audit should be performed or not when execution is done from the console.
* Specify the default auditor used to audit the eloquent model.
    
The configuration file can be found at `config/auditing.php`.

> {tip} These settings are optional and can be changed when necessary.

```php
// config/auditing.php
return [

    // Database Connection
    'connection' => null,

    // Table Name
    'table' => 'audits',

    // Audit via console
    'audit_console' => false,
    
    // Default auditor 
    'default_auditor' => 'database',
    
];
```
