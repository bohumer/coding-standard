# Coding policy

A comprehensive Coding Standard to help ensure consistency, readability, and maintainability in Laravel projects. These standards follow widely accepted practices with Laravel's conventions. It also help new junior coders to learn projects faster because standards not following *hard to understand* Laravel's principles.

The result of following these standards has two minor advantages:
- Projects is more stable
  - easy to updating/upgrading, less dependend on third party packages
- Projects is more flexible
  - easy to understand to junior coders, focused on base Laravel's convenions, instead of widely accepted practices



# Laravel Coding Standards


## General Guidelines
- **Naming Conventions**:
  - Classes, controllers, and models: `PascalCase`.
  - Methods and variables: `camelCase`.
  - Database tables: `snake_case` (plural).
  - Migration files: `snake_case` (descriptive action names).
  - Constants: `UPPER_SNAKE_CASE`.
  - Make method names more specific e.g. prefer `calculateTax()` instead of `calc()`
  - Keep names Short but Clear e.g. `resetPassword()` instead of `resetUserPasswordAndSendNotification()`
- **Indentation**: Use tabs for indentation (no spaces).
- **Line Length**: Try to keep lines under 180 characters.
- **Comments**: Use clear comments in code. Document functions, complex logic, and non-obvious decisions.
- **Avoid Hardcoding URLs**. Use `route()` helpers or configuration values for URLs.

## Folder and File Structure
- Follow Laravel's default directory structure. Group similar files logically.
- **Controllers**: Place all controllers in the `App\Http\Controllers` directory.
  - Organize by feature/module for large projects (e.g., `App\Http\Controllers\Api`, `App\Http\Controllers\Web`).
- **Models**: Store models in the `App\Models` directory.
- **Services**: Place classes in `App\Services` for reusable service classes.
- **Helpers**: Store global helpers and utils files and classes in `App\Helpers`.
- Avoid unnecessary API sub-folders like `App\Http\Controllers\Api\Mobile\v1\User`
  - Use `App\Http\Controllers\Api\User` instead. We always have only one API in all projects.
  - Do not use `App\Http\Controllers\Api` and `App\Http\Controllers\User` if project has no API at all
- Always when adding some new key/values to **.env file**, write those keys also to .env.example

## Security
- Use `$fillable` in models to prevent mass assignment vulnerabilities.
- Escape output in Blade templates using `{{ }}` or similar syntax to avoid XSS attacks.
- Avoid exposing sensitive information such as passwords or API tokens.
- Always hash passwords and sensitive data using Laravel's built-in tools like `bcrypt`.
- Never leak Eloquent Models into the front-end. Always use [API Resources](https://laravel.com/docs/11.x/eloquent-resources) and don't use `toArray()` on a Model.
- Never directly use a request to save a model (e.g. `Model::create($request->all())`).
- Files in the /public folder are directly accessible via the web. Ensure that sensitive files are not stored here.
- For private files, consider using the storage folder and serving them via a secure route.

## Performance
- Use eager loading to optimize database queries and avoid N+1 query issues.
- Always [prevent the lazy loading](https://laravel.com/docs/11.x/eloquent-relationships#preventing-lazy-loading) of relationships.
- If you need to display a set of database records, always use [pagination](https://laravel.com/docs/11.x/pagination).
- If you need to loop over database records and do some work, always use [chunks](https://laravel.com/docs/11.x/queries#chunking-results).
- Use queues for time-intensive operations, such as email sending or bulk data operations.
- Cache expensive operations using Laravel's caching system where applicable.

# Deeper Coding Principles

Please read 7 more detailed principles and coding standards:
1. [Routing](https://github.com/bohumer/coding-standard/blob/main/coding-standard/docs/1_routing.md)
2. [Controllers](https://github.com/bohumer/coding-standard/blob/main/coding-standard/docs/2_controllers.md)
3. [Models](https://github.com/bohumer/coding-standard/blob/main/coding-standard/docs/3_models.md)
4. [Migrations](https://github.com/bohumer/coding-standard/blob/main/coding-standard/docs/4_migrations.md)
5. [Views](https://github.com/bohumer/coding-standard/blob/main/coding-standard/docs/5_views.md)
6. [Validations](https://github.com/bohumer/coding-standard/blob/main/coding-standard/docs/6_validations.md)
7. [Testing](https://github.com/bohumer/coding-standard/blob/main/coding-standard/docs/7_testing.md)


---

By adhering to these standards, Laravel projects will maintain a high level of consistency, scalability, and readability.

Thank You for following it.
