# Audit presentation

With `Audit` records in the database, the next step is to display them to the user.
To aid in such task, the `Audit` model provides a few methods.

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
  string(26) "http://example.com/posts/1"
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

Displaying the metadata using translations for the `updated` event:

```php
// ...
'updated' => [
    'metadata' => 'On :audit_created_at, :user_name [:audit_ip_address] updated this record via :audit_url',
],
// ...
```

Blade template to display a `Collection` of `Audit` models:

```html
<ol>
    @forelse ($audits as $audit)
    <li>
        @lang('post.updated.metadata', $audit->getMetadata())
        <ul>
            ...
        </ul>
    </li>
    @empty
    <p>No Audits available</p>
    @endforelse
</ol>
```

By passing `true`, the metadata will be converted to a **JSON** object instead,

```html
<div id="post" data-metadata='{!! $log->getMetadata(true) !!}'>
</div>
```

making it easy to work with the data in the **DOM** via JavaScript.

```js
var metadata = JSON.parse(document.getElementById('post').getAttribute('data-metadata'));
```

> {note} Depending on the `$visible` property of the `User` model, other `user_` attributes may be available.

Lastly, the `Audit` properties can also be accessed directly from the model, as like any other `Eloquent` object:

```php
@lang('post.updated.metadata', [
    'audit_created_at' => (string) $audit->created_at,
    'user_name'        => $audit->user->name,
    'audit_ip_address' => $audit->ip_address,
    'audit_url'        => $audit->url,
])
```

## Modified

The `getModified()` method works in the same fashion as its `metadata` counterpart, meaning it will return an `array` by default.
Each of the modified `Auditable` model attributes will have the **old** and **new** values.

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
    string(62) "First, let's start by installing the laravel-auditing package."
    ["old"]=>
    string(16) "This is a draft."
  }
}
```

> {tip} The resolver will honor any **mutators** or **casts** that the `Auditable` model may have defined.

Displaying changes to the user, can be as simple as setting the translation file:

```php
// ...
'updated' => [
    'modified' => [
        'title'   => 'The title has been modified from <strong>:old</strong> to <strong>:new</strong>',
        'content' => 'The content has been modified from <strong>:old</strong> to <strong>:new</strong>',
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
        @lang('post.'.$audit->event.'.modified.'.$attribute, $modified)
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
{"title":{"new":"How To Audit Eloquent Models","old":"How to audit models"},"content":{"new":"First, let's start by installing the laravel-auditing package.","old":"This is a draft."}}
```

## Full example

Translation for the `updated` event:

```php
// ...
'updated' => [
    'metadata' => 'On :audit_created_at, :user_name [:audit_ip_address] updated this record via :audit_url',
    'modified' => [
        'title'   => 'The title has been modified from <strong>:old</strong> to <strong>:new</strong>',
        'content' => 'The content has been modified from <strong>:old</strong> to <strong>:new</strong>',
    ],
],
// ...
```

Blade template to display a `Collection` of `Audit` models:

```html
<ol>
    @forelse ($audits as $audit)
    <li>
        @lang('post.updated.metadata', $audit->getMetadata())

        @foreach ($audit->getModified() as $attribute => $modified)
        <ul>
            @lang('post.'.$audit->event.'.modified.'.$attribute, $modified)
        </ul>
        @endforeach
    </li>
    @empty
    <p>No Audits available</p>
    @endforelse
</ol>
```
