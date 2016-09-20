# Configuration

## Behavior Settings

The Auditing behavior settings are carried out with the declaration of attributes in the model. See the examples below:

* Disable / enable logging: `$auditEnabled = false`
* Clear the oldest records: `$historyLimit = 100`
* Turn off logging for specific fields: `$dontKeepLogOf = ['created_at', 'updated_at']`
* Tell what actions you want to audit: `$auditableTypes = ['created', 'saved', 'deleted']`

> {tip} This implementation is optional, you can make these customizations where desired.

```php
// app/Team.php
namespace App;

use Illuminate\Database\Eloquent\Model;

class Team extends Model 
{
    use OwenIt\Auditing\AuditingTrait;
    
    // Disables the log record in this model.
    protected $auditEnabled  = false;
    
    // Clear the oldest records after 100 records.
    protected $historyLimit = 100; 
    
    // Fields that you do NOT want to register.
    protected $dontKeepLogOf = ['created_at', 'updated_at'];
    
    // Tell what actions you want to audit.
    protected $auditableTypes = ['created', 'saved', 'deleted'];
}
```
## General settings
Using the configuration file, you can define:

* The Model used to represent the current user of application.
* A different database connection for audit.
* The table name used for log registers.
    
The configuration file can be found at `config/auditing.php`.

> {note} This implementation is optional.

```php
// config/auditing.php
return [

    // Authentication Model
    'model' => App\User::class,

    // Database Connection
    'connection' => null,

    // Table Name
    'table' => 'logs',
];
```
