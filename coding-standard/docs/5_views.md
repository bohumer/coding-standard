# 5. Views
- Keep View Names meaningfully
  - Name views after the resource they viewing.
  - Organize views in folders based on features or modules to maintain clarity and structure.
  - Examples: `user\dashboard.blade.php`, `user\edit.blade.php` ...etc.

- Keep Views Simple
  - Avoid raw PHP logic in Blade files. Instead, use helper functions or view composers for complex logic.
  - Avoid putting complex logic or database queries in your Blade files. Use controllers or helpers to handle data preparation.
  - Bad Example:

```php
@php
	$users = DB::table('users')->where('active', true)->get();
@endphp
<ul>
	@foreach ($users as $user)
		<li>{{ $user->name }}</li>
	@endforeach
</ul>
```

  - Good Example:

```php
// In Controller
$users = User::where('active', true)->get();
return view('users.index', compact('users'));

// In Blade
<ul>
	@foreach ($users as $user)
		<li>{{ $user->name }}</li>
	@endforeach
</ul>
```

- Follow the DRY Principle. Avoid repeating code by using components or includes.
- Use Blade Components
  - For reusable parts of your views (e.g., buttons, modals, forms), use Blade components.

```html
<form id="company_form" class="entity-company-form sm:max-w-2xl mx-auto mt-5" method="POST" action="{{ route('company.update', $model) }}">
	@method( $model->name ? 'PATCH' : 'PUT' )
	@csrf
	<x-form.text name="name" :label="t('Company_name')" :value="old('name', $model->name ?? '')" :required="true" />
```

- Leverage Layouts and Sections
  - Use `@extends()` and `@section()` to avoid duplication and create a consistent structure across pages.
  - Avoid Repetition with Includes. Use `@include()` for reusable pieces like headers, footers, and partials.
  - Layout file: layouts/app.blade.php
```html
<html>
<head>
	<title>@yield('title')</title>
</head>
<body>
	<x-svg.logo_main' size="medium" />
	@include('layout.header')
	<div class="content">
		@yield('content')
	</div>
```

  - and Child View: 

```php
@extends('layouts.app')
@section('title', t('title_home_page'))
@section('content')
	<h1>Welcome to the Home Page</h1>
@endsection
```

- Use `@yield()` and `@stack()` for Flexibility
  - Use `@yield()` for placeholders that can be used only once per page
  - Use `@stack()` for sections that can have multiple entries (e.g., scripts).
  - In Layout blade file:

```php
@yield('content')
@stack('scripts')
```
  - and Child View: 

```php
@push('scripts')
	<script src="/js/specific.js"></script>
@endpush
```

- Sanitize Output
  - Use blade `{{-- --}}` syntax for comments instead of html `<!-- -->`. This will exclude from final html.
  - Use the `{{ }}` syntax to escape content and prevent XSS attacks.
  - Use `{!! !!}` only for trusted content. Never use this for 'user entry' data.

```html
{{-- User section --}}
<p>{{ $user->name }}</p>  <!-- Escaped -->
<p>{!! $user->bio !!}</p> <!-- Trusted -->
```

- Optimize Loops
  - Avoid heavy processing inside Blade loops; prepare data in the controller.
  - Bad Example:

```php
@foreach ($users as $user)
    <p>{{ $user->posts()->count() }}</p>
@endforeach
```

  - Good Example:

```php
// In Controller
$users = User::withCount('posts')->get();

// In view
@foreach ($users as $user)
	<p>{{ $user->posts_count }}</p>
@endforeach
```

- Use Conditional Classes
  - Use Blade's conditional class syntax for dynamic styling.
  - Also is good to have in every `<body>` tag base information depend on page type e.g., 'page', 'user', 'uid-864' and so on..
  - Example:

```html
<body class="@yield('body_class') bg-white text-gray-800 dark:bg-gray-800 dark:text-gray-200">
	...
	<li class="{{ $user->isActive() ? 'active' : '' }}">
		{{ $user->name }}
	</li>
```

- Use named `route()` helper in Blade:
  - Always use route names or helpers instead of hardcoding URLs.
  - Avoid Hardcoding URLs except of outgoing urls. 
  - Use `target="_blank"` for urls out of project.
  - Bad Example:

```html
<a href="/login">Login</a>
```

  - Good Example:

```html
<a href="http://example.com" target="_blank">Outgoing</a>
<a href="{{ route('login') }}">Login</a>
```

- Use minimized inline svg icons and other vector graphics.

```html
	<x-svg.close class="h-8" alt="Close button" />
```

```xml
{{-- Close button --}}
<svg viewBox="0 0 14 14" fill="none" aria-hidden="true" xmlns="http://www.w3.org/2000/svg" {{
	 $attributes->merge(['class' => "
		inline
		transition
		duration-75
		flex-shrink-0
		rtl:rotate-180
	"]) }}>
	<path stroke="currentColor" stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M1 0.999939L13 13" />
	<path stroke="currentColor" stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M1 13L13 0.99996" />
</svg>
```


- Use Localization for Text
  - Do Not use `@lang` or `trans()` for translating text. Internal Laravel translations are in `resources/lang/en/\*\*\*.php`
  - Use custom translation helper `t()` instead. We use databaze editable translations instead of laravel hardcoded resources.

- Debugging with `@dd()` or `@dump()`
  - Use @dd or @dump to debug Blade templates.

```php
@dd($users)
```


---
### What next:
6. See the [Validations](https://github.com/bohumer/coding-standard/blob/main/coding-standard/docs/6_validations.md) coding standards.
