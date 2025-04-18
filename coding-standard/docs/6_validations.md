# 6. Validation
- Use Form Request Validation
  - Use form request classes for input validation instead of writing validation logic in the controller.
  - It keeps the controller clean and maintains separation of concerns by moving validation logic to a dedicated class.
  - Validate input data using custom request classes derived from `FormRequest`.
  - Use descriptive validation rules and messages to ensure clarity.
  - In request classes use custom validation messages. 
  - Use custom translation helper function `t()` to have messages editable.
  - Always use `rule_` prefix in translation string to better readibility.

```php
namespace App\Http\Requests\Entity;

use Illuminate\Foundation\Http\FormRequest;
use Illuminate\Validation\Rules;

class ActivityRequest extends FormRequest
{
	// Get array of validation rules
	public function rules(): array {
		return [
			'title' => [
				'required',
				'string',
				'max:255',
				'regex:/^(?!http)/',
			],
		];
	}

	// Custom validation error messages
	public function messages(): array {
		return [
			'title.required' => t('rule_title_required'),
			'title.string' => t('rule_title_string'),
			'title.regex' => t('rule_title_regex'),
			'title.max' => t('rule_title_max'),
		];
	}
}

// In controller:
public function store(ActivityRequest $request) {
	Activity::create($request->validated());
}
```

- Use Built-in Validation Rules
  - Always prefer Laravel's built-in validation rules whenever possible.
  - They are well-tested and cover most common use cases like required, email, unique, exists, etc.

- Use custom Validation rules if needed
  - Use the withValidator method for one-off custom logic in a specific FormRequest.
  - Or Use Validator::extend for global custom rules.

```php
namespace App\Http\Requests;

use Illuminate\Foundation\Http\FormRequest;
use Illuminate\Support\Facades\Validator;

class CustomFormRequest extends FormRequest
{
	...
	public function withValidator(Validator $validator) {
		$validator->after(function ($validator) {
			if ($this->hasInvalidCustomLogic()) {
				$validator->errors()->add('custom_field', t('rule_custom_field_message'));
			}
		});
	}

	private function hasInvalidCustomLogic() {
		// Add your custom validation logic here
		return $this->input('custom_field') === 'invalid';
	}
}
```

- Use the `Validator::extend()` Method for global custom rules.
  - Extend the Validator facade in `App\Providers\AppServiceProvider:`


```php
use Illuminate\Support\Facades\Validator;

public function boot()
{
	Validator::extend('custom_rule', function ($attribute, $value, $parameters, $validator) {
		return $value !== 'invalid';
	});
}

// Use the Rule in the FormRequest:
public function rules() {
	return [
		'custom_field' => 'required|custom_rule',
	];
}
```

- Avoid Inline Validation in Controllers. Validation logic should not clutter your controller methods.
- Always ensure custom validation logic is tested and appropriately documented.
- Use Conditional Validation Rules. Use `sometimes` or `required_if`, `required_unless` etc., for conditional validation.
- Validate Arrays and Nested Fields. Use .* syntax for validating arrays.

```php
	'items.*.name' => 'required|string',
	'items.*.price' => 'required|numeric|min:0',
```

- Validate Relational Data.
  - Use exists and unique rules to validate data against database records.

```php
	'category_id' => 'required|exists:categories,id',
	'email' => 'required|email|unique:users,email',
```

- Prevent Mass Assignment Vulnerabilities
  - Only use validated data to create or update models.

```php
// Bad Example:
User::create($request->all());

// Good Example:
User::create($request->validated());
```


---
### What next:
7. See the [Testing](https://git.greksak.sk/Michal/coding-standard/src/branch/main/docs/7_testing.md) coding standards.
