
# Customizing audits messages

You can define your own audit messages for presentation. These messages can be defined for both the model as well as for each of fields. The dynamic properties of the message can be specified by targeted fields using dot syntax like `{object.property.property}` or  `{object.property|Default value}` or `{object.property||callbackMethod}`. 

> {tip} This step is optional, you can make these customizations where desired.

Create a translation file for your model.

```php
<?php

return [
    'audit' => [
        // with default value
        'message' => '{user.name|Anonymous} {type} a post {elapsed_time}',
        'fields'  => [
            // with callback method
            'title'        => 'The title was defined as "{new.title||getNewTitle}"',
            'ip_address'   => 'Registered from the address {ip_address}',
            'publish_date' => [
                'created' => 'Publication date: {new.publish_date}',
                'deleted' => 'Post removed from {new.publish_date}',
            ],
        ],
    ],
];
```

Set the `$auditCustomMessage` and `$auditCustomFields` properties of your model to the corresponding language lines.

```php
<?php

namespace App\Models;

use OwenIt\Auditing\Auditable;
use Illuminate\Database\Eloquent\Model;

class Post extends Model
{
  use Auditable;    

    public static $auditCustomMessage = 'post.audit.message';

    public static $auditCustomFields = 'post.audit.fields';

    public function getNewTitle($post)
    {
        return $post->old['title'];
    }
    
    //...
}
```

### Fetching audits  

```php
// app/Http/Controllers/MyAppController.php 
    
    //...
    public function auditing()
    {
        // Fetch audits of a Post
        $audits = Post::find(1)->audits;
        
        return view('admin.auditing', compact('audits'));
    }
    //...
    
```

### Displaying audits

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
