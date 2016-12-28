# Audit presentation

Keeping a history of changes for a model is only half of it. The next step is to display them to the user.
To aid in such task, there are a few methods that the `Audit` model provides.

## Metadata

By default, the `Audit` metadata can be fetched via the `getMetadata()` method as an `array`.

```php
var_dump($audit->getMetadata());
```

**Output:**
```php
array(8) {
  ["audit_id"]=>
  string(36) "123b4567-4b94-41f1-9f12-fb1c7d1c9b3d"
  ["audit_event"]=>
  string(7) "updated"
  ["audit_url"]=>
  string(29) "http://example.com/my/profile"
  ["audit_ip_address"]=>
  string(9) "127.0.0.1"
  ["audit_created_at"]=>
  string(19) "2017-01-01 01:02:03"
  ["user_id"]=>
  string(1) "1"
  ["user_email"]=>
  string(15) "bob@example.com"
  ["user_name"]=>
  string(3) "Bob"
}
```

An easy way to display the metadata using translations would be:

```php
// ...
'updated' => [
    'metadata' => 'On :audit_created_at, :user_name [:audit_ip_address] updated this record via :audit_url',
],
// ...
```

```html
<ol>
    @forelse ($audits as $audit)
        <li>
            @lang('user.updated.metadata', $audit->getMetadata())
            <ul>
                ...
            </ul>
        </li>
    @empty
    <p>No Audits available</p>
    @endforelse
</ol>
```

By passing `true` as an argument, the metadata will be converted to a **JSON** object,

```html
<div id="invoice" data-metadata='{!! $log->getMetadata(true) !!}'>
</div>
```

making it easy to be consumed from the **DOM** via JavaScript

```js
var metadata = JSON.parse(document.getElementById('invoice').getAttribute('data-metadata'));
```

> {note} Depending on the `$visible` property of the `User` model, other `user_` attributes may be available.

Lastly, the `Audit` properties can also be accessed directly from the model, as like any other `Eloquent` object:

```php
@lang('user.updated.metadata', [
    'audit_created_at' => (string) $audit->created_at,
    'user_name'        => $audit->user->name,
    'audit_ip_address' => $audit->ip_address,
    'audit_url'        => $audit->url,
])
```

## Modified

The `getModified()` method works in the same fashion as the `getMetadata()` one, meaning it will return an `array` with the `Auditable` model modified attributes by default.
Each of the attributes will have the **old** and **new** values.

```php
var_dump($audit->getModified());
```

**Output:**
```php
array(2) {
  ["name"]=>
  array(2) {
    ["new"]=>
    string(3) "Bob"
    ["old"]=>
    string(6) "Robert"
  }
  ["email"]=>
  array(2) {
    ["new"]=>
    string(15) "bob@example.com"
    ["old"]=>
    string(18) "robert@example.com"
  }
}
```

> {tip} The resolver will honor any **casts** or **mutators** that the `Auditable` model may have defined and apply them the corresponding attributes.

Displaying changes to the user, can be as simple as setting the translation file:

```php
// ...
'updated' => [
    'modified' => [
        'name'  => 'The name has been modified from <strong>:old</strong> to <strong>:new</strong>',
        'email' => 'The email address has been modified from <strong>:old</strong> to <strong>:new</strong>',
    ],
],
// ...
```

and adding the following markup to a view:

```html
...
<li>
    @foreach ($audit->getModified() as $attribute => $modified)
    <ul>
        @lang('user.'.$audit->event.'.modified.'.$attribute, $modified)
    </ul>
    @endforeach
</li>
...
```

If needed, the data can be converted to **JSON** by passing `true` as an argument to the method.

```php
echo $audit->getModified(true);
```

**Output:**
```json
{"name":{"new":"Bob","old":"Robert"},"email":{"new":"bob@example.com","old":"robert@example.com"}}
```

## Full example

Translation file
```php
// ...
'updated' => [
    'metadata' => 'On :audit_created_at, :user_name [:audit_ip_address] updated this record via :audit_url',
    'modified' => [
        'name'  => 'The name has been modified from <strong>:old</strong> to <strong>:new</strong>',
        'email' => 'The email address has been modified from <strong>:old</strong> to <strong>:new</strong>',
    ],
],
// ...
```

View file
```html
<ol>
    @forelse ($audits as $audit)
    <li>
        @lang('user.updated.metadata', $audit->getMetadata())

        @foreach ($audit->getModified() as $attribute => $modified)
        <ul>
            @lang('user.'.$audit->event.'.modified.'.$attribute, $modified)
        </ul>
        @endforeach
    </li>
    @empty
    <p>No Audits available</p>
    @endforelse
</ol>
```
