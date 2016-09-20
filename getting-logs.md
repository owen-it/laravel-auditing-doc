# Getting the Logs

What you will see now is nothing less than powerful [Eloquent Model](https://laravel.com/docs/5.2/eloquent).

### Retrieving audit logs

```php
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
```

### Getting logs with user responsible for the change.

> Note: Remember to properly define the user model in the file ``` config/auditing.php ```.

```php
    ...
    'model' => App\User::class,
    ... 
```

```php

    // Get team
    $team = Team::find(1); 
    
    // Get all logs
    $team->logs->with('user'); 
```




