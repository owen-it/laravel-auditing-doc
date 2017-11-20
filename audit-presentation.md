# Audit presentation
The metadata and modified data can be accessed in several ways.

## Metadata
The `Audit` data is referred as metadata, since it's information about the modified attributes of an `Auditable` model.

Metadata can be fetched through the `getMetadata()` method as an `array`.

```php
var_dump($audit->getMetadata());
```

**Output:**
```php
array(11) {
  ["audit_id"]=>
  string(1) "1"
  ["audit_event"]=>
  string(7) "updated"
  ["audit_url"]=>
  string(29) "http://example.com/articles/1"
  ["audit_ip_address"]=>
  string(9) "127.0.0.1"
  ["audit_user_agent"]=>
  string(68) "Mozilla/5.0 (X11; Linux x86_64; rv:53.0) Gecko/20100101 Firefox/53.0"
  ["audit_tags"]=>
  array(2) {
    [0] =>
    string(3) "foo"
    [1] =>
    string(3) "bar"
  }
  ["audit_created_at"]=>
  string(19) "2017-01-01 01:02:03"
  ["audit_updated_at"]=>
  string(19) "2017-01-01 01:02:03"
  ["user_id"]=>
  string(1) "1"
  ["user_email"]=>
  string(15) "bob@example.com"
  ["user_name"]=>
  string(3) "Bob"
}
```

Passing `true` as the first argument will convert the data into its **JSON** representation.

```php
echo $audit->getMetadata(true, JSON_PRETTY_PRINT);
```

> {tip} The second argument accepts a bitmask of options. To know more, read the [`json_encode()`](http://php.net/manual/en/function.json-encode.php) function documentation.

**Output:**
```json
{
    "audit_id": "1",
    "audit_event": "updated",
    "audit_url": "http:\/\/example.com\/articles\/1",
    "audit_ip_address": "127.0.0.1",
    "audit_user_agent": "Mozilla/5.0 (X11; Linux x86_64; rv:53.0) Gecko/20100101 Firefox/53.0",
    "audit_tags": [
        "foo",
        "bar"
   ],
    "audit_created_at": "2017-01-01 01:02:03",
    "audit_updated_at": "2017-01-01 01:02:03",
    "user_id": "1",
    "user_email":"bob@example.com",
    "user_name":"Bob"
}
```

> {note} Depending on the `$visible` property of the `User` model, other `user_` prefixed attributes may become available.

Lastly, the `Audit` attributes can also be accessed directly, like in any other `Eloquent` model:

```php
echo $audit->id.PHP_EOL;
echo $audit->event.PHP_EOL;
echo $audit->url.PHP_EOL;
echo $audit->ip_address.PHP_EOL;
echo $audit->user_agent.PHP_EOL;
echo implode(',', $audit->tags).PHP_EOL;
echo $audit->created_at->toDateTimeString().PHP_EOL;
echo $audit->updated_at->toDateTimeString().PHP_EOL;
echo $audit->user_id.PHP_EOL;
echo $audit->user->email.PHP_EOL;
echo $audit->user->name.PHP_EOL;
```

**Output:**
```txt
1
updated
http://example.com/articles/1
127.0.0.1
Mozilla/5.0 (X11; Linux x86_64; rv:53.0) Gecko/20100101 Firefox/53.0
foo,bar
2017-01-01 01:02:03
1
bob@example.com
Bob
```

## Modified
The modified data is a set of `Auditable` model attributes that changed since the last event: `created`, `updated`, `deleted` and `restored`.

Only modified attributes will make part of the `array`, which consists in the **old** and **new** values for each one.

```php
var_dump($audit->getModified());
```

**Output:**
```php
array(2) {
  ["title"]=>
  array(2) {
    ["new"]=>
    string(28) "How To Audit Eloquent Models"
    ["old"]=>
    string(19) "How to audit models"
  }
  ["content"]=>
  array(2) {
    ["new"]=>
    string(56) "First, start by installing the laravel-auditing package."
    ["old"]=>
    string(16) "This is a draft."
  }
}
```

Like with the `getMetadata()` method, passing `true` as the first argument will convert the data into a **JSON** string.

```php
echo $audit->getModified(true);
```

**Output:**
```json
{"title":{"new":"How To Audit Eloquent Models","old":"How to audit models"},"content":{"new":"First, start by installing the laravel-auditing package.","old":"This is a draft."}}
```

> {tip} The `getModified()` method honors all attribute **mutators** and/or **casts** defined in the `Auditable` model, transforming the data accordingly.

Direct access to the **old** and **new** values is also possible:

```php
var_dump($audit->old_values, $audit->new_values);
```

**Output:**
```php
array(2) {
  ["title"]=>
  string(19) "How to audit models"
  ["content"]=>
  string(16) "This is a draft."
}
array(2) {
  ["title"]=>
  string(28) "How To Audit Eloquent Models"
  ["content"]=>
  string(56) "First, start by installing the laravel-auditing package."
}
```

## Examples
To better understand how the `Audit` methods can be used, here are some presentation examples.

### Listing Audits
Displaying a `Collection` of `Audit` models in an HTML unordered list.

`Article` translation file:

```php
return [
    // ...

    'unavailable_audits' => 'No Article Audits available',

    'updated'            => [
        'metadata' => 'On :audit_created_at, :user_name [:audit_ip_address] updated this record via :audit_url',
        'modified' => [
            'title'   => 'The Title has been modified from <strong>:old</strong> to <strong>:new</strong>',
            'content' => 'The Content has been modified from <strong>:old</strong> to <strong>:new</strong>',
        ],
    ],

    // ...
];
```

Blade template:

```php
<ul>
    @forelse ($audits as $audit)
    <li>
        @lang('article.updated.metadata', $audit->getMetadata())

        @foreach ($audit->getModified() as $attribute => $modified)
        <ul>
            <li>@lang('article.'.$audit->event.'.modified.'.$attribute, $modified)</li>
        </ul>
        @endforeach
    </li>
    @empty
    <p>@lang('article.unavailable_audits')</p>
    @endforelse
</ul>
```

> {tip} The use of pagination should be taken into account when dealing with large `Audit` collections.

### Displaying a single Audit
Present a single `Audit` using a _diff like_ view.

Common translation file:

```php
return [
    // ...

    'attribute'  => 'Attribute',
    'event'      => 'Event',
    'id'         => 'ID',
    'ip_address' => 'IP Address',
    'user_agent' => 'User Agent',
    'new'        => 'New',
    'old'        => 'Old',
    'url'        => 'URL',
    'user'       => 'User',
    'tags'       => 'Tags',

    // ...
];
```

Vue app to set the `data`:
```javascript
new Vue({
    el: '.container',
    data: {
        metadata: JSON.parse(document.getElementById('article').getAttribute('data-metadata')),
        modified: JSON.parse(document.getElementById('article').getAttribute('data-modified'))
    }
});
```

Blade template with Vue data bindings:

```php
<div id="article" class="container" data-metadata='{!! $audit->getMetadata(true) !!}' data-modified='{!! $audit->getModified(true) !!}'>
    <div v-model="metadata">
        <div class="row">
            <div class="col-md-3">
                <strong>@lang('common.id')</strong>
            </div>
            <div class="col-md-9">@{{ metadata.audit_id }}</div>
        </div>
        <div class="row">
            <div class="col-md-3">
                <strong>@lang('common.event')</strong>
            </div>
            <div class="col-md-9">@{{ metadata.audit_event }}</div>
        </div>
        <div class="row">
            <div class="col-md-3">
                <strong>@lang('common.user')</strong>
            </div>
            <div class="col-md-9">@{{ metadata.user_name }}</div>
        </div>
        <div class="row">
            <div class="col-md-3">
                <strong>@lang('common.ip_address')</strong>
            </div>
            <div class="col-md-9">@{{ metadata.audit_ip_address }}</div>
        </div>
        <div class="row">
            <div class="col-md-3">
                <strong>@lang('common.user_agent')</strong>
            </div>
            <div class="col-md-9">@{{ metadata.audit_user_agent }}</div>
        </div>
        <div class="row">
            <div class="col-md-3">
                <strong>@lang('common.tags')</strong>
            </div>
            <div class="col-md-9">@{{ metadata.audit_tags.join() }}</div>
        </div>
        <div class="row">
            <div class="col-md-3">
                <strong>@lang('common.url')</strong>
            </div>
            <div class="col-md-9">@{{ metadata.audit_url }}</div>
        </div>
    </div>

    <hr/>

    <table class="table table-striped">
        <thead>
            <tr>
                <th>@lang('common.attribute')</th>
                <th>@lang('common.old')</th>
                <th>@lang('common.new')</th>
            </tr>
        </thead>
        <tbody>
            <tr v-for="(value, attribute) in modified">
                <td><strong>@{{ attribute }}</strong></td>
                <td class="danger">@{{ value.old }}</td>
                <td class="success">@{{ value.new }}</td>
            </tr>
        </tbody>
    </table>
</div>
```
