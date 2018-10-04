# MVC core app class

See below, a core 'app' class for simple MVC, which parses a url for controller, action and params and then executes same.

V simple, but throws up some interesting design considerations:

- none of the functions called in the constructor return a value
  - some of these are setters, and that seems pretty normal for a setter to not return a value (though they could return $this, and that would allow them to be chained)
  - if they are not setters, is it ok for them to not return a value?
- some of the functions take a value, and some don't
  - if functions don't have side-effects I think it doesn't really matter if they for e.g. take $this->url_parts as a param, or if they take no param and operate on $this->url_parts internally, though the code is clearer if a param is given (and avoids constant use of $this within the function)
  - if the functions do have side effects, then you have to be more careful, because then the order they are called in can be significant, and that's something that needs avoiding, or needs to be very obvious (unlike some ASDAN code). Probably if you were being strict it should be the case that it shouldn't matter what order things are called in, or - given that isn't really feasible - every function should have clear error conditions that will show what the problem is (as in the below - if you call executeControllerAction before the controller and actions have been set, you will get a clear error)
- when should a function call another function and when not? For e.g. executeControllerAction calls createMessage - that de-clutters the constructor, but it isn't strictly necessary

All of these questions are around considerations about dependencies within the code, which is interesting. For e.g. if you don't pass $this->url_parts to setMethod, then the success of that function depends on other functions that handle url_parts not interfering with it in any way (e.g. unsetting keys as was the case in an earlier version). Well, er actually does it make any difference? hmmm...



```php
class App
{
    protected $url_parts;
    protected $controller = 'HomeController';
    protected $method = 'index';
    protected $params = [];

    public function __construct()
    {
        $this->url_parts = $this->parseUrl();
        $this->setController($this->url_parts);
        $this->setMethod($this->url_parts);
        $this->setParams($this->url_parts);

        $this->executeControllerAction();
    }

    public function executeControllerAction()
    {
        echo $this->createMessage();
        if (!class_exists($this->controller)){
            throw new Exception('No controller called ' . $this->controller);
        }
        if(!method_exists($this->controller, $this->method)) {
            throw new Exception($this->controller . ' has no action ' . $this->method);
        }
        call_user_func_array([$this->controller, $this->method], $this->params);
    }

    public function parseUrl()
    {
        $url_elements = explode('/', $_SERVER['REQUEST_URI']);
        return $url_elements;
    }

    public function setController($url_parts)
    {
        if(isset($url_parts[1])) {
            $this->controller = ucfirst($url_parts[1]) . 'Controller';
        }
        return $this;
    }

    public function setMethod($url_parts)
    {
        if(isset($url_parts[2])) {
            $this->method = $url_parts[2];
        }
        return $this;
    }

    public function setParams($url_parts)
    {
        if(array_key_exists(3, $url_parts)) {
            $this->params = array_slice($url_parts,3);
        }
        return $this;
    }

    public function createMessage()
    {
        $msg = $this->method . ' action of ' . $this->controller;
        if(count($this->params) > 0){
            $msg .= ' with params ' . implode(", ", $this->params);
        }
        return $msg;
    }
}
```

