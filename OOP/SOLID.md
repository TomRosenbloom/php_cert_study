# SOLID

## The Principles

### Single responsibility

Each unit of code - module, class, model, view, controller, method, whatever - should do just one thing. If this is realised then any such unit can be updated/replaced without danger of unexpected side effects. Also,   such units can be re-used in other contexts, and they make it easier to understand a system, or rather they mean you don't have to understand the whole system before working on any part of it. When you consider a higher level unit like a model or a controller, they don't literally do only one thing, but they are nevertheless responsible for a single part of a greater whole.

There's a lot of debate about what is the difference between Single Responsibility and Separation of Concerns. My take is that SR is the application of SoC. Another view is that SR specifically refers to classes in OOP. This is the Robert Martin definition. "A class should have only one reason to change". 

I think some of the semantic debate comes from uncertainty about whether these are *general* principles or specifically OOP principles. I think they are intended and hence should be considered as specific OOP principles, and in that case the Robert Martin definition is better. 

### Open/closed

"*software entities (classes, modules, functions, etc.) should be open for extension, but closed for modification*" 

So you could interpret 'closed' as meaning 'signed off'. So once a module has been completed and tested and gone into use, it shouldn't be changed, because of the risk of side effects, but it can be extended to create something new.

That was the original definition (Bertrand Meyer), but the more modern definition is more flexible, allowing a module to change *how* it does something, as long as it follows a blueprint for *what* it will do. This is realised in OOP with implementation of interfaces and/or inheritance from abstract base classes.

Again here, there is a more general principle (Meyer) and a specifically OOP principle/application.

### Liskov substitution

This one is a bit more difficult to get your head around. Basically, the functionality of subtypes should be substitutable with their supertypes. If you find yourself having to add some unique functionality to a subtype, then you're doing something wrong and you should re-think the definition of your supertype(s).

A classic example concerns squares and rectangles. It seems logical that Square should be a subtype of Rectangle, since it is a special case of a rectangle. However, Rectangle will require setWidth and setHeight, whereas these don't work for Square, so you'd have to add some functionality to Square to allow for that. What you should really do is have Quadrilateral as your supertype and Square and Rectangle as subtypes (you could I guess use some composition e.g. Decorator pattern to allow Square and Rectangle to share some methods/properties as appropriate). 

Another example that I like is:

```
public class Bird{
    public void fly(){}
}
public class Duck extends Bird{}

public class Ostrich extends Bird{} // oops
```

Corrected version:

```
public class Bird{
}
public class FlyingBirds extends Bird{
    public void fly(){}
}
public class Duck extends FlyingBirds{}
public class Ostrich extends Bird{} 
```

I'm trying to think where I've encountered this problem, because I definitely have but can't put my finger on it...

### Interface segregation

As with all of these principles, this is fundamentally about avoiding dependencies. In this case by breaking large interfaces into smaller units. In fact it is about a particular consequence of dependency i.e. that changes to a sub-system may require the whole system to be re-compiled. And it does specifically apply to interfaces. It's not a general principle like some of the others.

The basic idea is to avoid having at the base of a system a large interface that every task has to implement, even when the majority of the methods do not apply. There are a couple of classic examples from Robert Martin. One concerns the user interface for ATM machines. Every type of transaction - deposit, withdraw, etc. - has the login process in common, but after that they diverge. Hence the interface segregation principle should be applied to separate login functionality from transaction functionality.

### Dependency inversion

*A. High-level modules should not depend on low-level modules. Both should depend on abstractions.*
*B. Abstractions should not depend on details. Details should depend on abstractions.*

Also:

*Program to an interface, not an implementation*

NB not to be confused with dependency *injection*, that is something different. Likewise inversion of control.

https://code.tutsplus.com/tutorials/solid-part-4-the-dependency-inversion-principle--net-36872

I need to come back to this, but what this is about is separating out what components do (abstraction) - and providing that via an interface - from how they do it (implementation). A data abstraction layer is an example - easy to see how. Also the use of factories - not so easy to explain...