
# Customizing audits messages

You can define your own audit message for presentation. These messages can be defined for both the model as well as for each one of fields.The dynamic part of the message can be done by targeted fields per dot segmented as `{object.property.property}` or  `{object.property|Default value}` or `{object.property||callbackMethod}`. 

> Note: This implementation is optional, you can make these customizations where desired.

### Set messages to the model

```php
<?php

namespace App\Models;

use OwenIt\Auditing\Auditable;
use Illuminate\Database\Eloquent\Model;

class Post extends Model
{
  use Auditable;    

    // with default value
    public static $auditCustomMessage = '{user.name|Anonymous} {type} a post {elapsed_time}'; 
    
    // with callback method
    public static $auditCustomFields = [
        'title'  => 'The title was defined as "{new.title||getNewTitle}"', 
        'ip_address' => 'Registered from the address {ip_address}',
        'publish_date' => [
            'created' => 'Publication date: {new.publish_date}',
            'deleted' => 'Post removed from {new.publish_date}'
        ]
    ];
    
    public function getNewTitle($post)
    {
        return $post->old['title'];
    }
    
    //...
}
```

### Getting audits  

```php
// app/Http/Controllers/MyAppController.php 
    
    //...
    public function auditing()
    {
        // Get audits of Post
        $autits = Post::find(1)->audits;
        
        return view('admin.auditing', compact('autits'));
    }
    //...
    
```

### Featuring audits

```
    // resources/views/admin/auditing.blade.php
    ...
    <ol>
        @forelse ($audits as $audit)
            <li>
                {{ $audit->customMessage }}
                <ul>
                    @forelse ($audit->customFields as $custom)
                        <li>{{ $custom }}</li>
                    @empty
                        <li>No details</li>
                    @endforelse
                </ul>
            </li>
        @empty
            <p>No audits</p>
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
