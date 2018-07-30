# Zandstra on Composition and Inheritance

We all know about inheritance, and it's an easy thing to understand, but it has limitations. Mr Z. presents an example of a situation where simple inheritance falls short and the use of composition (with inheritance) is needed for a proper solution.

## Initial example: simple inheritance

He starts with a simple example where an abstract Lesson class, with abstract methods cost() and chargeType() is extended to produce two implementing classes, FixedPriceLesson and TimedPriceLesson. The constructor of Lesson takes *duration* as param, so I guess the implementation of this v simple scenario goes something like this:

```php
abstract class Lesson {
    protected $duration; // in minutes
    
    function __construct($duration) {
        $this->duration = $duration;
    }
    
    function cost() {}
    
    function chargeType() {}
}

class FixedPriceLesson {
    function cost() {
        return 30;
    }
    function chargeType() {
        return "fixed rate";
    }
}

class TimedPriceLesson {
    function __construct($duration){
        parent::__construct($duration);
    }    
    function cost() {
        return $this->duration * 0.5;
    }
    function chargeType() {
        return "timed rate";
    }    
}

$lesson = new TimedPriceLesson(30);
echo $lesson->cost();
```

Why an abstract class an not an interface? The general rule is you use an abstract class to define what the inheriting classes *are* and you use an interface to create a contract specifying what the implementing class must *do*. By that token, an interface doesn't provide any shared functionality as an abstract class does, but is just a promise/contract about what functionality an implementing class will have. In fact, you could say that an interface *shouldn't* provide actual functionality because the point of it is to say 'this thing must do this' but specifically *not* define how it does it. If two subclasses do the same thing in the same way, then you should use an abstract class. The point of an abstract class is to include shared functionality to prevent code duplication. So abstract classes and interfaces are really very different things.

We can get away without a constructor in FixedPriceLesson because duration is not used. NB this would be different if Lesson was an interface rather than an abstract object, and I think this is *why* we use an abstract class and not an interface in this scenario - because duration is not a contractual obligation since it is not relevant to FixedPriceLesson (as far as calculating cost is concerned).

## Inheritance breaks down

What if we want the lessons to have some other category, which doesn't exclude the original distinction between pricing categories? His example is Lecture and Seminar. These can be fixed price or timed price, and so we have four possible combinations - fixed price lecture, timed price lecture, fixed price seminar, and timed price seminar.

If you try and model this with inheritance only you have to create classes for each of these four possible combinations - say Lecture and Seminar extend Lesson, and then FixedPriceLecture and TimedPriceLecture extend Lecture and so on (or you could do it the other way, dividing the inheritance tree on fixed/timed first). That will mean you have to either duplicate code in the lower level classes, or you have to put conditional logic higher up, in the Lesson super class. 

## Solution: Strategy Pattern

The solution in this case is to use Strategy pattern.

```php
abstract class Lesson {

    private   $duration; // minutes
    private   $costStrategy;

    function __construct($duration, CostStrategy $strategy) {
        $this->duration = $duration;
        $this->costStrategy = $strategy;
    }
    function cost() {
        return $this->costStrategy->cost($this);
    }
    function chargeType() {
        return $this->costStrategy->chargeType( );
    }
    function getDuration() {
        return $this->duration;
    }

}

abstract class CostStrategy {
    abstract function cost(Lesson $lesson);
    abstract function chargeType();
}

class TimedCostStrategy extends CostStrategy {
    function cost(Lesson $lesson) {
        return ($lesson->getDuration() * 0.5);
    }
    function chargeType() {
        return "hourly rate";
    }
}

class FixedCostStrategy extends CostStrategy {
    function cost(Lesson $lesson) {
        return 30;
    }
    function chargeType() {
        return "fixed rate";
    }
}

class Lecture extends Lesson {
    // Lecture-specific implementations ...
}

class Seminar extends Lesson {
    // Seminar-specific implementations ...
}

$lecture = new Lecture(30, new TimedCostStrategy());
echo $lecture->cost();
```

Now when you instantiate a Lecture or Seminar you must supply a CostStrategy object as well as duration. NB in this instance, I feel like CostStrategy maybe should be an interface? 

