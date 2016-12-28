# Displaying Audits

The `Audit` logs can be fetched very easily, via [Eloquent](https://laravel.com/docs/master/eloquent) relations.

## Retrieving audit logs

Using the `Invoice` model from previous examples, here's how it works:

```php
// Get first available Invoice
$invoice = Invoice::first();

// Get all associated audits
$all = $invoice->audits;

// Get first audit
$first = $invoice->audits->first();

// Get last audit
$last = $invoice->audits->last();

// Get audit by id
$audit = $invoice->audits->find(4);
```

## Getting audit logs with the associated `User` model

Remember to properly set the `User` model and resolver in the `config/audit.php` file.

```php
// Get all associated audits
$all = $invoice->audits()->with('user')->get();
```

## Getting the Audit metadata
Get an `array` with the `Audit` metadata.

```php
// Get first available Invoice
$invoice = Invoice::first();

// Get latest audit
$audit = $invoice->audits->last();

var_dump($audit->getMetadata());
```

**Output:**
```php
array(6) {
  ["audit_id"]=>
  string(36) "082b4781-4b94-41f1-9f12-fb1c7d1c9b3d"
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
}
```

## Getting the modified properties
Get an `array` with the modified properties of the `Auditable` model.
The data includes the **old** and **new** values for each attribute.

```php
// Get first available Invoice
$invoice = Invoice::first();

// Get latest audit
$audit = $invoice->audits->last();

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
