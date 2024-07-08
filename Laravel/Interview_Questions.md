## How Laravel Request Life cycle works
Imagine a simple web application where a user requests to view a list of products by visiting the /products URL.
 
**Request Entry:**
The user enters http://example.com/products in their browser.
The request hits the public/index.php file.
 
**HTTP Kernel:**
The index.php file creates an instance of the HTTP kernel (App\Http\Kernel).
The kernel handles the request and begins the process.
 ```php
// public/index.php
require __DIR__.'/../vendor/autoload.php';
$app = require_once __DIR__.'/../bootstrap/app.php';
$kernel = $app->make(Illuminate\Contracts\Http\Kernel::class);
$response = $kernel->handle(
 $request = Illuminate\Http\Request::capture()
);
$response->send();
$kernel->terminate($request, $response);
```

**Service Providers:**
The kernel bootstraps the application by loading service providers.
Service providers bind services into the service container and perform bootstrapping tasks.
 ```php
// config/app.php
'providers' => [
    // List of service providers
    App\Providers\RouteServiceProvider::class,
],
```

**Middleware**
The request passes through global and route-specific middleware.
Middleware can perform tasks such as checking for authenticated users or logging.
 ```php
// app/Http/Middleware/CheckForMaintenanceMode.php
public function handle($request, Closure $next)
{
    if ($this->app->isDownForMaintenance()) {
        throw new HttpException(503);
    }

    return $next($request);
}
```

**Routing**
The router determines the appropriate route and controller action for the /products URL.
The route is defined in routes/web.php.
 ```php
// routes/web.php
Route::get('/products', [ProductController::class, 'index']);
```
**Controller**
The ProductController@index method is invoked to handle the request.

**Response Preparation:**
The controller method prepares the response by fetching the data and passing it to a view.
The response is then modified by middleware if necessary.

**Response Sending:**
The HTTP kernel sends the response back to the client.
 ```php
$response->send();
```
**Termination**
After the response is sent, the kernel’s terminate method is called for any post-response tasks, such as logging the request duration.
 ```php
$kernel->terminate($request, $response);
```
#### Summary of the Example
**Request Entry**: User requests http://example.com/products.
**HTTP Kernel**: public/index.php handles the request.
**Service Providers**: Loaded and bootstrapped.
**Middleware**: Request passes through middleware (e.g., maintenance mode check).
**Routing**: /products route matched to ProductController@index.
**Controller**: ProductController@index fetches products and returns the view.
**Response Preparation**: View rendered with product data.
**Response Sending**: Response sent to client.
**Termination**: Post-response tasks executed (e.g., logging).


## What is the purpose of the $guarded and $fillable property in a model?
The **$guarded** property in a Laravel Eloquent model serves a purpose similar to **$fillable** but with an opposite effect. While the **$fillable** property is used to specify 
which attributes are allowed to be mass-assigned, the **$guarded** property is used to specify which attributes should not be mass-assigned.


## What is a mutator in Laravel models?
In Laravel, mutators are special methods defined in Eloquent models that allow you to modify the values of attributes before they are actually stored in the database. Mutators 
are particularly useful when you need to perform some transformation or formatting on the data before saving it. Mutators come in two flavors: **accessor mutators** and **setter mutators**.

**Setter mutator** is used to modify attribute values before they are inserted or updated in the database. These mutators are named using the set{AttributeName}Attribute convention.
```php
class YourModel extends Model
{
    public function setNameAttribute($value)
    {
        $this->attributes['name'] = ucfirst($value); // Capitalize the first letter before storing in the database
    }
}
```
In this example, every time you set the name attribute on the model, the setNameAttribute mutator will be called, and it will capitalize the first letter of the name before 
saving it to the database.


## What is the purpose of accessors in Eloquent models?
Accessors provide a way to customize the data returned from a model, allowing you to perform transformations, formatting, or any other logic before the attribute is accessed.

**Accessor mutators** allow you to modify attribute values when they are retrieved from the database. These mutators are named using the get{AttributeName}Attribute convention.
```php
class YourModel extends Model
{
    public function getNameAttribute($value)
    {
        return strtoupper($value); // Convert the name to uppercase when retrieving from the database
    }
}
public function getCreatedAtAttribute($value)
{
    return Carbon::parse($value)->format('Y-m-d H:i:s');
}

public function getTotalPriceAttribute()
{
    return $this->price * $this->quantity;
}
```


## How to implement soft delete in Laravel?
Soft deletes in Laravel allow you to "delete" a record from the database without actually removing it. Instead, a timestamp is set to mark the record as "deleted," and it remains 
in the database, providing a way to recover or view deleted records if needed. 

Here's how you can implement soft deletes in Laravel:
Database Table Setup:
In your migration file, add a deleted_at column to the table where you want to implement soft deletes. This column will store the timestamp when a record is soft deleted.
```php
Schema::table('your_table', function ($table) {
    $table->softDeletes();
});
```

Run the migration: ```php php artisan migrate.```
Eloquent Model Configuration:
In your Eloquent model, use the SoftDeletes trait.
```php
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\SoftDeletes;

class YourModel extends Model
{
    use SoftDeletes;
    // ...
}
```
**Recovering Soft Deleted Records**
You can retrieve soft deleted records using the withTrashed() method or onlyTrashed() method.
**Force Deleting and Restoring**
To permanently remove a soft-deleted record, you can use the forceDelete() method.


## Migration in laravel and why it is necessary?
**What is Migration?**
A migration in Laravel is a way to manage database schema changes in a structured and version-controlled manner. Migrations allow developers to define and modify database tables 
using PHP code rather than raw SQL. This helps in keeping track of changes, making it easier to collaborate and deploy database changes.

###### Why is Migration Necessary?
**Version Control for Database:**
Migrations provide a way to version control your database schema, similar to how you version control your codebase. This ensures that all team members and deployment environments are using the same database structure.
**Ease of Collaboration:**
When working in a team, migrations allow each developer to apply the same database schema changes without having to manually write SQL scripts. This reduces the chance of errors and inconsistencies.
**Database Agnosticism:**
Migrations are written in PHP, making your schema definitions database-agnostic. Laravel can translate the migration code into the appropriate SQL for different database systems (e.g., MySQL, PostgreSQL, SQLite).
**Rollback Support:**
Migrations allow you to rollback changes. If you make a mistake, you can easily revert to the previous state without manual intervention.
**Automation in Deployment:**
Migrations can be automated in deployment scripts to ensure that the database schema is updated automatically when new code is deployed.

###### About:
**up Method**: Defines the changes to apply to the database (e.g., creating a table).
**down Method**: Defines how to revert the changes (e.g., dropping the table).
###### Running Migrations
```php
php artisan make:migration create_products_table
php artisan migrate
php artisan migrate:rollback
```
**Summary**
Migrations in Laravel provide a way to manage database schema changes in a version-controlled and automated manner. They are necessary to ensure consistency, ease collaboration, 
support rollback, and automate database updates during deployment.


## How Eager loading works?
Eager loading in Eloquent is a performance optimization technique that helps to reduce the number of database queries when retrieving related models. By default, Eloquent uses 
**lazy loading**, which means related models are only loaded from the database when accessed. However, lazy loading can lead to the **N+1 query problem**, where additional 
queries are executed for each related model, resulting in performance issues.
Eager loading addresses this problem by allowing you to load related models upfront in a single query, rather than loading them individually when accessed. This can significantly 
improve performance, especially when dealing with relationships between models.

Here's how eager loading works in Eloquent:
Basic Eager Loading:
To use eager loading, you can use the with method on your Eloquent query. This method accepts an array of relationships to load.
```php
// Without eager loading (N+1 query problem)
$posts = Post::all();
foreach ($posts as $post) {
    $comments = $post->comments; // Each access triggers a new query
}

// With eager loading
$posts = Post::with('comments')->get(); // Eager loading
foreach ($posts as $post) {
    $comments = $post->comments; // No additional queries; already loaded
}
```


## What are query scopes in Laravel models?
In Laravel, query scopes are a way to encapsulate common query constraints within your Eloquent models. They allow you to define reusable, named sets of constraints that can be 
applied to queries. Query scopes provide a convenient and expressive way to encapsulate parts of your queries and improve the readability and maintainability of your code.
To define a query scope, you can add a method to your Eloquent model with a name prefixed by "scope":
```php
class Post extends Model
{
    // Scope to retrieve only published posts
    public function scopePublished($query)
    {
        return $query->where('is_published', true);
    }

    // Other model code...
}
```

In this example, the scopePublished method defines a query scope named "published" that adds a constraint to retrieve only posts where the is_published column is true.
Now, you can use this query scope in your code like this:
```php
$publishedPosts = Post::published()->get();
```

