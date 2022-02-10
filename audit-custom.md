# Custom audits
Since v13 you may audit actions that does not origin from eloquents 'created', 'updated', 'deleted', 'restored' and 'retrieved' events.

## Example: Related attributes

Lets say you have articles and categories

```php
class Article extends Model implements \OwenIt\Auditing\Contracts\Auditable
{
    use Auditable;

    public function categories()
    {
        return $this->morphToMany(Category::class, 'model', 'model_has_categories');
    }

```
Migrations for this example looks like this:
```php 
Schema::create('categories', function (Blueprint $table) {
    $table->increments('id');
    $table->string('name');
    $table->timestamps();
});

Schema::create('model_has_categories', function (Blueprint $table) {
    $table->string('model_type');
    $table->unsignedBigInteger('category_id');
    $table->unsignedBigInteger('model_id');
    $table->timestamps();
});
```

Many things can be categorized, so we are using a morphToMany relation so that we can use `$article->sync($category)`, `$article->attach($category)` or `$article->detach($category)`. The common problem with this is, that no change is made on the Auditable (Article) or on the Category side, and thus we have no direct way to audit the assigned categories.

In version 13 of laravel-auditing, you can use Auditables attach, detach and sync pass-through methods like this: `$article->auditAttach('categories', $category);`. It works the same way as the [Eloquent attach](https://laravel.com/docs/master/eloquent-relationships#attaching-detaching), but it registers a custom event and writes an audit of the changes as if it was an array property of article.

First argument to the auditAttach, auditDetach and auditSync methods is the name of the relationship. In the example above, that is "categories". 

The audited value is the array version of the relationship return! 

## Example: Custom log event

You may custom-log any values you wish, as long as they are on an Auditable model. It works by dispatching the `\OwenIt\Auditing\Events\AuditCustom` event with the Auditable as argument when you have defined what to log

```php 
$article = Article::find(1);
$article->auditEvent = 'whateverYouWant';
$article->isCustomEvent = true;
$article->auditCustomOld = [
    'customExample' => 'Anakin Skywalker'
];
$article->auditCustomNew = [
    'customExample' => 'Darth Vader'
];
Event::dispatch(AuditCustom::class, [$article]);
```

