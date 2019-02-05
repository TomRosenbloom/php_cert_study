# Dependency Injection

I like the coverage of DI in this article: https://hackernoon.com/magic-methods-in-oop-the-definitive-guide-php-e3f8a0da278

As he says, DI is "a 25-dollar term for a 5-cent concept". Actually I think he may have nicked that from: https://www.jamesshore.com/Blog/Dependency-Injection-Demystified.html

Anyway, DI just means that where you have a piece of code that depends on some other piece of code, you send that second bit of code in from outside in a very explicit and clear way rather than calling it invisibly from within the first piece of code. Note that the second piece of code, the dependency, is typically something very simple - perhaps just a variable name - or it may be a reference to a larger piece of code.

I think it's probably true that DI isn't *necessarily* only applicable to OOP, but it is generally an OOP thing, and in that context, and quoting the second ref above, "Dependency injection means giving an object its instance variables". That is, giving one object the other objects that it needs rather than having it construct them itself https://stackoverflow.com/a/140655.

```php
class noiseGenerator {
    private $noise;
    function __construct(string $noise){
        $this->noise = $noise;
    }
    public function makeNoise() {
        echo "make a " . $this->noise . " noise";
    }
}

// without DI
class doorbell {
	static function ring() {
        $noise = new noiseGenerator('chimes');
        $noise->makeNoise();
	}
}
doorbell::ring();

// with DI
class doorbell {
    static function ring(noiseGenerator $noiseGenerator){
        $noiseGenerator->makeNoise();
    }
}
doorbell::ring(new noiseGenerator('chimes'));
```

[I think I could have made this example better, more in the style of https://stackoverflow.com/a/44945310, using an interface for *noise* and implementing it as *chimes* etc. but this will do for now].

The DI version has a few advantages. It's obvious now that doorbell has a dependency. It's obvious and easy to change the noise [though I think that part of my example is a bit off]. It also has advantages for testing apparently, but I need to come back to that.

Another thing that is questionable about my example is that I'm injecting the dependency via a (static) function call. In fact it is more usual to inject via the constructor or perhaps via a setter.

## Dependency Inversion

Dependency Injection is a *form of* Dependency Inversion. They are not interchangeable terms.