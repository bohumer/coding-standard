# 4. Migrations
- Use `snake_case` for table names and column names.
- Use shorter `006_` number prefixes instead of long laravels default `2024_11_08_100000_` for table names.
- Use Descriptive and Meaningful File Names, such as `create_users_table`.
- Avoid implementing business logic inside migrations. Migrations are for schema management, not data manipulation.
- Avoid combining multiple unrelated changes in a single migration file.
- You can combine mode **related** tables in one migration file

```php
use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{

	// Run the migrations.
	public function up(): void
	{
		Schema::create('users', function (Blueprint $table) {
			$table->id();
			$table->string('name');
			$table->string('email')->unique();
			$table->timestamp('email_verified_at')->nullable();
			$table->string('password');
			$table->timestamps();
		});

		Schema::create('user_tokens', function (Blueprint $table) {
			$table->string('email')->primary();
			$table->string('type')->default('password_reset');
			$table->string('token');
			$table->timestamp('created_at')->nullable();
		});
	}

	// Reverse the migrations.
	public function down(): void
	{
		Schema::dropIfExists('users');
		Schema::dropIfExists('user_tokens');
	}
};
```

- For morph tables use `model` instead of `abble_prefix`

```php
	Schema::create('comments', function (Blueprint $table) {
		$table->id();
		$table->unsignedBigInteger('model_id'); // ID of the related model
		$table->string('model_type'); // Model class name (Post, Video, etc.)
		$table->text('comment');      // The actual comment
		$table->timestamps();
	});
```

  - then use it in models and controllers like:

```php
// Relationship -- Many to Many (Polymorphic)
public function comments(): MorphToMany {
    return $this->morphMany(comment::class, 'model');
}

// Add a comment to the Post
$post->comments()->create(['comment' => 'Great post!']);
```

- Use the built-in methods (`string`, `integer`, `foreignId` etc.) instead of raw SQL queries for better readability and database portability.
- Try to avoid lot and messy table structure, try to minimize database at all
- Avoid to use bigInteger if is clearly that table will never has a tons of rows like `categories` or `tags`
- Use `unsignedTinyInteger` and `tinyText` if it is possible instead of `integer` or `string`.
- Focus of using mariaDb as db service
- Specify default values for columns when applicable to avoid `NULL` issues

```php
$table->boolean('is_active')->default(true);
$table->string('status')->default('pending');
```

- Use foreign key constraints to enforce relationships between tables. Use onDelete() and onUpdate() for cascading behavior.

```php
$table->foreignId('user_id')->constrained()->onDelete('cascade');
```

- Add indexes to frequently queried columns for performance improvements. Use unique() for unique constraints.

```php
$table->string('email')->unique();
$table->index(['first_name', 'last_name']);
```

- Specify nullable() for fields that can have missing values.

```php
$table->string('middle_name')->nullable();
```

- For longer fields always use separate model `Text()` and define morph relationship.

```php
Schema::create('texts', function (Blueprint $table) {
    $table->id(); // Auto-incrementing unsigned integer primary key
    $table->unsignedInteger('model_id');   // Equivalent to UNSIGNED INT(10)
    $table->tinyText('model_type');        // Equivalent to TINYTEXT
    $table->tinyText('flag')->nullable();  // TINYTEXT with NULL default
    $table->text('value')->nullable();     // TEXT with utf8mb4 collation
});

// In model:
// Relationship -- One to One (Polymorphic)
public function body(): MorphOne {
	return $this->morphOne(Text::class, 'model')->where('flag', 'body');
}
```

- Prefer timestamps over booleans. For example, `published_at` instead of `is_published`.
- Always use meaningfull `_at` suffix for date fields.


---
### What next:
5. See the [Views](https://git.greksak.sk/Michal/coding-standard/src/branch/main/docs/5_views.md) coding standards.
