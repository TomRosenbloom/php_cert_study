# Generating Objects

Zandstra has a chapter on patterns for generating objects. First up is Singleton - easy. Then Factory Method Pattern (and Abstract Factory Pattern). It's not so much difficult to see how factory patterns work, but more *why* they are needed.

The 'normal' way of creating an object is to instantiate it directly with $foo= new Foo($params). With a Factory it's something like $foo = FooFactory::create($params), or $foo = ObjectFactory('foo', $params). So what are the benefits of using this approach?

> "...choose between several interchangeable classes at runtime..."
>
> "...separate the creation of an object from the actual implementation..."
>
> used when:
>
> - A class cannot anticipate the object types that needs to create beforehand.
> - We want to encapsulate the logic for instantiating complex objects.
> - We want to reduce tight coupling between our application classes.

The above quotes are from https://coderoncode.com/design-patterns/programming/php/coding/development/2014/01/19/design-patterns-php-factories.html which also has a v good example. In this example, they imagine a system for managing IKEA products. So we have an abstract Product class:

```php
abstract class Product 
{
  private $sku;
  private $name;
  protected $type = null;
  
  public function __construct($sku, $name)
  {
    $this->sku = $sku;
    $this->name = $name;
  }
  
  public function getSku()
  {
    return $this->sku;
  }
  
  public function getName()
  {
    return $this->name;
  }
  
  public function getType()
  {
    return $this->type;
  }
}
```

This is extended for *every* product:

```php
class Product_Chair extends Product
{
  protected $type = 'chair';
}
class Product_Table extends Product
{
  protected $type = 'table';
}
// etc etc etc
```

Now imagine a controller called by http://factory.ikea.com/product/create/{product_type} with the following code:

```php
class ProductController {
  
 public function create($product_type)
 {
   // Some post data validation logic here
   
   // Now we need to instantiate our product 
   switch($product_type)
   {
     case 'chair':
       $product = new Product_Chair($post['sku'],$post['name']);
       break;
       
      case 'table':
       $product = new Product_Table($post['sku'],$post['name']);
       break;
           
       // etc etc etc
   }
   
   // Do something with the post data and save the product 
   
   return $product->getType();
 }
}
```

The switch statement would become absolutely humungous. Furthermore, if the Product class was changed to take a new parameter, every single one of these case clauses would need to be changed individually.

But if we make a ProductFactory:

```php
class ProductFactory 
{
  public static function build($product_type, $sku, $name)
  {
    $product = "Product_" . ucwords($product_type);
    if(class_exists($product))
    {
      return new $product($sku, $name);
    }
    else {
      throw new Exception("Invalid product type given.");
    }
  }
}
```

Then the entire switch statement can be replaced with:

```
$product = ProductFactory::build($product_type, $post['sku'], $post['name']);
```

So that's pretty convincing. And from [elsewhere](https://softwareengineering.stackexchange.com/questions/253254/why-should-i-use-a-factory-class-instead-of-direct-object-construction):

> [benefits of using factory]
>
> - It allows you to introduce an [Inversion of Control](https://en.wikipedia.org/wiki/Inversion_of_control) (IoC) container easily
> - It makes your code more testable as you can mock interfaces
> - It gives you a lot more flexibility when it comes time to change the application (i.e. you can create new implementations without changing the dependent code)

Also in this thread there are dissenters. Their point is really only that you shouldn't use factory method just for the sake of it, because it's the only pattern you know (a violation of [YAGNI](http://en.wikipedia.org/wiki/You_aren't_gonna_need_it)). It seems also that there is some received wisdom that you should avoid using 'new', so that gets overapplied by coders without them understanding why (and hence why not).

