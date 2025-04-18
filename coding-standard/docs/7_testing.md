# 7. Testing
- Write Tests on Feature Layer only
  - Unit Tests are focus on testing individual classes, methods, or functions in isolation. We don't need it as we test application in whole.
  - Test complete flows like routes, controllers, views and middleware to ensure the application works as expected.
  - Write Feature tests for end-to-end validation of application workflows.

- Use Descriptive Test Method Names
  - Use clear and descriptive method names to convey the purpose of the test.

```php
public function test_user_can_login_with_valid_credentials() {
    // Test logic
}
```

- Organize Tests in Logical Directories
  - Organize tests to match the structure of your application.
  - Try to focus on models level structure
    e.g., `tests/Feature/UserTest.php`, `tests/Feature/PostTest.php` ...etc.

- Leverage Laravel's Testing Features
  - Use built-in Laravel testing helpers to reduce boilerplate code.

```php
$this->post('/login', [
	'email' => 'test@example.com',
	'password' => 'password',
])->assertStatus(200);
```

- Avoid Hardcoding Data
  - Use model factories to generate test data efficiently.
  - Use factories, seeders, or dynamic data generation instead of hardcoding values.

```php
$user = User::factory()->create();
$this->actingAs($user)->get('/dashboard')->assertStatus(200);

// Bad Example:
$this->post('/login', [
    'email' => 'fixed@example.com',
    'password' => 'password',
])->assertStatus(200);

// Good Example:
$user = User::factory()->create();
$this->post('/login', [
    'email' => $user->email,
    'password' => 'password',
])->assertStatus(200);
```

- Mock External Dependencies
  - Avoid testing third-party APIs or services in your tests. Use mocks to simulate external dependencies.
  - Ensure that all requests using the HTTP Client [have been faked](https://laravel.com/docs/11.x/http-client#preventing-stray-requests).
  - Mock also queued processes to ensure they’re dispatched correctly without executing them.

```php
Http::fake([
	'api.example.com/*' => Http::response(['data' => 'mocked'], 200),
]);
$response = Http::get('https://api.example.com/data');
$response->assertOk();
```

- Write tests for every controllers methods.
  - Test Validation Logic. Test how your application handles validation errors.
  - Test Both Positive and Negative Cases. Write tests for valid and invalid scenarios.

```php
// Positive Tests:
$this->post('/register', [
	'email' => '',
])->assertSessionHasErrors('email');
$this->post('/login', [
    'email' => 'test@example.com',
    'password' => 'correct-password',
])->assertRedirect('/dashboard');

// Negative Test:
$this->post('/login', [
    'email' => 'test@example.com',
    'password' => 'wrong-password',
])->assertSessionHasErrors('email');
```

- Clean Up the Database Between Tests
  - Use the RefreshDatabase trait to ensure your database is clean for every test.

```php
use Illuminate\Foundation\Testing\RefreshDatabase;

class UserTest extends TestCase
{
	use RefreshDatabase;

	public function test_user_creation() {
		$user = User::factory()->create();
		$this->assertDatabaseHas('users', ['email' => $user->email]);
	}
}
```

- Run tests always after significant changes to ensure the application remains stable.
- Write a test to replicate a reported bug before fixing it to ensure the issue is resolved and doesn't reoccur.


---
