# Upgrading
This document provides the necessary steps to upgrade from a previous version.

## PHP
Support for PHP **5.5**/**5.6** has been dropped. Version **5.0.0** of the package requires PHP **7.0.0** or greater.

## Before upgrading
The `doctrine/dbal` package is required to successfully execute the upgrade migrations, so make sure it is available.

```sh
composer require doctrine/dbal
```

> {note} Any changes made to the initial [Audit Migration](audit-migration) should be taken into account, do not blindly copy and paste!

## Upgrade from version 4.1.x to version 5.0.x
### Table
Version **5.0.0** adds a `tags` column to the `audits` table.
Use the following migration to convert a default **4.1.x** table structure into the **5.0.x** version:

```php
<?php
use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
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
            $table->string('tags')->nullable();
        });
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

## Upgrade from version 3.1.x to version 5.0.x
### Model
All `Auditable` models must implement the `OwenIt\Auditing\Contracts\Auditable` interface.

> {tip} Take a look at the brief example in the [Introduction](introduction).

### Table
Use the following migration to convert a default **3.1.x** table structure into the **5.0.x** version:

```php
<?php
use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
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
            $table->renameColumn('route', 'url');
            $table->string('user_agent')->nullable();
            $table->string('tags')->nullable();
            $table->timestamp('updated_at')->nullable();
        });

        // Set the updated_at value and keep the current created_at value.
        DB::table('audits')->whereNull('updated_at')->update([
            'created_at' => DB::raw('created_at'),
            'updated_at' => DB::raw('created_at'),
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

## Upgrade from version 2.4.x to version 5.0.x
### Model
All `Auditable` models must implement the `OwenIt\Auditing\Contracts\Auditable` interface.

> {tip} Take a look at the brief example in the [Introduction](introduction).

### Table
Use the following migration to convert a default **2.4.x** table structure into the **5.0.x** version:

```php
<?php
use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
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

## Update the configuration
In version **5.0.0**, more options were added to the default configuration and the `default` key was renamed to `driver`.

> {tip} When in doubt, check the bundled configuration file and update or simply replace your current file and make the appropriate changes.

### Queues
Audit queue support has been removed since version **4.0.0**.

### Custom message and fields
Since version **4.0.0**, `Auditable` custom message and fields are no longer supported.
Models should be updated to reflect this change, by removing the `$auditCustomMessage` and `$auditCustomFields` attributes.

> {tip} Documentation on how to display changes can be seen in the [Audit presentation](audit-presentation) section.

## User ID resolver
From version **5.0.0** onwards, using a `Closure` or any sort of `callable` in the configuration for resolving a `User` is no longer supported.
Instead, the `FQCN` of a class implementing the `OwenIt\Auditing\Contracts\UserResolver` interface should be used.

> {tip} Read more about it in the [General Configuration](general-configuration) section.
