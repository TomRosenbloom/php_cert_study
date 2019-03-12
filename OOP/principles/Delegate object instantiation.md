# Delegate object instantiation

As stated by Zandstra, you should follow the general principle of delegating object instantiation i.e. not generating specific objects at the point they are used, but generalising the process. Kind of thing.

In practice this often (always?) means creating an object from a string representation of its name, and this is something I have done frequently in my own code. For example, in my DIY MVC, the abstract BaseController class instantiates a model in its constructor, but only when implemented by an extending controller which supplies the model name. Like this:

```php
abstract class BaseController
{
    protected $modelName;
    protected $model;
    
    function __construct() {
        $this->model = new $this->modelName;
    }
}

class EventController extends BaseController
{
    public function __construct()
    {
        $this->modelName = "Event";
        parent::__construct();
    }
}
```

So rather than creating an Event model in the EventController, I've delegated it to the BaseController, where it is done in a generic way that will serve for any future controller classes that extend BaseController.

I'm sort of doing the same thing with models. In this case I'm sending back a table name to BaseModel and this is used to run queries. I'm not anywhere in this process instantiating objects, but I could, probably should, and in due course will be doing that when I use some kind of proper OOP method for returning database query results.