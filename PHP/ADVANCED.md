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