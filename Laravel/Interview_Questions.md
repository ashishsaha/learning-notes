###### What is a mutator in Laravel models?

In Laravel, mutators are special methods defined in Eloquent models that allow you to modify the values of attributes before they are actually stored in the database. Mutators 
are particularly useful when you need to perform some transformation or formatting on the data before saving it. Mutators come in two flavors: accessor mutators and setter 
mutators.

Setter Mutators:
Setter mutators are used to modify attribute values before they are inserted or updated in the database. These mutators are named using the set{AttributeName}Attribute convention.
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
