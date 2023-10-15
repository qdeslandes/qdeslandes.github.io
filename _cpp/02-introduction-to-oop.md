---
layout: page
title:  "Introduction to Object-Oriented Programming (OOP)"
collection: cpp
---

The following lesson will introduce fundamental Object-Oriented Programming (OOP) concepts, using C++ as an example. Those concepts are valid for every OOP language, even though the specific keywords used in the various examples are only valid for C++.

## Basic concepts

OOP is a programming paradigm that provides a structured way to design and organize code by modeling real-world entities as objects. It emphasizes the interaction between data (attributes or properties) and behavior (methods or functions): data and behavior are bundled together within an object. Unlike procedural programming, where data and functions are separate, OOP encapsulates them as a single unit.

### Class

[Classes][class_cppref] are a structural concept in OOP. It serves as a blueprint for creating objects in a program. A class defines the structure, the content, and the purpose of an object. A class is a fully-fledged, user-defined type, like `int` or `unsigned short` could be.

In C++, it looks like this (see on [Compiler Explorer][ce_class]):
```cpp
class Car
{
};
```

Using the [`class` keyword][class_keyword_cppref], we define a new class called `Car`. Our class is now a type and can be used to create objects of type `Car`. For now, our class doesn't do anything, but it's still a valid one!


### Object

As a class is only a blueprint for a type, what happens when you declare a variable of that type? You created an object! An object is an instance of a class. You can create as many objects as you want.

Continuing on the previous example (see on [Compiler Explorer][ce_object]):
```cpp
int main()
{
    Car myCar;
    Car myOtherCar;

    return 0;
}
```

We now create two objects of the same class (hence, the same type): `myCar` and `myOtherCar`. Both are allocated on the stack, with automatic storage duration: when the `main()` function returns, both objects will be destroyed, as would be an `int` or `double`.

### Attribute

We now have objects instantiated from our class, but they don't do much!

We previously stated that OOP is about collocating the data and behavior together in the object: your object will store the data and the behavior to apply to that data. That's what [attributes][attr_cppref] are for: attributes are the data. Attributes are also called "properties".

Let's add data to our class (see on [Compiler Explorer][ce_attr]):
```cpp
class Car
{
    enum Brand brand;
    enum Color color;
    uint8_t speed;
};
```

Assuming we defined `enum Brand` and `enum Color` earlier, we now have data in our class! Remember, the class is only a blueprint, so it defines the attributes without any data, as it doesn't represent anything yet until we create an object.

In our `Car` class, we have 3 attributes:
- `brand`, of type `enum Brand`
- `color`, of type `enum Color`
- `speed`, of type `uint8_t` (an 8 bits unsigned value)

```cpp
Car myCar;
```

The object `myCar`, of type `Car`, now has 3 attributes. If we compare the size of the `myCar` object (using `sizeof(myCar)`):
- No attribute (empty class): 1 byte
- 3 attributes as defined above: 12 bytes

By changing the class definition, we have changed what our object contains!

### Method

Objects don't just contain data; they also exhibit behavior. Behavior is defined through methods (a function defined in a class) within the class itself.

We can now add methods to our class (see on [Compiler Explorer][ce_method]):
```cpp
class Car
{
private:
    enum Brand brand;
    enum Color color;
    uint8_t speed;

public:
    void accelerate()
    {
        speed += 1;
    }
};
```

We added a few things to our class! First of all, let's go with the method `accelerate()`, it's pretty straighforward: we simply define a function within the class. This function doesn't have any parameter, although it could, and it could also return a value (but it doesn't).

Our methods have access to our classe's attributes and other method. On the example above, `accelerate()` can access `speed`, but also `brand` and `color`. Remember: our attributes are the data, and our methods are the behavior to apply to the data. Here's how we would call our method: `myCar.accelerate();`.

Now, what about `private` and `public`? That's a story for another lesson! For now, we'll stick to `private:` before our attributes, and `public:` before our methods!

### To go further

You might wonder what would happen if you called `myCar.accelerate()`, or you might not. Well, you should, because we haven't assigned any value to `myCar.speed`. We can't do `myCar.speed = 0;` because `speed` is private (again, more on that later), so what would `speed += 1` do in `accelerate()`?!

It's also a story for another lesson, but there are multiple ways to set default values for an object's properties. Until we learn about constructors, you can do the following:

```cpp
class Car
{
    enum Brand brand = Ford;
    enum Color color = Red;
    uint8_t speed = 0;
};
```

Now, each object you create will have a default value for `brand`, `color`, and `speed`.

## OOP's 4 fundamental pillars

Now that we've learned about the basic concepts, let's see how we use them in accordance with OOP's 4 fundamental pillars. The 4 pillars defined below will have their own lesson, this is only an introduction.

### Encapsulation

Encapsulation is the ability to hide details from the user of your code and encapsulate everything under a clear interface. An example of encapsulation would be a "setter", which is a function used to assign a value to a property of the object. The setter is also responsible for validating the value beforehand.

For our `Car` class, it could look like this (see on [Compiler Explorer][ce_encap]):
```cpp
#define MAX_NUM_PASSENGERS 8

class Car
{
private:
    // ...
    int numPassengers = 0;

public:
    // ...

    int getNumPassengers()
    {
        return numPassengers;
    }

    void setNumPassengers(int _numPassengers)
    {
        if (_numPassengers < 0 || _numPassengers > MAX_NUM_PASSENGERS)
            return;

        numPassengers = _numPassengers;
    }
};
```

Let's analyze this snippet: we can see a new attribute `numPassengers`, with a default value of 0. This is the number of passengers in our car (obviously). There are also 2 new methods:
- `getNumPassengers()` which returns the current number of passengers in the car.
- `setNumPassengers()` which allows the user to set the number of passengers in the car. Before changing the number of passengers, `setNumPassengers()` will ensure the new value is valid: it's a positive number, smaller than 8.

As defined by the encapsulation concept, our properties are not modified directly by the user, but through a method that allows us to validate the value prior to changing the attribute.

### Abstraction

Abstraction involves simplifying complex reality by modeling classes based on essential properties and behaviors. It hides irrelevant details, focusing on what is important for the object's intended use.

We actually already used encapsulation in the `Car` class example! Do you remember `accelerate()`? The user of the `Car` object has no knowledge of the `speed` attribute, they just know that `accelerate()` is used to make the car go faster! We could go further and have an acceleration factor depending on the car's brand (or color: everyone knows red cars go faster!), and these details would still be hidden behind `accelerate()`.

Our users dont't have to know about the details of our class, they only need to focus on the fact that the `Car` class is used to represent a car. It can accelerate and have passengers, but the exact details of how the car works are irrelevant to them.

### Inheritance

Inheritance allows you to create new classes based on existing ones. The new classes will inherit the existing class data and behavior to some extent. This facilitates code reuse and the establishment of relationships between classes.

We could imagine a `RaceCar` object, which inherits from `Car`, having the same properties and methods but a slightly different behavior. The `RaceCar` could, for example, go faster and brake harder, but it can still, like any `Car`, accelerate. This way, we don't have to reimplement all the basic properties and methods of a car, we can simply start with `Car` and extend the class.

I won't go much more into details for now, to keep things simple. We will have a detailed lesson on inheritance.

### Polymorphism

Polymorphism (literally "multiple shapes") is the capacity for a given object to be used as an object of a parent class. It allows for flexibility in designing and working with objects.

Continuing with our `RaceCar` example: `RaceCar` inherits from `Car`. We would say that `Car` is the base (or parent) class, while `RaceCar` is the derived (or child) class. Because `RaceCar` inherits from `Car`, `RaceCar` is also a `Car`, which makes sense from a real-world perspective, right? What this means, is that every object created from a class that is derived from `Car` can be used as a argument to a function having a `Car` as a parameter!

I won't go much more into details for now, to keep things simple. We will have a detailed lesson on polymorphism.

[class_cppref]: https://en.cppreference.com/w/cpp/language/classes
[class_keyword_cppref]: https://en.cppreference.com/w/cpp/keyword/class
[attr_cppref]: https://en.cppreference.com/w/cpp/language/data_members

[ce_class]: https://godbolt.org/#g:!((g:!((g:!((h:codeEditor,i:(filename:'1',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,selection:(endColumn:1,endLineNumber:1,positionColumn:1,positionLineNumber:1,selectionStartColumn:1,selectionStartLineNumber:1,startColumn:1,startLineNumber:1),source:'class+Car%0A%7B%0A%7D%3B%0A%0Aint+main()%0A%7B%0A++++return+0%3B%0A%7D'),l:'5',n:'0',o:'C%2B%2B+source+%231',t:'0')),k:50,l:'4',n:'0',o:'',s:0,t:'0'),(g:!((h:executor,i:(argsPanelShown:'1',compilationPanelShown:'0',compiler:g132,compilerName:'',compilerOutShown:'0',execArgs:'',execStdin:'',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,libs:!(),options:'',overrides:!(),source:1,stdinPanelShown:'1',tree:0,wrap:'1'),l:'5',n:'0',o:'Executor+x86-64+gcc+13.2+(C%2B%2B,+Editor+%231)',t:'0')),k:50,l:'4',n:'0',o:'',s:0,t:'0')),l:'2',n:'0',o:'',t:'0')),version:4
[ce_object]: https://godbolt.org/#g:!((g:!((g:!((h:codeEditor,i:(filename:'1',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,selection:(endColumn:20,endLineNumber:8,positionColumn:20,positionLineNumber:8,selectionStartColumn:20,selectionStartLineNumber:8,startColumn:20,startLineNumber:8),source:'class+Car%0A%7B%0A%7D%3B%0A%0Aint+main()%0A%7B%0A++++Car+myCar%3B%0A++++Car+myOtherCar%3B%0A%0A++++return+0%3B%0A%7D'),l:'5',n:'0',o:'C%2B%2B+source+%231',t:'0')),k:50,l:'4',n:'0',o:'',s:0,t:'0'),(g:!((h:executor,i:(argsPanelShown:'1',compilationPanelShown:'0',compiler:g132,compilerName:'',compilerOutShown:'0',execArgs:'',execStdin:'',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,libs:!(),options:'',overrides:!(),source:1,stdinPanelShown:'1',tree:0,wrap:'1'),l:'5',n:'0',o:'Executor+x86-64+gcc+13.2+(C%2B%2B,+Editor+%231)',t:'0')),k:50,l:'4',n:'0',o:'',s:0,t:'0')),l:'2',n:'0',o:'',t:'0')),version:4
[ce_attr]: https://godbolt.org/#g:!((g:!((g:!((h:codeEditor,i:(filename:'1',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,selection:(endColumn:3,endLineNumber:15,positionColumn:3,positionLineNumber:15,selectionStartColumn:3,selectionStartLineNumber:15,startColumn:3,startLineNumber:15),source:'%23include+%3Cstdint.h%3E+//+For+uint8_t%0A%0Aenum+Brand%0A%7B%0A++++Ford,%0A++++Lada,%0A++++Peugeot%0A%7D%3B%0A%0Aenum+Color%0A%7B%0A++++Red,+%0A++++Green,+%0A++++Blue%0A%7D%3B%0A%0Aclass+Car%0A%7B%0A++++enum+Brand+brand%3B%0A++++enum+Color+color%3B%0A++++uint8_t+speed%3B%0A%7D%3B%0A%0Aint+main()%0A%7B%0A++++Car+myCar%3B%0A++++Car+myOtherCar%3B%0A%0A++++return+0%3B%0A%7D'),l:'5',n:'0',o:'C%2B%2B+source+%231',t:'0')),k:50,l:'4',n:'0',o:'',s:0,t:'0'),(g:!((h:executor,i:(argsPanelShown:'1',compilationPanelShown:'0',compiler:g132,compilerName:'',compilerOutShown:'0',execArgs:'',execStdin:'',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,libs:!(),options:'',overrides:!(),source:1,stdinPanelShown:'1',tree:0,wrap:'1'),l:'5',n:'0',o:'Executor+x86-64+gcc+13.2+(C%2B%2B,+Editor+%231)',t:'0')),k:50,l:'4',n:'0',o:'',s:0,t:'0')),l:'2',n:'0',o:'',t:'0')),version:4
[ce_method]: https://godbolt.org/#g:!((g:!((g:!((h:codeEditor,i:(filename:'1',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,selection:(endColumn:24,endLineNumber:35,positionColumn:24,positionLineNumber:35,selectionStartColumn:24,selectionStartLineNumber:35,startColumn:24,startLineNumber:35),source:'%23include+%3Cstdint.h%3E+//+For+uint8_t%0A%0Aenum+Brand%0A%7B%0A++++Ford,%0A++++Lada,%0A++++Peugeot%0A%7D%3B%0A%0Aenum+Color%0A%7B%0A++++Red,+%0A++++Green,+%0A++++Blue%0A%7D%3B%0A%0Aclass+Car%0A%7B%0Aprivate:%0A++++enum+Brand+brand%3B%0A++++enum+Color+color%3B%0A++++uint8_t+speed%3B%0A%0Apublic:%0A++++void+accelerate()%0A++++%7B%0A++++++++speed+%2B%3D+1%3B%0A++++%7D%0A%7D%3B%0A%0Aint+main()%0A%7B%0A++++Car+myCar%3B%0A++++%0A++++myCar.accelerate()%3B%0A%0A++++return+0%3B%0A%7D'),l:'5',n:'0',o:'C%2B%2B+source+%231',t:'0')),k:50,l:'4',n:'0',o:'',s:0,t:'0'),(g:!((h:executor,i:(argsPanelShown:'1',compilationPanelShown:'0',compiler:g132,compilerName:'',compilerOutShown:'0',execArgs:'',execStdin:'',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,libs:!(),options:'',overrides:!(),source:1,stdinPanelShown:'1',tree:0,wrap:'1'),l:'5',n:'0',o:'Executor+x86-64+gcc+13.2+(C%2B%2B,+Editor+%231)',t:'0')),k:50,l:'4',n:'0',o:'',s:0,t:'0')),l:'2',n:'0',o:'',t:'0')),version:4
[ce_encap]: https://godbolt.org/#z:OYLghAFBqd5QCxAYwPYBMCmBRdBLAF1QCcAaPECAMzwBtMA7AQwFtMQByARg9KtQYEAysib0QXACx8BBAKoBnTAAUAHpwAMvAFYTStJg1DIApACYAQuYukl9ZATwDKjdAGFUtAK4sGIAMwAHKSuADJ4DJgAcj4ARpjEIGakAA6oCoRODB7evgHBaRmOAuGRMSzxicl2mA5ZQgRMxAQ5Pn5Btpj2xQwNTQSl0XEJSbaNza15HQrjAxFDFSNmAJS2qF7EyOwc5v4RyN5YANQm/m4z%2BIIAdAin2EcA9A9HAGIkR14RBIEA%2BgQmGgAgrt9odMCczk4ZsRMKw7o9nm9iEcLiAUOtBADgUDdlgaJEjgBZQEADR%2BUTkhJ%2BykBQiE2CiAHFsAAlIRHQJYrGMHxHCzEQzoLkAdisQKOEteJHQpCxkqOoSY6CYsvFkuUmC8wEwqH%2BOOFABFTmLsYCeSwjjkSCKTfKWZgZSc1RLGTDGKQnYD5RZvJgRUb/CasQcmAoFJamjasSliHgAG5MAjsOWS818gUMdBHWIZrOnA1S4hCwMpiVpq3ItC0d75o724u2yWfQS/AgolKYB0QgsaY2lo5fI4MHzKUNKIwJcO13slnFAlJeWK0PDIED9uOoPBZpjILb0AVJiDLfsmUX9%2BUKDtd6y1rh950nQ1ch%2BD7UEcqjsOMbXEBRHk9ng%2B8owgQGwMEOI5jt%2Bk73l6kqnkac5wRKG5biimDvpBX4Tr%2BECDj8w4sJ%2B44/gox4PqejbyhKeBUEcEAEVhJGThCbhHBoj5uKebGMURUE4VO/j3MSZIUlSNJ0gyzJsuRyHUcBGFgbB8rnpKhHEdBv7dkcvEaQJynwU%2B%2BoBkGQKDiwTARP%2B%2BpURGyIsAAnm4kaznJ/aOc5xBXDue4JImmD/q5KkPqi6JeG2pxcWcJxmGY5SVEcqB0Sk/GkSAMVmKxkVHB5TRXG%2BH6pZOR5ZdFoWuLQBkSrlXlKJhfHYaREBcBox5BZKoVoOFpVseYcXDMiSVHCljWTulfU9TlTl5QVTGaX%2ByyTeVmaVe11XTbVGGFaNuFcG1tmdesEVnNlfXxQkiXJUVv7jbFk01flW1zQJJWRdly3oKtpmmgpoHEOBM5BoaHCrLQnAAKy8H4HBaKQqCcFxljWCi6ybOCuw8KQBCaCDqwANYgODGj6JwkhQzjcOcLwCggMT2MwyDpBwLASCYKotThSQ5CUE0wAKKOkS0EICCoAA7tDmNoCwKR0ImWQC10wti9DsNSzL%2B4gMAXD%2BMkat0AkUSsNsvB6/uADy4VK%2BLFNs7UgLEHzVMhOzyANJcTv8IIIhiOwUgyIIigqOoDOkLoXD6IYxg3pY%2Bh4LENOQKsqApD0NMcLwqBxgksZYAnR5rBsWx6BcEQK0LIvW9wvAEG6tOkKLAopJwPCgxD5Mh/DHDYC7nPIqogQAGwALQD5IRzALuRza1cmUQIjVgx0cuCEDWZj%2BHt1c48s%2BOE8TYMcGTpAqxnTs03TW%2BtxwZjt7Dnebwz2%2BkFnv5ZCAkhAA
