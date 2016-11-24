# Displaying the Audits
Using [Eloquent](https://laravel.com/docs/master/eloquent), we can fetch the logs very easily.

### Retrieving audit logs

```php
    // Get team
    $team = Team::find(1); 
    
    // Get all audits
    $team->audits; 
    
    // Get first audit
    $team->audits->first(); 
    
    // Get last audit
    $team->audits->last();  
    
    // Selects audit
    $team->audits->find(2); 
```

### Getting logs with user responsible for the change.
Remember to properly define the user model in the file ``` config/auditing.php ```.

```php

    // Get team
    $team = Team::find(1); 
    
    // Get all audits
    $team->audits()->with('user')->get(); 
```

### Getting custom message
Set your custom messages in [customizing log message](/docs/{{version}}/customizing)

```php
    // Get team
    $team = Team::find(1); 

    // Get first audit
    $audit = $team->audits->first(); 
    
    $audit->customMessage;
    
    // Antério Vieira created a post 1 day ago 
```

### Getting custom fields
Set your custom fields in [customizing log fields](/docs/{{version}}/customizing)

```php
    // Get team
    $team = Team::find(1); 

    // Get first audit
    $audits = $team->audits->first(); 
    
    $audits->customFields;
    
    /* 
        [
            'name' => 'The name was defined as "Team Laravel"'
            'address' => 'Registered from the address 127.0.0.1'
        ]
    */
```

