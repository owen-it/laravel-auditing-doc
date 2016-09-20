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

Remember to properly define the user model in the file ``` config/auditing.php ```.

```php

    // Get team
    $team = Team::find(1); 
    
    // Get all logs
    $team->logs->with('user'); 
```

### Getting custom message

Set your custom messages in [customizing log message](/docs/{{version}}/customizing)

```php
    // Get team
    $team = Team::find(1); 

    // Get first log
    $log = $team->logs->first(); 
    
    $log->customMessage;
    
    // Antério Vieira created a post 1 day ago 
```

### Getting custom fields

Set your custom fields in [customizing log fields](/docs/{{version}}/customizing)

```php
    // Get team
    $team = Team::find(1); 

    // Get first log
    $log = $team->logs->first(); 
    
    $log->customFields;
    
    /* 
        [
            'name' => 'The name was defined as "Team Laravel"'
            'address' => 'Registered from the address 127.0.0.1'
        ]
    */
```

