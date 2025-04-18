# 3. Models
- Use **singular**, descriptive names for models.
- Use **accessors** for transforming data within the model.
- Do not use mutators in models - can be hard to understand for junior coders.
- Define relationships **explicitly**, such as `hasOne`, `morphToMany`, or `belongsTo`.
- Describe all relationships in one line comment above method.

```php
	// Relationship -- One to One (Polymorphic)
	public function citation(): MorphOne {
		return $this->morphOne(Text::class, 'model')->where('flag', 'citation');
	}
```

- In models use order of variables:
  1. protected `$table` - please always declare this property
  2. protected `$fillable` - please use safer fillable instead of `$guarded`
  3. protected `$casts`
  4. public boot method
- Use this order of methods in models:
  1. Relationships
  2. Accessors
  3. Custom methods
  4. Helpers
- In `$fillable` property and migrations use this order
  1. id
  2. other id's fields
  3. shorter name fields
  4. longer name fields
  5. date fields
  6. laravel timestamps
- Transform Eloquent attributes to Carbon object for date fields (by casting)

```php
// Good example: Eloquent model with relationships and accessors
use Illuminate\Database\Eloquent\Relations\HasMany;
use Illuminate\Database\Eloquent\Relations\MorphToMany;

class Company extends Model
{

	// Base table name
	protected $table = 'companies';

	// Mass assignment
	protected $fillable = [ 'type_id', 'category_id', 'owner_id', 'name', 'slug', 'public_at' ];

	// Return Carbon object
	protected $casts = [ 'public_at' => 'date' ];

	// Bootstrap any application services.
	public function boot(): void {
		Model::preventLazyLoading();
		parent::boot();
		static::saving(function ($model) {
			$model->slug = SlugService::createSlug($model);
		});
	}

	// Relationship -- One to Many
	public function employees(): HasMany {
		return $this->hasMany(Employ::class);
	}

	// Relationship -- Many to Many (Polymorphic)
	public function tags(): MorphToMany {
		return $this->morphToMany(Tag::class, 'model');
	}

	// Accessor -- fullName
	public function getFullNameAttribute() {
		return "{$this->name} ({$this->public_at->format('Y')})";
	}
}

```

- Avoid Using $with for Relationships that is not mandatory or needed,
  This can lead to Increased memory usage and Slower query performance.

```php
// Not ideal: Will always load 'posts' relationship, even when not needed.
protected $with = ['posts'];

// Better: Use explicit eager loading when needed.
$users = User::with('posts')->get();
```

- Use $with sparingly for relationships that are:
  - Essential in most cases (e.g., User with Profile).
  - Lightweight (e.g., small relationships that don't load large datasets).
  - Rarely Subject to Change (e.g., relationships that won't result in additional nested relationships later).

- Avoid Using **soft delete** on models
  - It using ```sql WHERE `deleted_at` IS NULL``` on every hooks. It should be performance issue on big project.
  - There are not automatically enabled in Query Builder e.g. ```php DB::table('tags')->get(); ```
  - It does't use cascading operations on a database level
  - It should have problem with unique constraints e.g. creating user with unique email that is soft deleted return a database `Duplicate entry error`.

- Use the static `query()` method to begin querying an Eloquent Model.
- Use Incrementing IDs as the internal primary key.


---
### What next:
4. See the [Migrations](https://git.greksak.sk/Michal/coding-standard/src/branch/main/docs/4_migrations.md) coding standards.
