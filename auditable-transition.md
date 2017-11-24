# Auditable Transition
Another feature introduced in version **5.0.0** is the ability use an `Audit` record to transition between states of an `Auditable` model.

## Transition Rules
An `Auditable` model can **only** transition through an `Audit` with the same `auditable_id` and `auditable_type`, otherwise, an `AuditableTransitionException` will be thrown.

Besides a matching `auditable_id` and `auditable_type`, attribute compatibility must be met between the `Audit`'s `old_values` / `new_values` and the `Auditable` class.

An `AuditableTransitionException` will be thrown if an attribute incompatibility is detected.

Incompatible property names can be retrieved using the `getIncompatibilities()` method.

Using a Article model example, here's how it works:
```php
<?php

// ...

try {
    $article = Article::find(123);

    // An old Audit from when the Article
    // model had different property names
    $audit = $article->audits()->first();

    $article->transitionTo($audit);
    
} catch (AuditableTransitionException $e) {
    echo sprintf('Incompatible attributes: %s', implode(', ', $e->getIncompatibilities()));
}

// ...
```

## Transitioning
The `Auditable` interface defines a `transitionTo()` method, which is in charge of validating and transitioning the model state.

The method takes two arguments, an `Audit` which will be used as the history to go back to, and a `bool` (optional), which will define if the **old** or the **new** data from the `Audit` should be used. 

> {tip} By default, the **new** data will be used.

Still using the same Article model, here's how it works:
```php
<?php

// ...

// Get the latest Article
$latestArticle = Article::orderBy('created_at', 'desc')->first();

// Get the first Audit from the latest Article
$firstAudit = $latestArticle->audits()->first();

// Update the Article properties with the corresponding ones in the new_values Audit attribute
$dirtyLatestArticleFromNew = $latestArticle->transitionTo($firstAudit);

// Update the Article properties with the corresponding ones in the old_values Audit attribute
$dirtyLatestArticleFromOld = $latestArticle->transitionTo($firstAudit, true);

// See the updated attributes during the state transition using the new values
dump($dirtyLatestArticleFromNew->getDirty());

// See the updated attributes during the state transition using the old values
dump($dirtyLatestArticleFromOld->getDirty());

// ...
```

> {note} The `transitionTo()` method updates model attributes without persisting to the database. It's the developer's responsibility to check that the new state is correct and calling `save()` on the model.
