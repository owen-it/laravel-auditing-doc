# Upgrading
This document provides the necessary steps to upgrade from a previous version to **8.0.x**.

## PHP
Support for PHP **5.x** has been dropped since version **5.0.0**.
PHP **7.0.13** or greater is now required.

## Before upgrading
The `doctrine/dbal` package is required to successfully execute the upgrade migrations, so make sure it's installed.

```sh
composer require doctrine/dbal
```

> {note} Any custom changes made to the initial [Audit Migration](audit-migration) should be taken into account, do not blindly copy and paste!

## Upgrade from version 12.x to 13
Version 13 introduces a couple of changes to config and resolvers.

### Resolvers
If you have made custom resolver implementations of _UrlResolver_, _UserAgentResolver_ or _IpAddressResolver_, they now extend the Resolver contract 

```php
class UrlResolver implements \OwenIt\Auditing\Contracts\Resolver
```
Except for the user resolver, resolvers are now dynamic and can be added or removed by updating the config file.

Previously there was a config key "resolver", which is now gone and replaced by "resolvers".
```php
'resolvers' => [
    'ip_address' => OwenIt\Auditing\Resolvers\IpAddressResolver::class,
    'user_agent' => OwenIt\Auditing\Resolvers\UserAgentResolver::class,
    'url'        => OwenIt\Auditing\Resolvers\UrlResolver::class,
],
```

The user resolver has moved to the user config key
```php
'user'      => [
    'morph_prefix' => 'user',
    'guards'       => [
        'web',
        'api',
    ],
    'resolver' => OwenIt\Auditing\Resolvers\UserResolver::class
], 
```

### Empty values config
Now you can choose to omit the writing of empty audits. You need to add the following section to to config/audit.php file:

```php
/*
|--------------------------------------------------------------------------
| Empty Values
|--------------------------------------------------------------------------
|
| Should Audit records be stored when the recorded old_values & new_values
| are both empty?
|
| Some events may be empty on purpose. Use allowed_empty_values to exclude
| those from the empty values check. For example when auditing
| model retrieved events which will never have new and old values
|
*/

'empty_values'         => true,
'allowed_empty_values' => [
    'retrieved'
], 
```

## Upgrade from version 7.0.x to version 8.0.x
Version **8.0.x** breaks `AttributeRedactor` usage, slightly.
If you were previously using it, make sure to read the [Attribute Modifiers](attribute-modifiers) section and update your models, accordingly.

The `audits` table structure remains the same, so no changes in that regard are needed.

> {tip} The `AttributeRedactor` interface was previously called `AuditRedactor`.

### Configuration
Overall, the configuration remains the same, with just the `audit.redact` entry being dropped.

## Upgrade from version 5.1.x/6.1.x to version 8.0.x
### Table
Use the following migration to convert a default **5.1.x**/**6.1.x** table structure into the **8.0.x** version:

```php
<?php
use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\DB;
use Illuminate\Support\Facades\Schema;

class UpdateAuditsTable extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::table('audits', function (Blueprint $table) {
            $table->string('user_type')->nullable();
        });
        
        // Set the user_type value and keep the timestamp values.
        DB::table('audits')->update([
            'user_type'  => \App\User::class,
            'created_at' => DB::raw('created_at'),
            'updated_at' => DB::raw('updated_at'),
        ]);
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        // There's no turning back
    }
}
```

> {note} If necessary, replace `\App\User::class` with the FQCN of the User model, before setting the `user_type`.

## Upgrade from version 4.1.x to version 8.0.x
### Table
Use the following migration to convert a default **4.1.x** table structure into the **8.0.x** version:

```php
<?php
use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\DB;
use Illuminate\Support\Facades\Schema;

class UpdateAuditsTable extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::table('audits', function (Blueprint $table) {
            $table->string('user_type')->nullable();
            $table->string('tags')->nullable();
        });
        
        // Set the user_type value and keep the timestamp values.
        DB::table('audits')->update([
            'user_type'  => \App\User::class,
            'created_at' => DB::raw('created_at'),
            'updated_at' => DB::raw('updated_at'),
        ]);
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        // There's no turning back
    }
}
```

> {note} If necessary, replace `\App\User::class` with the FQCN of the User model, before setting the `user_type`.

## Upgrade from version 3.1.x to version 8.0.x
### Model
All `Auditable` models must implement the `OwenIt\Auditing\Contracts\Auditable` interface.

> {tip} Take a look at the brief example in the [Model Setup](model-setup).

### Table
Use the following migration to convert a default **3.1.x** table structure into the **8.0.x** version:

```php
<?php
use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\DB;
use Illuminate\Support\Facades\Schema;

class UpdateAuditsTable extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::table('audits', function (Blueprint $table) {
            $table->increments('id')->change();
            $table->renameColumn('type', 'event');
            $table->renameColumn('old', 'old_values');
            $table->renameColumn('new', 'new_values');
            $table->unsignedInteger('user_id')->nullable()->change();
            $table->string('user_type')->nullable();
            $table->renameColumn('route', 'url');
            $table->string('user_agent')->nullable();
            $table->string('tags')->nullable();
            $table->timestamp('updated_at')->nullable();
        });

        // Set the user_type value and keep the timestamp values.
        DB::table('audits')->update([
            'user_type'  => \App\User::class,
            'created_at' => DB::raw('created_at'),
            'updated_at' => DB::raw('updated_at'),
        ]);
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        // There's no turning back
    }
}
```

> {note} If necessary, replace `\App\User::class` with the FQCN of the User model, before setting the `user_type`.

## Upgrade from version 2.4.x to version 8.0.x
### Model
All `Auditable` models must implement the `OwenIt\Auditing\Contracts\Auditable` interface.

> {tip} Take a look at the brief example in the [Model Setup](model-setup).

### Table
Use the following migration to convert a default **2.4.x** table structure into the **8.0.x** version:

```php
<?php
use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\DB;
use Illuminate\Support\Facades\Schema;

class UpdateAuditsTable extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::table('audits', function (Blueprint $table) {
            $table->unsignedInteger('user_id')->nullable()->change();
            $table->string('user_type')->nullable();
            $table->unsignedInteger('owner_id')->change();
            $table->renameColumn('owner_id', 'auditable_id');
            $table->renameColumn('owner_type', 'auditable_type');
            $table->index([
                'auditable_id',
                'auditable_type',
            ]);
            $table->renameColumn('type', 'event');
            $table->renameColumn('old_value', 'old_values');
            $table->renameColumn('new_value', 'new_values');
            $table->renameColumn('route', 'url');
            $table->renameColumn('ip', 'ip_address');
            $table->string('user_agent')->nullable();
            $table->string('tags')->nullable();
        });
        
        // Set the user_type value and keep the timestamp values.
        DB::table('audits')->update([
            'user_type'  => \App\User::class,
            'created_at' => DB::raw('created_at'),
            'updated_at' => DB::raw('updated_at'),
        ]);        
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        // There's no turning back
    }
}
```

> {note} If necessary, replace `\App\User::class` with the FQCN of the User model, before setting the `user_type`.

## Update the configuration
The `audit.redact` entry which was present since version **6.1.0** has been dropped.

> {tip} When in doubt, check the bundled configuration file and update or simply replace your current file and make the appropriate changes.
