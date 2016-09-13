
# Customizing log message

You can define your own log messages for presentation. These messages can be defined for both the model as well as for each one of fields.The dynamic part of the message can be done by targeted fields per dot segmented as `{object.property.property}` or  `{object.property|Default value}` or `{object.property||callbackMethod}`. 

> Note: This implementation is optional, you can make these customizations where desired.

Set messages to the model
```php
<?php

namespace App\Models;

use OwenIt\Auditing\Auditable;
use Illuminate\Database\Eloquent\Model;

class Post extends Model
{
  use Auditable;    

    // with default value
    public static $logCustomMessage = '{user.name|Anonymous} {type} a post {elapsed_time}'; 
    
    // with callback method
    public static $logCustomFields = [
        'title'  => 'The title was defined as "{new.title||getNewTitle}"', 
        'ip' => 'Registered from the address {ip||getAnotherthing}',
        'publish_date' => [
            'created' => 'Publication date: {new.publish_date}',
            'delete' =>  'Post removed from {new.publish_date}'
        ]
    ];
    
    public function getNewTitle($log)
    {
        return $log->old['title'];
    }
    
    public function getAnotherthing($log)
    {
        return ';)';
    }
    
    //...
}
```
Getting change logs 
```php
// app/Http/Controllers/MyAppController.php 
    
    //...
    public function auditing()
    {
        // Get logs of Post
        $logs = Post::find(1)->logs;
        
        return view('admin.auditing', compact('logs'));
    }
    //...
    
```
Featuring log records:
```
    // resources/views/admin/auditing.blade.php
    ...
    <ol>
        @forelse ($logs as $log)
            <li>
                {{ $log->customMessage }}
                <ul>
                    @forelse ($log->customFields as $custom)
                        <li>{{ $custom }}</li>
                    @empty
                        <li>No details</li>
                    @endforelse
                </ul>
            </li>
        @empty
            <p>No logs</p>
        @endforelse
    </ol>
    ...
    
```
Result:
<ol>
  <li>Antério Vieira created a post 1 day ago   
    <ul>
      <li>The title was defined as "Did someone say rapid?"</li>
      <li>Registered from the address 192.168.10.1</li>
      <li>Publication date: 2016-05-25 00:49:26.0</li>
    </ul>
  </li>
  <li>Antério Vieira updated a post 1 day ago   
    <ul>
      <li>The title was updated as "Did someone say rapid?"</li>
      <li>Registered from the address 192.168.10.1</li>
      <li>The post publication date has been updated from 2016-05-20 00:49:26.0 to 2016-05-25 00:49:26.0</li>
    </ul>
  </li>
  <li>Raphael França deleted a post 2 day ago   
    <ul>
      <li>Registered from the address 192.168.10.1</li>
    </ul>
  </li>  
  <li>...</li>
</ol>
