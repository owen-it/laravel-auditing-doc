# Auditor
The `Auditor` class is responsible for auditing and clearing `Audit` records.

Usually, there's no need to use it directly, since the `AuditableObserver` takes care of things for us.

Yet, should the need to call it manually arise, here is how it _could_ be done.

## Using the Auditor Facade
```php
<?php
namespace App\Http\Controllers;

use App\Http\Requests\Request;
use App\Models\Article;
use OwenIt\Auditing\Facades\Auditor;

class ArticleController extends Illuminate\Routing\Controller
{
    public function update(Request $request, Article $article)
    {
        $article->update($request->all());

        if ($audit = Auditor::execute($article)) {
            Auditor::prune($article);
        }
    }
}
```

## Injecting the Auditor as a dependency
With the `AuditingServiceProvider` registered, we can use the IoC to resolve and inject the `Auditor` into a method.

```php
<?php
namespace App\Http\Controllers;

use App\Http\Requests\Request;
use App\Models\Article;
use OwenIt\Auditing\Contracts\Auditor;

class ArticleController extends Illuminate\Routing\Controller
{
    public function update(Request $request, Article $article, Auditor $auditor)
    {
        $article->update($request->all());

        if ($audit = $auditor->execute($article)) {
            $auditor->prune($article);
        }
    }
}
```

> {note} Business logic shouldn't go into a Controller! These are _just_ examples on how to manually call the `Auditor`. Also keep in mind that, unless you set the model's `$auditableEvents` property to an empty `array`, you'll get duplicate `Audit` records this way.
