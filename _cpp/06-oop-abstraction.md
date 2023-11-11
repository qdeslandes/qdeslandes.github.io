---
layout: page
title:  "OOP - Abstraction"
collection: cpp
published: true
---

## Virtual functions

Let's start with an example (see on [Compiler Explorer][ce_no_virtual]):

```cpp
class Language
{
public:
    void greet() { std::cout << "Hello!" << std::endl; }
};

class German: public Language
{
public:
    void greet() { std::cout << "Guten tag!" << std::endl; }
};

class Spanish: public Language
{
public:
    void greet() { std::cout << "Hola!" << std::endl; }
};

void do_greet(Language &l)
{
    l.greet();
}

int main()
{
    Language l;
    German g;
    Spanish s;

    do_greet(l);
    do_greet(g);
    do_greet(s);

    return 0;
}
```

What can we expect in this situation? We expect each language object's `greet()` method to be called, and to successively print `Hello!`, `Guten tag!`, and `Hola!`, right? However, the actual result is slightly different: we only have `Hello!`, `Hello!`, and `Hello!`... why is that?!

Firstly, let's explain why our compiler is happy with this code: the free function `do_greet()` expects a reference to a `Language` object. In C++, any object can be used as a reference or a pointer to one of its base class. That is, `German g;` can be passed as a `Language &` or `Language *` to a function, because `Language` is a base class of `German`.

Now, the resolution of the `greet()` method used in the `do_greet()` free function is done at compile time. Our compiler notices that `do_greet()` receives a `Language &` object, so `Language::greet()` is called. The issue is that our objects are actually a derived class of `Language`, and we want their override of `greet()` to be called. How can we do that?

To have the expected behavior, we need what is called "dynamic dispatch", which is the runtime resolution of a method call. This is supported in C++ using the [`virtual`][virtual_cppref] keyword.

Marking a function as `virtual` notifies the compiler that this function can be overridden in derived classes, and that the lookup for the actual function should be performed at runtime. Methods in the base class should then be marked as `virtual`, while their overriding counterparts in the derived class should be marked as [`override`][override_cppref]. `override` is optional, but you should use it, because it will trigger a compiler warning if the overridden function is not `virtual`. This is extremely useful to ensure you will receive the expected behavior from your override (see on [Compiler Explorer][ce_virtual]):

```cpp
class Language
{
public:
    virtual void greet() { std::cout << "Hello!" << std::endl; }
};

class German: public Language
{
public:
    void greet() override { std::cout << "Guten tag!" << std::endl; }
};

class Spanish: public Language
{
public:
    void greet() override { std::cout << "Hola!" << std::endl; }
};
```

You can now witness the expected behavior: `Hello!`, `Guten tag!`, and `Hola!`. For the sake of completeness, here's the compiler output if `Language::greet()` is not marked as `virtual`, but `German::greet()` and `Spanish::greet()` are marked as `override` (see on [Compiler Explorer][ce_override_no_virtual]):

```
main.cc:12:10: error: ‘void German::greet()’ marked ‘override’, but does not override
   12 |     void greet() override { std::cout << "Guten tag!" << std::endl; }
      |          ^~~~~
main.cc:18:10: error: ‘void Spanish::greet()’ marked ‘override’, but does not override
   18 |     void greet() override { std::cout << "Hola!" << std::endl; }
      |          ^~~~~
```

See how useful it is to mark your overrides as `override`? Thanks to this, I know that `German::greet()` and `Spanish::greet()` won't be called if my `German` object or `Spanish` object are passed as a reference or pointer to `Language`.

## Virtual destructors

The previous example can be extrapolated to destructors too (see on [Compiler Explorer][ce_no_virtual_dtor]):

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
    A *a = new B();
    delete a;
}
```

The example above dynamically creates an object of type `B`, and stores it as a pointer to an object of type `A`. This is valid, as mentioned above: an object of a derived class can be manipulated as a pointer or reference to its base class.

As expected, the constructor of `a` will print `A::A()` then `B::B()`, but its destruction (i.e. `delete a`) only prints `A::~A()`. This means our object is only partially destroyed, and resources allocated by `B` (if any) are not freed!

Thankfully, this can be solved by marking `A`'s destructor as virtual (see on [Compiler Explorer][ce_virtual_dtor]):
```cpp
class A
{
public:
    virtual ~A() { std::cout << "A::~A()" << std::endl; }
};
```

## Pure virtual methods, abstract classes, and final qualifier

A base class can also define a `virtual` method without any implementation, these are called pure virtual methods. Any class containing at least one pure virtual method is called an [abstract class][abstract_class_cppref] and can't be instantiated (see on [Compiler Explorer][ce_abstract_class]):

```cpp
class A
{
public:
    // "= 0" marks the method as pure virtual. It has no implementation
    // in A.
    virtual void greet() = 0;
};

class B: public A
{
public:
    void greet() override { std::cout << "Hello" << std::endl; }
};

int main()
{
    // Error! A::greet() is a pure virtual method, making A an abstract
    // class, so it can't be instanciated!
    A a;

    // Ok, B implements greet(), so B is not an abstract class.
    B b;
}
```

Abstract classes are useful to define a common interface between different classes, so generic methods can be called on functions relying on this interface.

### The final qualifier

Finally, the [`final` qualifier][final_cppref] can be used to prevent a method from being overridden further (see on [Compiler Explorer][ce_final]):

```cpp
class A
{
public:
    virtual void greet() = 0;
};

class B: public A
{
public:
    void greet() override final
    {
        std::cout << "Hello!" << std::endl;
    }
};

class C: public B
{
public:
    // Error! B::greet() is final, so it can't be overridden by
    // classes inheriting from B!
    void greet() override {}
};
```

[virtual_cppref]: https://en.cppreference.com/w/cpp/language/virtual
[override_cppref]: https://en.cppreference.com/w/cpp/language/override
[abstract_class_cppref]: https://en.cppreference.com/w/cpp/language/abstract_class
[final_cppref]: https://en.cppreference.com/w/cpp/language/final

[ce_no_virtual]: https://godbolt.org/#g:!((g:!((g:!((h:codeEditor,i:(filename:'1',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,selection:(endColumn:2,endLineNumber:37,positionColumn:2,positionLineNumber:37,selectionStartColumn:2,selectionStartLineNumber:37,startColumn:2,startLineNumber:37),source:'%23include+%3Ciostream%3E%0A%0Aclass+Language%0A%7B%0Apublic:%0A++++void+greet()+%7B+std::cout+%3C%3C+%22Hello!!%22+%3C%3C+std::endl%3B+%7D%0A%7D%3B%0A%0Aclass+German:+public+Language%0A%7B%0Apublic:%0A++++void+greet()+%7B+std::cout+%3C%3C+%22Guten+tag!!%22+%3C%3C+std::endl%3B+%7D%0A%7D%3B%0A%0Aclass+Spanish:+public+Language%0A%7B%0Apublic:%0A++++void+greet()+%7B+std::cout+%3C%3C+%22Hola!!%22+%3C%3C+std::endl%3B+%7D%0A%7D%3B%0A%0Avoid+do_greet(Language+%26l)%0A%7B%0A++++l.greet()%3B%0A%7D%0A%0Aint+main()%0A%7B%0A++++Language+l%3B%0A++++German+g%3B%0A++++Spanish+s%3B%0A%0A++++do_greet(l)%3B%0A++++do_greet(g)%3B%0A++++do_greet(s)%3B%0A%0A++++return+0%3B%0A%7D'),l:'5',n:'0',o:'C%2B%2B+source+%231',t:'0')),k:59.6078431372549,l:'4',n:'0',o:'',s:0,t:'0'),(g:!((h:executor,i:(argsPanelShown:'1',compilationPanelShown:'0',compiler:g132,compilerName:'',compilerOutShown:'0',execArgs:'',execStdin:'',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,libs:!(),options:'-O0',overrides:!(),runtimeTools:!(),source:1,stdinPanelShown:'1',tree:0,wrap:'1'),l:'5',n:'0',o:'Executor+x86-64+gcc+13.2+(C%2B%2B,+Editor+%231)',t:'0')),k:40.3921568627451,l:'4',n:'0',o:'',s:0,t:'0')),l:'2',n:'0',o:'',t:'0')),version:4
[ce_virtual]: https://godbolt.org/#g:!((g:!((g:!((h:codeEditor,i:(filename:'1',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,selection:(endColumn:5,endLineNumber:12,positionColumn:5,positionLineNumber:12,selectionStartColumn:5,selectionStartLineNumber:12,startColumn:5,startLineNumber:12),source:'%23include+%3Ciostream%3E%0A%0Aclass+Language%0A%7B%0Apublic:%0A++++virtual+void+greet()+%7B+std::cout+%3C%3C+%22Hello!!%22+%3C%3C+std::endl%3B+%7D%0A%7D%3B%0A%0Aclass+German:+public+Language%0A%7B%0Apublic:%0A++++void+greet()+override+%7B+std::cout+%3C%3C+%22Guten+tag!!%22+%3C%3C+std::endl%3B+%7D%0A%7D%3B%0A%0Aclass+Spanish:+public+Language%0A%7B%0Apublic:%0A++++void+greet()+override+%7B+std::cout+%3C%3C+%22Hola!!%22+%3C%3C+std::endl%3B+%7D%0A%7D%3B%0A%0Avoid+do_greet(Language+%26l)%0A%7B%0A++++l.greet()%3B%0A%7D%0A%0Aint+main()%0A%7B%0A++++Language+l%3B%0A++++German+g%3B%0A++++Spanish+s%3B%0A%0A++++do_greet(l)%3B%0A++++do_greet(g)%3B%0A++++do_greet(s)%3B%0A%0A++++return+0%3B%0A%7D'),l:'5',n:'0',o:'C%2B%2B+source+%231',t:'0')),k:59.6078431372549,l:'4',n:'0',o:'',s:0,t:'0'),(g:!((h:executor,i:(argsPanelShown:'1',compilationPanelShown:'0',compiler:g132,compilerName:'',compilerOutShown:'0',execArgs:'',execStdin:'',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,libs:!(),options:'-O0',overrides:!(),runtimeTools:!(),source:1,stdinPanelShown:'1',tree:0,wrap:'1'),l:'5',n:'0',o:'Executor+x86-64+gcc+13.2+(C%2B%2B,+Editor+%231)',t:'0')),k:40.3921568627451,l:'4',n:'0',o:'',s:0,t:'0')),l:'2',n:'0',o:'',t:'0')),version:4
[ce_override_no_virtual]: https://godbolt.org/#g:!((g:!((g:!((h:codeEditor,i:(filename:'1',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,selection:(endColumn:5,endLineNumber:6,positionColumn:5,positionLineNumber:6,selectionStartColumn:5,selectionStartLineNumber:6,startColumn:5,startLineNumber:6),source:'%23include+%3Ciostream%3E%0A%0Aclass+Language%0A%7B%0Apublic:%0A++++void+greet()+%7B+std::cout+%3C%3C+%22Hello!!%22+%3C%3C+std::endl%3B+%7D%0A%7D%3B%0A%0Aclass+German:+public+Language%0A%7B%0Apublic:%0A++++void+greet()+override+%7B+std::cout+%3C%3C+%22Guten+tag!!%22+%3C%3C+std::endl%3B+%7D%0A%7D%3B%0A%0Aclass+Spanish:+public+Language%0A%7B%0Apublic:%0A++++void+greet()+override+%7B+std::cout+%3C%3C+%22Hola!!%22+%3C%3C+std::endl%3B+%7D%0A%7D%3B%0A%0Avoid+do_greet(Language+%26l)%0A%7B%0A++++l.greet()%3B%0A%7D%0A%0Aint+main()%0A%7B%0A++++Language+l%3B%0A++++German+g%3B%0A++++Spanish+s%3B%0A%0A++++do_greet(l)%3B%0A++++do_greet(g)%3B%0A++++do_greet(s)%3B%0A%0A++++return+0%3B%0A%7D'),l:'5',n:'0',o:'C%2B%2B+source+%231',t:'0')),k:59.6078431372549,l:'4',n:'0',o:'',s:0,t:'0'),(g:!((h:executor,i:(argsPanelShown:'1',compilationPanelShown:'0',compiler:g132,compilerName:'',compilerOutShown:'0',execArgs:'',execStdin:'',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,libs:!(),options:'-O0',overrides:!(),runtimeTools:!(),source:1,stdinPanelShown:'1',tree:0,wrap:'1'),l:'5',n:'0',o:'Executor+x86-64+gcc+13.2+(C%2B%2B,+Editor+%231)',t:'0')),k:40.3921568627451,l:'4',n:'0',o:'',s:0,t:'0')),l:'2',n:'0',o:'',t:'0')),version:4
[ce_no_virtual_dtor]: https://godbolt.org/#g:!((g:!((g:!((h:codeEditor,i:(filename:'1',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,selection:(endColumn:2,endLineNumber:21,positionColumn:2,positionLineNumber:21,selectionStartColumn:2,selectionStartLineNumber:21,startColumn:2,startLineNumber:21),source:'%23include+%3Ciostream%3E%0A%0Aclass+A%0A%7B%0Apublic:%0A++++A()+%7B+std::cout+%3C%3C+%22A::A()%22+%3C%3C+std::endl%3B+%7D%0A++++~A()+%7B+std::cout+%3C%3C+%22A::~A()%22+%3C%3C+std::endl%3B+%7D%0A%7D%3B%0A%0Aclass+B:+public+A%0A%7B%0Apublic:%0A++++B()+%7B+std::cout+%3C%3C+%22B::B()%22+%3C%3C+std::endl%3B+%7D%0A++++~B()+%7B+std::cout+%3C%3C+%22B::~B()%22+%3C%3C+std::endl%3B+%7D%0A%7D%3B%0A%0Aint+main()%0A%7B%0A++++A+*a+%3D+new+B()%3B%0A++++delete+a%3B%0A%7D'),l:'5',n:'0',o:'C%2B%2B+source+%231',t:'0')),k:59.6078431372549,l:'4',n:'0',o:'',s:0,t:'0'),(g:!((h:executor,i:(argsPanelShown:'1',compilationPanelShown:'0',compiler:g132,compilerName:'',compilerOutShown:'0',execArgs:'',execStdin:'',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,libs:!(),options:'-O0',overrides:!(),runtimeTools:!(),source:1,stdinPanelShown:'1',tree:0,wrap:'1'),l:'5',n:'0',o:'Executor+x86-64+gcc+13.2+(C%2B%2B,+Editor+%231)',t:'0')),k:40.3921568627451,l:'4',n:'0',o:'',s:0,t:'0')),l:'2',n:'0',o:'',t:'0')),version:4
[ce_virtual_dtor]: https://godbolt.org/#g:!((g:!((g:!((h:codeEditor,i:(filename:'1',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,selection:(endColumn:13,endLineNumber:7,positionColumn:13,positionLineNumber:7,selectionStartColumn:13,selectionStartLineNumber:7,startColumn:13,startLineNumber:7),source:'%23include+%3Ciostream%3E%0A%0Aclass+A%0A%7B%0Apublic:%0A++++A()+%7B+std::cout+%3C%3C+%22A::A()%22+%3C%3C+std::endl%3B+%7D%0A++++virtual+~A()+%7B+std::cout+%3C%3C+%22A::~A()%22+%3C%3C+std::endl%3B+%7D%0A%7D%3B%0A%0Aclass+B:+public+A%0A%7B%0Apublic:%0A++++B()+%7B+std::cout+%3C%3C+%22B::B()%22+%3C%3C+std::endl%3B+%7D%0A++++~B()+%7B+std::cout+%3C%3C+%22B::~B()%22+%3C%3C+std::endl%3B+%7D%0A%7D%3B%0A%0Aint+main()%0A%7B%0A++++A+*a+%3D+new+B()%3B%0A++++delete+a%3B%0A%7D'),l:'5',n:'0',o:'C%2B%2B+source+%231',t:'0')),k:59.6078431372549,l:'4',n:'0',o:'',s:0,t:'0'),(g:!((h:executor,i:(argsPanelShown:'1',compilationPanelShown:'0',compiler:g132,compilerName:'',compilerOutShown:'0',execArgs:'',execStdin:'',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,libs:!(),options:'-O0',overrides:!(),runtimeTools:!(),source:1,stdinPanelShown:'1',tree:0,wrap:'1'),l:'5',n:'0',o:'Executor+x86-64+gcc+13.2+(C%2B%2B,+Editor+%231)',t:'0')),k:40.3921568627451,l:'4',n:'0',o:'',s:0,t:'0')),l:'2',n:'0',o:'',t:'0')),version:4
[ce_abstract_class]: https://godbolt.org/#g:!((g:!((g:!((h:codeEditor,i:(filename:'1',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,selection:(endColumn:1,endLineNumber:3,positionColumn:1,positionLineNumber:3,selectionStartColumn:1,selectionStartLineNumber:3,startColumn:1,startLineNumber:3),source:'%23include+%3Ciostream%3E%0A%0Aclass+A%0A%7B%0Apublic:%0A++++//+%22%3D+0%22+marks+the+method+as+pure+virtual.+It+has+no+implementation%0A++++//+in+A.%0A++++virtual+void+greet()+%3D+0%3B%0A%7D%3B%0A%0Aclass+B:+public+A%0A%7B%0Apublic:%0A++++void+greet()+override+%7B+std::cout+%3C%3C+%22Hello%22+%3C%3C+std::endl%3B+%7D%0A%7D%3B%0A%0Aint+main()%0A%7B%0A++++//+Error!!+A::greet()+is+a+pure+virtual+method,+making+A+an+abstract%0A++++//+class,+so+it+can!'t+be+instanciated!!%0A++++A+a%3B%0A%0A++++//+Ok,+B+implements+greet(),+so+B+is+not+an+abstract+class.%0A++++B+b%3B%0A%7D'),l:'5',n:'0',o:'C%2B%2B+source+%231',t:'0')),k:59.6078431372549,l:'4',n:'0',o:'',s:0,t:'0'),(g:!((h:executor,i:(argsPanelShown:'1',compilationPanelShown:'0',compiler:g132,compilerName:'',compilerOutShown:'0',execArgs:'',execStdin:'',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,libs:!(),options:'-O0',overrides:!(),runtimeTools:!(),source:1,stdinPanelShown:'1',tree:0,wrap:'1'),l:'5',n:'0',o:'Executor+x86-64+gcc+13.2+(C%2B%2B,+Editor+%231)',t:'0')),k:40.3921568627451,l:'4',n:'0',o:'',s:0,t:'0')),l:'2',n:'0',o:'',t:'0')),version:4
[ce_final]: https://godbolt.org/#g:!((g:!((g:!((h:codeEditor,i:(filename:'1',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,selection:(endColumn:58,endLineNumber:21,positionColumn:58,positionLineNumber:21,selectionStartColumn:58,selectionStartLineNumber:21,startColumn:58,startLineNumber:21),source:'%23include+%3Ciostream%3E%0A%0Aclass+A%0A%7B%0Apublic:%0A++++virtual+void+greet()+%3D+0%3B%0A%7D%3B%0A%0Aclass+B:+public+A%0A%7B%0Apublic:%0A++++void+greet()+override+final%0A++++%7B%0A++++++++std::cout+%3C%3C+%22Hello!!%22+%3C%3C+std::endl%3B%0A++++%7D%0A%7D%3B%0A%0Aclass+C:+public+B%0A%7B%0Apublic:%0A++++//+Error!!+B::greet()+is+final,+so+it+can!'t+be+overridden+by%0A++++//+classes+inheriting+from+B!!%0A++++void+greet()+override+%7B%7D%0A%7D%3B'),l:'5',n:'0',o:'C%2B%2B+source+%231',t:'0')),k:59.6078431372549,l:'4',n:'0',o:'',s:0,t:'0'),(g:!((h:executor,i:(argsPanelShown:'1',compilationPanelShown:'0',compiler:g132,compilerName:'',compilerOutShown:'0',execArgs:'',execStdin:'',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,libs:!(),options:'-O0',overrides:!(),runtimeTools:!(),source:1,stdinPanelShown:'1',tree:0,wrap:'1'),l:'5',n:'0',o:'Executor+x86-64+gcc+13.2+(C%2B%2B,+Editor+%231)',t:'0')),k:40.3921568627451,l:'4',n:'0',o:'',s:0,t:'0')),l:'2',n:'0',o:'',t:'0')),version:4
