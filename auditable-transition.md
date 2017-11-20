# Auditable Transition
Another feature introduced in version **5.0.0** is the ability for an `Auditable` model to transition to another state using an `Audit` record.

The idea is to use the `Audit` trail of a model, in order to undo or redo changes to its state.

## Transitioning
The `Auditable` interface defines a `transitionTo()` method, which does exactly what it says.
Two arguments are accepted (the first being an `Audit` and the second being a `bool`), although only the first one is mandatory.

Using an Article model example, here's how it works:
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

// See the attributes that changed during the state transition using the new values
dump($dirtyLatestArticleFromNew->getDirty());

// See the attributes that changed during the state transition using the old values
dump($dirtyLatestArticleFromOld->getDirty());

// ...
```

> {note} The `transitionTo()` method updates model attributes without persisting to the database. The developer is responsible for checking that the new state is correct and calling `save()` on the model.
