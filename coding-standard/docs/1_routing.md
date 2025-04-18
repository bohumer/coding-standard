# 1. Routing
- **Route Definitions**:
  - Define routes in `routes/web.php` for web and `routes/api.php` for API.
  - Keep the `routes/web.php` File Clean
  - Limit web.php file only to high-level route definitions.
  - Delegate logic to controllers, middleware, or services.
  - Always name your routes using the name() method.
  - Use meaningful route names (e.g., `user.profile` instead of `profile`).

```php
// Good example: Defining a single route
Route::get('/author/show', [UserController::class, 'show'])->name('author.show');

// Bad example
Route::get('/author/profile', [UserController::class, 'show'])->name('author-profile');
```
- Use **RESTful Resource** Controllers for CRUD operations:

```php
// Good example: Resource routes for CRUD
Route::resource('user', UserController::class)->except(['show']);
```

  This automatically creates routes like:

	GET /user → index
	POST /user/ → store
	GET /user/{slug} → edit
	POST /user/{slug} → update
	And more...

  - If you need to exclude some methods:

```php
Route::resource('post', PostController::class)->except(['create', 'edit']);
```

- **Group Related** Routes
  - Use route groups to logically organize and group related routes.
  - Use route groups for middleware, prefixes, or namespaces.

```php
// Good example: Grouped routes
Route::group(['prefix'=>'admin', 'as'=>'admin.'], function() {
	Route::controller(AdminController::class)->group(function() {
		Route::get(...)
	});
});
```

  - Avoid repeating similar logic in multiple routes. Instead, use route groups or reusable methods.

```php
// Bad Example:
Route::get('/admin/users', [AdminController::class, 'users'])->middleware('admin');
Route::get('/admin/posts', [AdminController::class, 'posts'])->middleware('admin');

// Good Example:
Route::prefix('admin')->middleware('admin')->group(function () {
	Route::get('/users', [AdminController::class, 'users']);
	Route::get('/posts', [AdminController::class, 'posts']);
});
```

- Use **Route Parameters** Effectively
  - Define route parameters with clear names and constraints.
  - Use {} to define required parameters and add constraints via where.

```php
Route::get('/comment/{id}', [CommentController::class, 'show'])->where('id', '[0-9]+')->name('comment.show');
```

- Use optional parameters when needed:

```php
Route::get('/profile/{user?}', [ProfileController::class, 'show'])->name('profile.show');
```


---
### What next:
2. See the [Controllers](https://github.com/bohumer/coding-standard/blob/main/coding-standard/docs/2_controllers.md) coding standards.
