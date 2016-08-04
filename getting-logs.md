# Getting the Logs

What you will see now is nothing less than powerful Eloquent, to learn more click [here](https://laravel.com/docs/5.2/eloquent).

```php
// app/Http/Controller/MyAppController.php
namespace App\Http\Controllers;

use App\Team;

class MyAppController extends BaseController 
{
    public function index()
    {
        // Get team
        $team = Team::find(1); 
        
        // Get all logs
        $team->logs; 
        
        // Get first log
        $team->logs->first(); 
        
        // Get last log
        $team->logs->last();  
        
        // Selects log
        $team->logs->find(2); 
    }
    //...
}
```
Getting logs with user responsible for the change.
```php
use OwenIt\Auditing\Log;

$logs = Log::with(['user'])->get();

```
or
```php
use App\Team;

$logs = Team::logs->with(['user'])->get();

```

> Note: Remember to properly define the user model in the file ``` config/auditing.php ```.

```php
    ...
    'model' => App\User::class,
    ... 
```
