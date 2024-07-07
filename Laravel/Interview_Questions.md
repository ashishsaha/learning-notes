
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


#### What is the purpose of accessors in Eloquent models?
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


## What is the purpose of the $guarded and $fillable property in a model?
The **$guarded** property in a Laravel Eloquent model serves a purpose similar to **$fillable** but with an opposite effect. While the **$fillable** property is used to specify 
which attributes are allowed to be mass-assigned, the **$guarded** property is used to specify which attributes should not be mass-assigned.


#### How to implement soft delete in Laravel?

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



