# Audit Tags
Version **5.0.0** brings the ability to tag `Audit` records, making it convenient for filtering.

## Generating tags
The `Auditable` interface defines a `generateTags()` method for generating tags in each model.

That method should return an `array` of strings, which will be converted into a comma separated values string on save.

By default, the `Auditable` trait implementation will return an empty `array`, which results in `NULL`, once the `Audit` is stored.

Implementation:
```php
<?php
namespace App\Models;

use App\AuditDrivers\MyCustomDriver;
use Illuminate\Database\Eloquent\Model;
use OwenIt\Auditing\Contracts\Auditable;

class Article extends Model implements Auditable
{
    use \OwenIt\Auditing\Auditable;

    /**
     * {@inheritdoc}
     */
    public function generateTags(): array
    {
        return [
            $this->editor->name,
            $this->reporter->name,
            $this->designer->name,
            $this->photographer->name,
        ];
    }

    // ...
}
```
