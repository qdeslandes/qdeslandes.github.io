---
layout: page
title:  "OOP - Inheritance"
collection: cpp
published: true
---

While the previous lessons explained how to create a class, one of the core concepts of OOP hasn't been discussed yet: inheritance. That's what this lesson is about.

## Inheritance, in theory

Inheritance allows developers to define a class in terms of another class. That is, instead of creating a new class from scratch, we can make it inherit the properties and attributes of another class and continue from there.

The class we inherit from is called the **base** class (or parent class), while the new class is called the **derived** class (or child class). An object created from the base class has access to all the members of the base class, while an object created from the derived class has access to all the members of the base class and all the members of the derived class.

## Inheritance, in C++

### How to inherit?

To materialize inheritance between two classes, C++ uses the following syntax (see on [Compiler Explorer][ce_inheritance_in_cpp]):

```cpp
class Animal
{
private:
    size_t m_weight;

protected:
    size_t set_weight(size_t weight) { m_weight = weight; }

public:
    void get_weight() { return m_weight; }
};

class Dog: public Animal
{
public:
    void bark() { std::cout << "BARK!" << std::endl; }
};
```

The `Dog` class inherits from the `Animal` class; hence, an object of type `Dog` (i.e. `Dog mydog;`) is also of type `Animal`. This means we can do the following: `std::cout << myDog.get_weight() << std::endl;`. As described previously, because `Dog` inherits from `Animal`, `Dog` has access to the attributes and members of the `Animal` class.

Does it mean **all** the attributes and methods are accessible? Not really. If you remember about the visibility attributes from the previous lessons (i.e. `private`, `protected`, `public`), they affect what `Dog` can access to:
- `private`: members of `Animal` which are marked `private` are exclusive to `Animal`. They can't be accessed outside of the class, even by the derived classes.
- `protected`: members of `Animal` which are marked `protected` are exclusive to `Animal` and its derived classes, but can't be accessed from outside those. It's a way to share attributes and methods between a class and the classes that inherit from it without marking them `public`.
- `public`: attributes and methods marked as public can be accessed from the class, its derived classes, and from outside the object.

Continuing with the previous example (see on [Compiler Explorer][ce_visibility_attr]):

```cpp
class Dog: public Animal
{
public:
    Dog()
    {
        // Error! m_weight is private to Animal.
        m_weight = 3;

        // Ok, set_weight() is protected, so Dog can access it.
        set_weight(3);
    }
};

int main()
{
    Dog myDog;

    // Error! m_weight is private to Animal.
    std::cout << myDog.m_weight << std::endl;

    // Ok, get_weight() is public in Animal.
    std::cout << myDog.get_weight() << std::endl;

    // Error! set_weight() is protected, it can only be access by Animal and
    // its derived class.
    myDog.set_weight(30);

    // Ok, bark() is public in Dog.
    myDog.bark();
}
```

You probably also noticed the `public` specifier used when `Dog` inherits from `Animal` (i.e. `class Dog: public Animal`). This specific can be any valid [access specifier][access_spec_cppref] (`public`, `protected`, or `private`), and is used to override the visibility of the members of the base class:
- `public`: enforces all the members of the base class to be at least `public`  when inherited in the derived class.. Hence, this has no effect and doesn't alter the visiblity of the members.
- `protected`: enforces all the members of the base class to be at least `protected` when inherited in the derived class. The `public` members of `Animal` would become `protected`, while `protected` and `privated` members wouldn't be affected.
- `private`: enforces all the members of the base class to be at least `private` when inherited in the derived class. Hence, the `public` and `protected` members of `Animal` would become `private`, while existing `private` members wouldn't be affected.

The access specifier can be used to limit the amount of accessible inherited members. Using the previous example, let's assume `Dog` is defined as `class Dog: protected Animal`. This means the members of `Dog` inherited from `Animal` are **at least** `protected`, which will only affect `get_weight()` (see on [Compiler Explorer][ce_protected_inherits]):

```cpp
class Dog: protected Animal
{
};

int main()
{
    Dog myDog;

    // Error! Dog::get_weight() is protected!
    myDog.get_weight();

    Animal myAnimal;

    // Ok, Animal::get_weight() is public.
    myAnimal.get_weight();
}
```

### Overriding

Overriding is the ability for a derived class to replace a method from the base class. This is useful to implement a behavior that is specific to the base class:

```cpp
class Dog
{
public:
    void bark() { std::cout << "BARK!" << std::endl; }
};

class Chihuahua: public Dog
{
public:
    void bark() { std::cout << "bark!" << std::endl; }
};

int main()
{
    Dog myDog;
    myDog.bark();
    // Prints "BARK!

    Chihuahua myChihuahua;
    myChihuahua.bark();
    // Prints "bark!"
}
```

For an override to be valid, it needs to have the same name, return type, and parameters as the function it overrides. The following is not an override (see on [Compiler Explorer][ce_not_override]):

```cpp
class Dog
{
public:
    void bark() { std::cout << "BARK!" << std::endl; }
};

class Chihuahua: public Dog
{
public:
    void bark(bool twice)
    {
        std::cout << "bark!";
        if (twice)
            std::cout << "bark!";
        std::cout << std::endl;
    }
};
```

Because `Dog::bark()` and `Chihuahua::bark()` have different signatures, the latter is not an override of the former: it's a new method, specific to `Chihuahua`. However, because `Chihuahua::bark()` has the same name as `Dog::bark()`, it masks it, meaning `myChihuahua.bark()` is erroneous, because the only `bark()` method available to `myChihuahua` requires an argument.

However, `Dog::bark()` can be made available by adding `using Dog::bark;` in the `Chihuahua` class (see on [Compiler Explorer][ce_using_bark]):

```cpp
class Chihuahua: public Dog
{
public:
    using Dog::bark;

    void bark(bool twice)
    {
        std::cout << "bark!";
        if (twice)
            std::cout << "bark!";
        std::cout << std::endl;
    }
};

int main()
{
    Chihuahua myChihuahua;

    // Because of `using Dog::bark;` in `Chihuahua`, both are valid.
    myChihuahua.bark();
    myChihuahua.bark(true);
}
```

### Constructors and destructors

To conclude on a lighter note, let's see how the construction and destruction of inherited objects are performed. Let's use the following example (see on [Compiler Explorer][ce_ctor_dtor]):

```cpp
class A
{
public:
    A() { std::cout << "A::A()" << std::endl; }
    ~A() { std::cout << "A::~A()" << std::endl; }
};

class B: public A
{
public:
    B() { std::cout << "B::B()" << std::endl; }
    ~B() { std::cout << "B::~B()" << std::endl; }
};

int main()
{
    B b;
}
```

When `b` is created, the memory will first be allocated for an object of the size of `B`, then `A::A()` (`A`'s constructor) will be called, then `B::B()`. The opposite process is performed during destruction (`B::~B()` then `A::~A()`).

[access_spec_cppref]: https://en.cppreference.com/w/cpp/language/access

[ce_inheritance_in_cpp]: https://godbolt.org/#g:!((g:!((g:!((h:codeEditor,i:(filename:'1',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,selection:(endColumn:11,endLineNumber:13,positionColumn:11,positionLineNumber:13,selectionStartColumn:11,selectionStartLineNumber:13,startColumn:11,startLineNumber:13),source:'%23include+%3Ciostream%3E%0A%23include+%3Ccstdint%3E%0A%0Aclass+Animal%0A%7B%0Aprivate:%0A++++size_t+m_weight%3B%0A%0Aprotected:%0A++++void+set_weight(size_t+weight)+%7B+m_weight+%3D+weight%3B+%7D%0A%0Apublic:%0A++++size_t+get_weight()+%7B+return+m_weight%3B+%7D%0A%7D%3B%0A%0Aclass+Dog:+public+Animal%0A%7B%0Apublic:%0A++++void+bark()+%7B+std::cout+%3C%3C+%22BARK!!%22+%3C%3C+std::endl%3B+%7D%0A%7D%3B%0A%0Aint+main()%0A%7B%0A++++Dog+myDog%3B%0A%0A++++std::cout+%3C%3C+myDog.get_weight()+%3C%3C+std::endl%3B%0A%0A++++return+0%3B%0A%7D'),l:'5',n:'0',o:'C%2B%2B+source+%231',t:'0')),k:59.6078431372549,l:'4',n:'0',o:'',s:0,t:'0'),(g:!((h:executor,i:(argsPanelShown:'1',compilationPanelShown:'0',compiler:g132,compilerName:'',compilerOutShown:'0',execArgs:'',execStdin:'',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,libs:!(),options:'-O0',overrides:!(),runtimeTools:!(),source:1,stdinPanelShown:'1',tree:0,wrap:'1'),l:'5',n:'0',o:'Executor+x86-64+gcc+13.2+(C%2B%2B,+Editor+%231)',t:'0')),k:40.3921568627451,l:'4',n:'0',o:'',s:0,t:'0')),l:'2',n:'0',o:'',t:'0')),version:4
[ce_visibility_attr]: https://godbolt.org/#g:!((g:!((g:!((h:codeEditor,i:(filename:'1',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,selection:(endColumn:5,endLineNumber:27,positionColumn:5,positionLineNumber:27,selectionStartColumn:5,selectionStartLineNumber:27,startColumn:5,startLineNumber:27),source:'%23include+%3Ciostream%3E%0A%23include+%3Ccstdint%3E%0A%0Aclass+Animal%0A%7B%0Aprivate:%0A++++size_t+m_weight%3B%0A%0Aprotected:%0A++++void+set_weight(size_t+weight)+%7B+m_weight+%3D+weight%3B+%7D%0A%0Apublic:%0A++++size_t+get_weight()+%7B+return+m_weight%3B+%7D%0A%7D%3B%0A%0Aclass+Dog:+public+Animal%0A%7B%0Apublic:%0A++++Dog()%0A++++%7B%0A++++++++//+Error!!+m_weight+is+private+to+Animal.%0A++++++++m_weight+%3D+3%3B%0A%0A++++++++//+Ok,+set_weight()+is+protected,+so+Dog+can+access+it.%0A++++++++set_weight(3)%3B%0A++++%7D%0A++++%0A++++void+bark()+%7B+std::cout+%3C%3C+%22BARK!!%22+%3C%3C+std::endl%3B+%7D%0A%7D%3B%0A%0Aint+main()%0A%7B%0A++++Dog+myDog%3B%0A%0A++++//+Error!!+m_weight+is+private+to+Animal.%0A++++std::cout+%3C%3C+myDog.m_weight+%3C%3C+std::endl%3B%0A%0A++++//+Ok,+get_weight()+is+public+in+Animal.%0A++++std::cout+%3C%3C+myDog.get_weight()+%3C%3C+std::endl%3B%0A%0A++++//+Error!!+set_weight()+is+protected,+it+can+only+be+access+by+Animal+and%0A++++//+its+derived+class.%0A++++myDog.set_weight(30)%3B%0A%0A++++//+Ok,+bark()+is+public+in+Dog.%0A++++myDog.bark()%3B%0A%7D'),l:'5',n:'0',o:'C%2B%2B+source+%231',t:'0')),k:59.6078431372549,l:'4',n:'0',o:'',s:0,t:'0'),(g:!((h:executor,i:(argsPanelShown:'1',compilationPanelShown:'0',compiler:g132,compilerName:'',compilerOutShown:'0',execArgs:'',execStdin:'',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,libs:!(),options:'-O0',overrides:!(),runtimeTools:!(),source:1,stdinPanelShown:'1',tree:0,wrap:'1'),l:'5',n:'0',o:'Executor+x86-64+gcc+13.2+(C%2B%2B,+Editor+%231)',t:'0')),k:40.3921568627451,l:'4',n:'0',o:'',s:0,t:'0')),l:'2',n:'0',o:'',t:'0')),version:4
[ce_protected_inherits]: https://godbolt.org/#g:!((g:!((g:!((h:codeEditor,i:(filename:'1',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,selection:(endColumn:24,endLineNumber:25,positionColumn:24,positionLineNumber:25,selectionStartColumn:24,selectionStartLineNumber:25,startColumn:24,startLineNumber:25),source:'%23include+%3Ciostream%3E%0A%23include+%3Ccstdint%3E%0A%0Aclass+Animal%0A%7B%0Aprivate:%0A++++size_t+m_weight%3B%0A%0Aprotected:%0A++++void+set_weight(size_t+weight)+%7B+m_weight+%3D+weight%3B+%7D%0A%0Apublic:%0A++++size_t+get_weight()+%7B+return+m_weight%3B+%7D%0A%7D%3B%0A%0Aclass+Dog:+protected+Animal%0A%7B%0A%7D%3B%0A%0Aint+main()%0A%7B%0A++++Dog+myDog%3B%0A%0A++++//+Error!!+Dog::get_weight()+is+protected!!%0A++++myDog.get_weight()%3B%0A%0A++++Animal+myAnimal%3B%0A%0A++++//+Ok,+Animal::get_weight()+is+public.%0A++++myAnimal.get_weight()%3B%0A%7D'),l:'5',n:'0',o:'C%2B%2B+source+%231',t:'0')),k:59.6078431372549,l:'4',n:'0',o:'',s:0,t:'0'),(g:!((h:executor,i:(argsPanelShown:'1',compilationPanelShown:'0',compiler:g132,compilerName:'',compilerOutShown:'0',execArgs:'',execStdin:'',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,libs:!(),options:'-O0',overrides:!(),runtimeTools:!(),source:1,stdinPanelShown:'1',tree:0,wrap:'1'),l:'5',n:'0',o:'Executor+x86-64+gcc+13.2+(C%2B%2B,+Editor+%231)',t:'0')),k:40.3921568627451,l:'4',n:'0',o:'',s:0,t:'0')),l:'2',n:'0',o:'',t:'0')),version:4
[ce_not_override]: https://godbolt.org/#g:!((g:!((g:!((h:codeEditor,i:(filename:'1',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,selection:(endColumn:3,endLineNumber:7,positionColumn:3,positionLineNumber:7,selectionStartColumn:3,selectionStartLineNumber:7,startColumn:3,startLineNumber:7),source:'%23include+%3Ciostream%3E%0A%0Aclass+Dog%0A%7B%0Apublic:%0A++++void+bark()+%7B+std::cout+%3C%3C+%22BARK!!%22+%3C%3C+std::endl%3B+%7D%0A%7D%3B%0A%0Aclass+Chihuahua:+public+Dog%0A%7B%0Apublic:%0A++++void+bark()+%7B+std::cout+%3C%3C+%22bark!!%22+%3C%3C+std::endl%3B+%7D%0A%7D%3B%0A%0Aint+main()%0A%7B%0A++++Dog+myDog%3B%0A++++myDog.bark()%3B%0A++++//+Prints+%22BARK!!%0A%0A++++Chihuahua+myChihuahua%3B%0A++++myChihuahua.bark()%3B%0A++++//+Prints+%22bark!!%22%0A%7D'),l:'5',n:'0',o:'C%2B%2B+source+%231',t:'0')),k:59.6078431372549,l:'4',n:'0',o:'',s:0,t:'0'),(g:!((h:executor,i:(argsPanelShown:'1',compilationPanelShown:'0',compiler:g132,compilerName:'',compilerOutShown:'0',execArgs:'',execStdin:'',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,libs:!(),options:'-O0',overrides:!(),runtimeTools:!(),source:1,stdinPanelShown:'1',tree:0,wrap:'1'),l:'5',n:'0',o:'Executor+x86-64+gcc+13.2+(C%2B%2B,+Editor+%231)',t:'0')),k:40.3921568627451,l:'4',n:'0',o:'',s:0,t:'0')),l:'2',n:'0',o:'',t:'0')),version:4
[ce_using_bark]: https://godbolt.org/#g:!((g:!((g:!((h:codeEditor,i:(filename:'1',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,selection:(endColumn:2,endLineNumber:30,positionColumn:2,positionLineNumber:30,selectionStartColumn:2,selectionStartLineNumber:30,startColumn:2,startLineNumber:30),source:'%23include+%3Ciostream%3E%0A%0Aclass+Dog%0A%7B%0Apublic:%0A++++void+bark()+%7B+std::cout+%3C%3C+%22BARK!!%22+%3C%3C+std::endl%3B+%7D%0A%7D%3B%0A%0Aclass+Chihuahua:+public+Dog%0A%7B%0Apublic:%0A++++using+Dog::bark%3B%0A%0A++++void+bark(bool+twice)%0A++++%7B%0A++++++++std::cout+%3C%3C+%22bark!!%22%3B%0A++++++++if+(twice)%0A++++++++++++std::cout+%3C%3C+%22bark!!%22%3B%0A++++++++std::cout+%3C%3C+std::endl%3B%0A++++%7D%0A%7D%3B%0A%0Aint+main()%0A%7B%0A++++Chihuahua+myChihuahua%3B%0A%0A++++//+Because+of+%60using+Dog::bark%3B%60+in+%60Chihuahua%60,+both+are+valid.%0A++++myChihuahua.bark()%3B%0A++++myChihuahua.bark(true)%3B%0A%7D'),l:'5',n:'0',o:'C%2B%2B+source+%231',t:'0')),k:59.6078431372549,l:'4',n:'0',o:'',s:0,t:'0'),(g:!((h:executor,i:(argsPanelShown:'1',compilationPanelShown:'0',compiler:g132,compilerName:'',compilerOutShown:'0',execArgs:'',execStdin:'',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,libs:!(),options:'-O0',overrides:!(),runtimeTools:!(),source:1,stdinPanelShown:'1',tree:0,wrap:'1'),l:'5',n:'0',o:'Executor+x86-64+gcc+13.2+(C%2B%2B,+Editor+%231)',t:'0')),k:40.3921568627451,l:'4',n:'0',o:'',s:0,t:'0')),l:'2',n:'0',o:'',t:'0')),version:4
[ce_ctor_dtor]: https://godbolt.org/#g:!((g:!((g:!((h:codeEditor,i:(filename:'1',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,selection:(endColumn:2,endLineNumber:20,positionColumn:2,positionLineNumber:20,selectionStartColumn:2,selectionStartLineNumber:20,startColumn:2,startLineNumber:20),source:'%23include+%3Ciostream%3E%0A%0Aclass+A%0A%7B%0Apublic:%0A++++A()+%7B+std::cout+%3C%3C+%22A::A()%22+%3C%3C+std::endl%3B+%7D%0A++++~A()+%7B+std::cout+%3C%3C+%22A::~A()%22+%3C%3C+std::endl%3B+%7D%0A%7D%3B%0A%0Aclass+B:+public+A%0A%7B%0Apublic:%0A++++B()+%7B+std::cout+%3C%3C+%22B::B()%22+%3C%3C+std::endl%3B+%7D%0A++++~B()+%7B+std::cout+%3C%3C+%22B::~B()%22+%3C%3C+std::endl%3B+%7D%0A%7D%3B%0A%0Aint+main()%0A%7B%0A++++B+b%3B%0A%7D'),l:'5',n:'0',o:'C%2B%2B+source+%231',t:'0')),k:59.6078431372549,l:'4',n:'0',o:'',s:0,t:'0'),(g:!((h:executor,i:(argsPanelShown:'1',compilationPanelShown:'0',compiler:g132,compilerName:'',compilerOutShown:'0',execArgs:'',execStdin:'',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,libs:!(),options:'-O0',overrides:!(),runtimeTools:!(),source:1,stdinPanelShown:'1',tree:0,wrap:'1'),l:'5',n:'0',o:'Executor+x86-64+gcc+13.2+(C%2B%2B,+Editor+%231)',t:'0')),k:40.3921568627451,l:'4',n:'0',o:'',s:0,t:'0')),l:'2',n:'0',o:'',t:'0')),version:4
