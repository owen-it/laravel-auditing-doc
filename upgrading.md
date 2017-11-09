# Upgrading
This document provides the necessary steps to upgrade from a previous version.

> {note} The `doctrine/dbal` package might be required.
> {tip} Any customisations made to the original migration should be taken into account. Do not blindly copy and paste!

## Upgrading from version 4.0.x to version 4.1.x
### Table
Version `4.1.0` brings back the `updated_at` column to the `audits` table.
Use the following migration to convert a default **4.0.x** table structure into the **4.1.x** version:

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
            // The user_agent column was introduced in version 4.0.4, so depending
            // on the 4.0.x version we're on, it might be needed to add it
            if (!Schema::hasColumn('audits', 'user_agent')) {
                $table->string('user_agent')->nullable();
            }

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

## Upgrading from version 3.1.x to version 4.1.x
### Model
All `Auditable` models must implement the `OwenIt\Auditing\Contracts\Auditable` interface.

> {tip} Take a look at the brief example in the [Introduction](introduction).

### Table
Use the following migration to convert a default **3.1.x** table structure into the **4.1.x** version:

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

## Upgrading from version 2.4.x to version 4.1.x
### Model
All `Auditable` models must implement the `OwenIt\Auditing\Contracts\Auditable` interface.

> {tip} Take a look at the brief example in the [Introduction](introduction).

### Table
Use the following migration to convert a default **2.4.x** table structure into the **4.1.x** version:

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

### Configuration

From version **4.0.0** onward, the configuration file has been renamed from `auditing.php` to `audit.php`.
The structure has also been updated, with options being **added**, **modified** and **removed**.

> {tip} When in doubt, check the bundled configuration file.

### Queues
Audit queue support has been removed in version **4.0.0**.

### Custom message and fields
Since version **4.0.0**, `Auditable` custom message and fields are no longer necessary.
Models should be updated to reflect this change, by removing the `$auditCustomMessage` and `$auditCustomFields` attributes.

> {tip} Documentation on how to display changes can be seen in the [Audit presentation](audit-presentation) section.
