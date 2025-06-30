## What are Traits in PHP? Why would you use one over inheritance?
Traits in PHP are a mechanism for code reuse in single inheritance languages like PHP. A trait allows you to define methods and properties that can be reused in multiple classes without using inheritance.
PHP only supports single inheritance, meaning a class can only extend one other class. Traits overcome this limitation by allowing you to inject reusable behavior into multiple unrelated classes.
### Why use Traits over inheritance? 
PHP does not support multiple inheritance, so if you need to share methods across classes that don’t share the same parent, you can use Traits.
They allow better modularity and separation of concerns.
Prevent code duplication across classes.
### Example  
 ```php
trait Logger {
    public function log($message) {
        echo "[LOG]: " . $message;
    }
}

class User {
    use Logger;

    public function create() {
        $this->log("User created.");
    }
}

class Product {
    use Logger;

    public function save() {
        $this->log("Product saved.");
    }
}

// Usage
$user = new User();
$user->create(); // Outputs: [LOG]: User created.

$product = new Product();
$product->save(); // Outputs: [LOG]: Product saved.
```
### When useful
Useful when multiple classes need shared behavior but can't extend the same parent.

## How would you design a system to store millions of log records per day, and query them efficiently?
### Why it’s asked:
To assess data modeling, performance planning, and query strategies.
### Possible Answer:
Use partitioning by date or ID in MySQL.
Optimize with indexes on timestamp, user_id, etc.
Consider archiving old logs or moving to a time-series database if analytics is needed.

Table design:
```php
CREATE TABLE logs (
    id BIGINT AUTO_INCREMENT,
    user_id INT,
    action VARCHAR(255),
    created_at DATETIME,
    INDEX(user_id),
    INDEX(created_at)
);
```

## How would you design a scalable notification system for a large PHP application?
### What it's testing:
Asynchronous processing, decoupling, and queue usage.
### Expected Concepts:
Use a queue system like Laravel Queue (with Redis or SQS).
Push notification jobs to the queue to offload processing.
Store logs in a notifications table (relational).
Use broadcasting (e.g., Pusher, Socket.IO, or WebSockets) for real-time delivery.
Retry failed jobs, monitor with Laravel Horizon.

## Your MySQL queries are slowing down under high traffic. What steps do you take to investigate and optimize?
### What it's testing:
Query tuning, indexing, and database scaling.
### Expected Steps:
Use EXPLAIN to analyze slow queries.
Check for missing indexes.
Avoid SELECT *; fetch only needed columns.
Optimize JOINs and GROUP BY usage.
Use query caching and database connection pooling.
Archive old data or consider sharding for huge datasets.

## How would you design a multi-tenant SaaS application using PHP + MySQL?
### What it's testing:
Database design patterns, isolation strategy.
### Expected Approaches:
Database-per-tenant: best isolation, scalable.
Schema-per-tenant: moderate isolation, one DB.
Single DB, shared schema: use tenant_id in all tables.
Use Laravel packages like stancl/tenancy to manage tenants.
Secure access using middleware that resolves the tenant from domain or user context.

## How do you handle large file uploads in a PHP app without crashing the server?
### What it's testing:
Memory, resource limits, and asynchronous processing.
### Best Practices:
Adjust php.ini: increase upload_max_filesize, post_max_size, and max_execution_time. 
Upload in chunks using JS libraries (e.g., Dropzone, tus.io). 
Store files in cloud (S3) via direct upload or using presigned URLs. 
Process uploaded files asynchronously via queues.