# 2. Controllers
- Keep Controller Names Singular
  - Name controllers after the resource they control, in singular form.
  - Examples: `UserController()`, `PostController()`

- **Separate API** and Web Controllers if application has API
  - Use separate controllers for APIs and web routes to maintain clear separation of concerns.
  - Example:
  ```php namespace App\Http\Controllers\Web;``` - UserController (for web)
  ```php namespace App\Http\Controllers\Api;``` - UserController (for API)

- Follow the **Single Responsibility Principle** (SRP) by keeping controllers focused on handling requests and responses.
  - Controllers should only handle HTTP requests and responses.
  - Avoid putting business logic, database queries, or other responsibilities directly in the controller. Delegate these tasks to services or model methods.

```php
// Bad Example:
public function index() {
	$users = DB::table('users')->where('active', true)->get();
	return view('users.index', compact('users'));
}

// Good Example:
public function index(UserService $userService): View|Response {
	$users = $userService->getActiveUsers();
	return view('users.index', compact('users'));
}
```

- Delegate heavy business logic to **services** and reuse code from service classes.
- **Do not use** repository, action or observer classes - can be hard to understand for junior coders.
- Declare the method **Return classes** by colon after method declaration.
- Ensure web controllers return views, responses or redirect.
- API controllers have to return only JSON responses (response()->json()).

```php
// Simple controller
class SimpleController extends Controller
{
	use Illuminate\Http\RedirectResponse;
	use Illuminate\Http\Response;
	use Illuminate\View\View;

	public function functionName(): View|Response|RedirectResponse {
		...

		// Good example of return view
		return view('users.index', compact('users'));

		// Good example of return response
		return response()
			->view('article.show', ['model'=>$article])
			->header('last-modified', Carbon::now()->format('r'));

		// Good example of return redirect
		return redirect()->route('activity.show', $activity->slug);
	}
}
```

- If you need to display a set of database records, always use [pagination](https://laravel.com/docs/11.x/pagination).

- Use Resource Controllers
  - Use resource controllers for standard CRUD operations to keep your routes and methods consistent and concise.

```php
Route::resource('posts', PostController::class);
```

  - Resource controller methods:
    index():   Display a list of resources
    show():    Display a single resource
    store():   Save a new resource
    update():  Update an existing resource
    destroy(): Delete a resource

- Keep Methods Focused and Short
  - Each controller method should handle a single responsibility or action.

```php
// Bad Example:
public function manage(Request $request) {
    if ($request->isMethod('post')) {
        $this->createPost($request);
    } else if ($request->isMethod('put')) {
        $this->updatePost($request);
    } else {
        $this->deletePost($request);
    }
}

// Good Example:
// Separate the logic into different methods like store(), update(), etc.
```

- Follow RESTful Principles
  - Design your controllers to adhere to REST principles:
  - Use HTTP verbs (GET, POST, PUT, DELETE) correctly.
  - Use meaningful URIs like /users or /user/{slug}.

- Use **Dependency Injection**
  - Inject services, or helpers into your controller methods or constructors instead of creating them manually.

```php
// Bad Example:
public function __construct() {
	$this->userService = new UserService();
}

// Good Example:
public function __construct(UserService $userService) {
	$this->userService = $userService;
}
```

- Leverage Middleware
  - Use middleware for tasks like authentication, authorization, or logging, instead of duplicating this logic across controller methods.

```php
public function __construct() {
	$this->middleware('auth');
	$this->middleware('can:update,post')->only(['edit', 'update']);
}
```

- Return JSON for APIs
  - For API controllers, return JSON responses with appropriate status codes.
```php
public function store(PostRequest $request) {
	return response()->json(['data' => $data], 201); // 201 - Created
}
```


---
### What next:
3. See the [Models](https://github.com/bohumer/coding-standard/blob/main/coding-standard/docs/3_models.md) coding standards.
