---
layout: page
title:  "OOP - Constructors and destructors"
collection: cpp
published: false
---

As previously discussed, classes are defined using the [`class`][class_keyword_cppref] keyword in C++, followed by their name and the members specifications.

While we've defined the whole class in a source file (`.cpp`) so far, it's not mandatory. We could declare the class within a header (`.h`) file, so it can be included and used elsewhere, then implement some of the methods within the header file and others in a source file.

## (Default) constructor

A constructor is a special member of a class that is automatically called when an object of that class is created. Constructors are used to initialize the object's data members and perform necessary setup tasks. They have the same name as the class and no return type. Constructors can be overloaded (we can define more than one constructor for a class).

Let's define a `Log` class, which will be used to store and process log messages in our application (see on [Compiler Explorer][ce_ctor]):
```cpp
class Log
{
private:
    uint8_t m_severity;
    const char *m_msg;

public:
    Log(const char *msg, uint8_t severity = 0):
        m_severity(severity)
    {
        m_msg = msg ? strdup(msg) : nullptr;
    }

    void print()
    {
        std::cout << (void *)m_msg << ": " << m_msg << std::endl;
    }
};

int main()
{
    Log l("Hello, world!");
    l.print();
}
```

In `main()`, when we declare `Log l;`, the compiler will reserve enough space on the stack for our object to fit, then call `Log(const char *msg, uint8_t severity = 0)` to initialize it.

Our constructor, `Log(const char *msg, uint8_t severity = 0)` will initialise both `m_severity` and `m_msg` in two different ways:
- `m_severity` is initialized through an initializer list. Following the constructor's prototype, you can list the attributes of your object and initialize them there. Your constructor's parameters are also available.
- `m_msg` is initialized within the body of the constructor. This is used when attribute initialization depends on more complex computation (which is not the case here). As `strdup()` doesn't allow `NULL` argumentz, we need to check whether `msg` is `NULL` before calling it.

### Default constructor

The following statement will trigger a compiler error (see on [Compiler Explorer][ce_def_ctor_missing]):
```cpp
int main()
{
    Log l;
}
```

As you can see in the compiler output, "no matching function for call to 'Log::Log()'". Our compiler can't find a function matching the signature `Log::Log()`, which is correct because the only one we have is `Log(const char *, uint8_t)`!

When declaring an object, its constructor will be called to initialize it, even if you don't provide any arguments. Hence, our compiler can't find the constructor for `Log` which doesn't require any parameters. This kind of constructor is called the default constructor. We can either define it ourselves (`Log::Log()`) or modify the existing constructor, so it can be called without any argument (`Log(const char *msg = nullptr, uint8_t severity = 0)`).

> **I've tried defining a class without any constructor, and it works. Why?**
>
> Your compiler is able to generate the default constructor by itself in some cases. However, if you define a constructor, then the compiler won't generate the default constructor for you, as it assumes the construction of your object is non-trivial.

## Destructor

A destructor is a special member function of a class that is called automatically when an object of that class is about to be destroyed, typically when it goes out of scope. The destructor is responsible for cleaning up resources and performing any necessary cleanup tasks, such as releasing memory or closing files. They have the same name as the class but are prefixed with `~` and have neither parameters nor a return type.

If you do not define a destructor, your compiler will generate a default one for you with an empty body (it won't do anything).

In our `Log` class, we dynamically allocate memory (using `strdup`), meaning we are responsible for freeing it. We can't rely on our compiler generating a default empty destructor, so let's write one (see on [Compiler Explorer][ce_dtor]):
```cpp
class Log
{
    // ...

public:
    // ...

    ~Log()
    {
        free(const_cast<char *>(m_msg));
        m_msg = nullptr;
    }
};
```

That's it! Now when our object of type `Log` will go out of scope, this destructor will be called and free the `m_msg` attribute.

[`const_cast<char *>(m_msg)`][const_cast_cppref] is used to prevent a compiler warning: `free()` expects a `void *` while `m_msg` is only automatically converted to `const void *`. Hence, we use `const_cast<char *>(m_msg)` to cast away the constness of `m_msg`. The specific meaning of this cast and the `<char *>` part will be discussed later.

## Copy constructor

Now what would happen if we do the following (see on [Compiler Explorer][ce_missing_cpy_ctor]):

```cpp
int main()
{
    Log l("Hello, world!");
    Log b(l);

    l.print();
    b.print();
}
```

Firstly, it breaks! Compiler Explorer reports "free(): double free detected in tcache 2". However, there is no compiler error, which is strange because it seems like `b` is constructed using a constructor taking a `Log` object as argument, but we haven't defined such a constructor!

This type of constructor (which takes an object of its own class as a parameter) is called a [copy constructor][cpy_ctor_cppref]; it constructs an object of class `X` by copying another object of the same class. Effectively, it will copy all the attributes of the object passed as an argument.

As we hadn't defined any copy constructors ourselves, the compiler did it for us. This automatically generated constructor will copy `m_severity` and `m_msg` from the object passed as an argument to the new object.

Because the compiler created a basic copy constructor, it copied `l.m_msg` into `b.m_msg`, so both objects pointed to the same message. You probably see where I'm going now: when both objects go out of scope, their destructors are called: first `l`'s, which frees `l.m_msg`, then `b`'s, which **tries** to free `b.m_msg`, but this address has already been freed, hence the double free error!

To resolve this issue, we can create our own copy constructor. As a rule of thumb, you would have to create your own copy constructor (and destructor, as well as move constructor) if you manipulate raw pointers in your class (see on [Compiler Explorer][ce_cpy_ctor]):
```cpp
class Log
{
    // ...

public:
    // ...

    Log(const Log &other):
        m_severity(other.m_severity)
    {
        m_msg = other.m_msg ? strdup(other.m_msg) : nullptr;
    }

    // ...
};
```

It works now because we effectively copy the data pointed to by `m_msg` instead of just copying the pointer! By calling `Log::print()` for both `l` and `b`, we can see `m_msg` has a different address in each object:

```
0xa082b0: Hello, world!
0xa082d0: Hello, world!
```

### Move constructor

Last but not least, the [move constructor][move_ctor_cppref]! It is (again) a special constructor, that allows for the efficient transfer of resources (such as dynamically allocated memory) from one object to another. It is used to improve the performance of object transfers, especially in cases where deep copying of data can be avoided.

A move constructor has the following signature:
```cpp
class Log
{
    // ...

public:
    // ...

    Log(Log &&other) { ... }
};
```

The `&&` notation is an `rvalue` reference, long story short, it's a reference used to identify and work with temporary objects or values. Don't worry too much about this for now.

After a move constructor has been used, the moved object (the object used as an argument when calling the move constructor: `other` in the example above) is considered valid but unspecified. That is to say, assume the moved object can't be used anymore until we dive a bit more into this.

For our `Log` class, we can now define a move constructor (see on [Compiler Explorer][ce_mv_ctor]):
```cpp
class Log
{
    // ...

public:
    // ...

    Log(Log &&other):
        m_severity(other.m_severity)
    {
        m_msg = other.m_msg;
        other.m_msg = nullptr;
    }

    // ...
};

int main()
{
    Log l("Hello, world!");
    l.print();

    Log b(std::move(l));

    // l.print(); // You can't do that anymore!
    b.print();
}
```

In our move constructor, we initialize `m_severity` within the initializer list. Then we steal the `m_msg` pointer from `other` to initialize our `m_msg` pointer. We also set `other.m_msg` to `nullptr`, because otherwise `other` would still have a copy of the pointer in its `m_msg` attribute, so when `other's` destructor is called, it will free `m_msg`, making it invalid for the newly constructed object too (see on [Compiler Explorer][ce_invalid_m_msg])!

> **What is `std::move()`?**
>
> [`std::move`][move_cppref] is used to help the compiler understand what kind of expression we expect. It doesn't actually move anything, but it changes the expression from being a ["lvalue"][value_cat_cppref] to a "xvalue". These details are not very important right now, but using `std::move` here will ensure our move constructor is used instead of our copy constructor.

[class_keyword_cppref]: https://en.cppreference.com/w/cpp/language/class
[const_cast_cppref]: https://en.cppreference.com/w/cpp/language/const_cast
[cpy_ctor_cppref]: https://en.cppreference.com/w/cpp/language/copy_constructor
[move_ctor_cppref]: https://en.cppreference.com/w/cpp/language/move_constructor
[move_cppref]: https://en.cppreference.com/w/cpp/utility/move
[value_cat_cppref]: https://en.cppreference.com/w/cpp/language/value_category

[ce_ctor]: https://godbolt.org/#g:!((g:!((g:!((h:codeEditor,i:(filename:'1',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,selection:(endColumn:2,endLineNumber:25,positionColumn:2,positionLineNumber:25,selectionStartColumn:2,selectionStartLineNumber:25,startColumn:2,startLineNumber:25),source:'%23include+%3Ccstdint%3E++//+For+uint8_t%0A%23include+%3Ciostream%3E+//+For+std::cout,+std::endl,+...%0A%23include+%3Cstring.h%3E+//+For+strdup%0A%0Aclass+Log%0A%7B%0Aprivate:%0A++++uint8_t+m_severity%3B%0A++++const+char+*m_msg%3B%0A%0Apublic:%0A++++Log(const+char+*msg,+uint8_t+severity+%3D+0):%0A++++++++m_severity(severity)%0A++++%7B%0A++++++++m_msg+%3D+msg+%3F+strdup(msg)+:+nullptr%3B%0A++++%7D%0A%0A++++void+print()%0A++++%7B%0A++++++++std::cout+%3C%3C+(void+*)m_msg+%3C%3C+%22:+%22+%3C%3C+m_msg+%3C%3C+std::endl%3B%0A++++%7D%0A%7D%3B%0A%0Aint+main()%0A%7B%0A++++Log+l(%22Hello,+world!!%22)%3B%0A++++l.print()%3B%0A%7D'),l:'5',n:'0',o:'C%2B%2B+source+%231',t:'0')),k:59.6078431372549,l:'4',n:'0',o:'',s:0,t:'0'),(g:!((h:executor,i:(argsPanelShown:'1',compilationPanelShown:'0',compiler:g132,compilerName:'',compilerOutShown:'0',execArgs:'',execStdin:'',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,libs:!(),options:'-Wall+-Wextra',overrides:!(),source:1,stdinPanelShown:'1',tree:0,wrap:'1'),l:'5',n:'0',o:'Executor+x86-64+gcc+13.2+(C%2B%2B,+Editor+%231)',t:'0')),k:40.3921568627451,l:'4',n:'0',o:'',s:0,t:'0')),l:'2',n:'0',o:'',t:'0')),version:4
[ce_def_ctor_missing]: https://godbolt.org/#g:!((g:!((g:!((h:codeEditor,i:(filename:'1',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,selection:(endColumn:6,endLineNumber:21,positionColumn:6,positionLineNumber:21,selectionStartColumn:6,selectionStartLineNumber:21,startColumn:6,startLineNumber:21),source:'%23include+%3Ccstdint%3E++//+For+uint8_t%0A%23include+%3Ciostream%3E+//+For+std::cout,+std::endl,+...%0A%23include+%3Cstring.h%3E+//+For+strdup%0A%0Aclass+Log%0A%7B%0Aprivate:%0A++++uint8_t+m_severity%3B%0A++++const+char+*m_msg%3B%0A%0Apublic:%0A++++Log(const+char+*msg,+uint8_t+severity+%3D+0):%0A++++++++m_severity(severity)%0A++++%7B%0A++++++++m_msg+%3D+msg+%3F+strdup(msg)+:+nullptr%3B%0A++++%7D%0A%0A++++void+print()%0A++++%7B%0A++++++++std::cout+%3C%3C+(void+*)m_msg+%3C%3C+%22:+%22+%3C%3C+m_msg+%3C%3C+std::endl%3B%0A++++%7D%0A%7D%3B%0A%0Aint+main()%0A%7B%0A++++Log+l%3B%0A%7D'),l:'5',n:'0',o:'C%2B%2B+source+%231',t:'0')),k:59.6078431372549,l:'4',n:'0',o:'',s:0,t:'0'),(g:!((h:executor,i:(argsPanelShown:'1',compilationPanelShown:'0',compiler:g132,compilerName:'',compilerOutShown:'0',execArgs:'',execStdin:'',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,libs:!(),options:'-Wall+-Wextra',overrides:!(),source:1,stdinPanelShown:'1',tree:0,wrap:'1'),l:'5',n:'0',o:'Executor+x86-64+gcc+13.2+(C%2B%2B,+Editor+%231)',t:'0')),k:40.3921568627451,l:'4',n:'0',o:'',s:0,t:'0')),l:'2',n:'0',o:'',t:'0')),version:4
[ce_dtor]: https://godbolt.org/#g:!((g:!((g:!((h:codeEditor,i:(filename:'1',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,selection:(endColumn:1,endLineNumber:23,positionColumn:1,positionLineNumber:23,selectionStartColumn:1,selectionStartLineNumber:23,startColumn:1,startLineNumber:23),source:'%23include+%3Ccstdint%3E++//+For+uint8_t%0A%23include+%3Ciostream%3E+//+For+std::cout,+std::endl,+...%0A%23include+%3Cstring.h%3E+//+For+strdup%0A%0Aclass+Log%0A%7B%0Aprivate:%0A++++uint8_t+m_severity%3B%0A++++const+char+*m_msg%3B%0A%0Apublic:%0A++++Log(const+char+*msg+%3D+nullptr,+uint8_t+severity+%3D+0):%0A++++++++m_severity(severity)%0A++++%7B%0A++++++++m_msg+%3D+msg+%3F+strdup(msg)+:+nullptr%3B%0A++++%7D%0A%0A++++~Log()%0A++++%7B%0A++++++++free(const_cast%3Cchar+*%3E(m_msg))%3B%0A++++++++m_msg+%3D+nullptr%3B%0A++++%7D%0A%0A++++void+print()%0A++++%7B%0A++++++++std::cout+%3C%3C+(void+*)m_msg+%3C%3C+%22:+%22+%3C%3C+m_msg+%3C%3C+std::endl%3B%0A++++%7D%0A%7D%3B%0A%0Aint+main()%0A%7B%0A++++Log+l%3B%0A%7D'),l:'5',n:'0',o:'C%2B%2B+source+%231',t:'0')),k:59.6078431372549,l:'4',n:'0',o:'',s:0,t:'0'),(g:!((h:executor,i:(argsPanelShown:'1',compilationPanelShown:'0',compiler:g132,compilerName:'',compilerOutShown:'0',execArgs:'',execStdin:'',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,libs:!(),options:'-Wall+-Wextra',overrides:!(),source:1,stdinPanelShown:'1',tree:0,wrap:'1'),l:'5',n:'0',o:'Executor+x86-64+gcc+13.2+(C%2B%2B,+Editor+%231)',t:'0')),k:40.3921568627451,l:'4',n:'0',o:'',s:0,t:'0')),l:'2',n:'0',o:'',t:'0')),version:4
[ce_missing_cpy_ctor]: https://godbolt.org/#g:!((g:!((g:!((h:codeEditor,i:(filename:'1',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,selection:(endColumn:15,endLineNumber:36,positionColumn:15,positionLineNumber:36,selectionStartColumn:15,selectionStartLineNumber:36,startColumn:15,startLineNumber:36),source:'%23include+%3Ccstdint%3E++//+For+uint8_t%0A%23include+%3Ciostream%3E+//+For+std::cout,+std::endl,+...%0A%23include+%3Cstring.h%3E+//+For+strdup%0A%0Aclass+Log%0A%7B%0Aprivate:%0A++++uint8_t+m_severity%3B%0A++++const+char+*m_msg%3B%0A%0Apublic:%0A++++Log(const+char+*msg+%3D+nullptr,+uint8_t+severity+%3D+0):%0A++++++++m_severity(severity)%0A++++%7B%0A++++++++m_msg+%3D+msg+%3F+strdup(msg)+:+nullptr%3B%0A++++%7D%0A%0A++++~Log()%0A++++%7B%0A++++++++free(const_cast%3Cchar+*%3E(m_msg))%3B%0A++++++++m_msg+%3D+nullptr%3B%0A++++%7D%0A%0A++++void+print()%0A++++%7B%0A++++++++std::cout+%3C%3C+(void+*)m_msg+%3C%3C+%22:+%22+%3C%3C+m_msg+%3C%3C+std::endl%3B%0A++++%7D%0A%7D%3B%0A%0Aint+main()%0A%7B%0A++++Log+l(%22Hello,+world!!%22)%3B%0A++++Log+b(l)%3B%0A%0A++++l.print()%3B%0A++++b.print()%3B%0A%7D'),l:'5',n:'0',o:'C%2B%2B+source+%231',t:'0')),k:59.6078431372549,l:'4',n:'0',o:'',s:0,t:'0'),(g:!((h:executor,i:(argsPanelShown:'1',compilationPanelShown:'0',compiler:g132,compilerName:'',compilerOutShown:'0',execArgs:'',execStdin:'',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,libs:!(),options:'-Wall+-Wextra',overrides:!(),source:1,stdinPanelShown:'1',tree:0,wrap:'1'),l:'5',n:'0',o:'Executor+x86-64+gcc+13.2+(C%2B%2B,+Editor+%231)',t:'0')),k:40.3921568627451,l:'4',n:'0',o:'',s:0,t:'0')),l:'2',n:'0',o:'',t:'0')),version:4
[ce_cpy_ctor]: https://godbolt.org/#g:!((g:!((g:!((h:codeEditor,i:(filename:'1',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,selection:(endColumn:49,endLineNumber:43,positionColumn:49,positionLineNumber:43,selectionStartColumn:49,selectionStartLineNumber:43,startColumn:49,startLineNumber:43),source:'%23include+%3Ccstdint%3E++//+For+uint8_t%0A%23include+%3Ciostream%3E+//+For+std::cout,+std::endl,+...%0A%23include+%3Cstring.h%3E+//+For+strdup%0A%0Aclass+Log%0A%7B%0Aprivate:%0A++++uint8_t+m_severity%3B%0A++++const+char+*m_msg%3B%0A%0Apublic:%0A++++Log(const+char+*msg+%3D+nullptr,+uint8_t+severity+%3D+0):%0A++++++++m_severity(severity)%0A++++%7B%0A++++++++m_msg+%3D+msg+%3F+strdup(msg)+:+nullptr%3B%0A++++%7D%0A%0A++++Log(const+Log+%26other):%0A++++++++m_severity(other.m_severity)%0A++++%7B%0A++++++++m_msg+%3D+other.m_msg+%3F+strdup(other.m_msg)+:+nullptr%3B%0A++++%7D%0A%0A++++~Log()%0A++++%7B%0A++++++++free(const_cast%3Cchar+*%3E(m_msg))%3B%0A++++++++m_msg+%3D+nullptr%3B%0A++++%7D%0A%0A++++void+print()%0A++++%7B%0A++++++++std::cout+%3C%3C+(void+*)m_msg+%3C%3C+%22:+%22+%3C%3C+m_msg+%3C%3C+std::endl%3B%0A++++%7D%0A%7D%3B%0A%0Aint+main()%0A%7B%0A++++Log+l(%22Hello,+world!!%22)%3B%0A++++Log+b(l)%3B%0A%0A++++l.print()%3B%0A%0A++++l.~Log()%3B+//+Manually+calling+l!'s+destructor%0A%0A++++b.print()%3B%0A%7D'),l:'5',n:'0',o:'C%2B%2B+source+%231',t:'0')),k:59.6078431372549,l:'4',n:'0',o:'',s:0,t:'0'),(g:!((h:executor,i:(argsPanelShown:'1',compilationPanelShown:'0',compiler:g132,compilerName:'',compilerOutShown:'0',execArgs:'',execStdin:'',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,libs:!(),options:'-O0+-Wall+-Wextra',overrides:!(),source:1,stdinPanelShown:'1',tree:0,wrap:'1'),l:'5',n:'0',o:'Executor+x86-64+gcc+13.2+(C%2B%2B,+Editor+%231)',t:'0')),k:40.3921568627451,l:'4',n:'0',o:'',s:0,t:'0')),l:'2',n:'0',o:'',t:'0')),version:4
[ce_mv_ctor]: https://godbolt.org/#g:!((g:!((g:!((h:codeEditor,i:(filename:'1',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,selection:(endColumn:1,endLineNumber:30,positionColumn:1,positionLineNumber:30,selectionStartColumn:1,selectionStartLineNumber:30,startColumn:1,startLineNumber:30),source:'%23include+%3Ccstdint%3E++//+For+uint8_t%0A%23include+%3Ciostream%3E+//+For+std::cout,+std::endl,+...%0A%23include+%3Cstring.h%3E+//+For+strdup%0A%0Aclass+Log%0A%7B%0Aprivate:%0A++++uint8_t+m_severity%3B%0A++++const+char+*m_msg%3B%0A%0Apublic:%0A++++Log(const+char+*msg+%3D+nullptr,+uint8_t+severity+%3D+0):%0A++++++++m_severity(severity)%0A++++%7B%0A++++++++m_msg+%3D+msg+%3F+strdup(msg)+:+nullptr%3B%0A++++%7D%0A%0A++++Log(const+Log+%26other):%0A++++++++m_severity(other.m_severity)%0A++++%7B%0A++++++++m_msg+%3D+other.m_msg+%3F+strdup(other.m_msg)+:+nullptr%3B%0A++++%7D%0A%0A++++Log(Log+%26%26other):%0A++++++++m_severity(other.m_severity)%0A++++%7B%0A++++++++m_msg+%3D+other.m_msg%3B%0A++++++++other.m_msg+%3D+nullptr%3B%0A++++%7D%0A%0A++++~Log()%0A++++%7B%0A++++++++free(const_cast%3Cchar+*%3E(m_msg))%3B%0A++++++++m_msg+%3D+nullptr%3B%0A++++%7D%0A%0A++++void+print()%0A++++%7B%0A++++++++std::cout+%3C%3C+(void+*)m_msg+%3C%3C+%22:+%22+%3C%3C+m_msg+%3C%3C+std::endl%3B%0A++++%7D%0A%7D%3B%0A%0Aint+main()%0A%7B%0A++++Log+l(%22Hello,+world!!%22)%3B%0A++++Log+b(l)%3B%0A%0A++++l.print()%3B%0A++++b.print()%3B%0A%7D'),l:'5',n:'0',o:'C%2B%2B+source+%231',t:'0')),k:59.6078431372549,l:'4',n:'0',o:'',s:0,t:'0'),(g:!((h:executor,i:(argsPanelShown:'1',compilationPanelShown:'0',compiler:g132,compilerName:'',compilerOutShown:'0',execArgs:'',execStdin:'',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,libs:!(),options:'-O0+-Wall+-Wextra',overrides:!(),source:1,stdinPanelShown:'1',tree:0,wrap:'1'),l:'5',n:'0',o:'Executor+x86-64+gcc+13.2+(C%2B%2B,+Editor+%231)',t:'0')),k:40.3921568627451,l:'4',n:'0',o:'',s:0,t:'0')),l:'2',n:'0',o:'',t:'0')),version:4
[ce_invalid_m_msg]: https://godbolt.org/#z:OYLghAFBqd5QCxAYwPYBMCmBRdBLAF1QCcAaPECAMzwBtMA7AQwFtMQByARg9KtQYEAysib0QXACx8BBAKoBnTAAUAHpwAMvAFYTStJg1DIApACYAQuYukl9ZATwDKjdAGFUtAK4sGEs6SuADJ4DJgAcj4ARpjEIGYAHKQADqgKhE4MHt6%2B/ilpGQIhYZEsMXGJtpj2jgJCBEzEBNk%2BflwBdpgOmfWNBMUR0bHxSQoNTS257bbj/aGDZcOJAJS2qF7EyOwc5gDMocjeWADUJrtuyGP4gmfYx8cA9A/HAGIkx16hBAkA%2BgQmGgAgnsDkdMKdzk4xsRMKxbo9nm9iMcriAUOsCKQUQR0GjXLQsQA6YkA4FmfYMQ5eE5nNzQ0LAQkIeFPV7vaHoLzJUmkw5MBQKY5BVDAHkAdisQOSxDwADcmAR2KT7vdPoJfgRjiwfkpZbFCABPM6SwEq45oBhjc0IRrHABU2pYClFuxNpOSXiitDwyBAypVwuAEAtVuQNuRDudEIAIscGF5aLRkgQyB8vhqUZg9TKCAaY8cNMs/UCzWbtbr9bmIBWcwblv77iYJQ3S46o2dY07gBCXtjiJzkhAu8tjiA4wmkynjS2m9GeSWAyLgwIrYHTmYAGyoAgIWJFltlnVZysGiDb3fEQnl4%2B1%2BsLxvN%2B%2Btn5d/Pn2JXl/t3a9jlcs87h%2BbbACOY7xomybENOT6zvOpqLkGa7mBuyHvsQ%2B5PoeNaGgBF6fthuZ3vBD4mqWKrAW%2BgGXsB0HEaWrJoZ%2Br4duOEFTq6M5inOQItgAfoGEBEWaTakWRxxUDCmDLpaBA/KIYy0mGtp2rcQ5fiB9YcZh5Hqfm4GTlBWl0bBPFPrKqB4OgxzSl8gmcaJZGouiXiarStLHBA5mWfaywUW55zrmYY7mGYEJuO5fnnO5Tn4rRwlceKc5GaSXxakwoR2Txj50WutAQCFAAS1S0KgWIAO4kLQ6BgGAIWaQ5xy0ISNmCHZyVPmuUTVjiaIsKgeoQLQyz1S2UTNTKrUjVl0YcKstCcAArLwfgcFopCoJw4WWNYKLrJs4J7DwpAEJos2rAA1iAC0AJyEhuGhigkki7FwuximYC2SNd%2BicJIy2netnC8AoIAaMdp2rHAsBIJgqhdC5JDkJQjTAAoyiGNUQgIKgZUrUdaAsMkdAKpk6NhLQWM4yta0E0T9BxMAr0BLTdCxOErDbLwLP0wA8i5lO4wDsNdICxCo0DgRw8g9TXBL/CCCIYjsFIMiCIoKjqKtOh6AYRgoNY1j6HgUQg5AqyoMmmQgxwAC0PMaMcNsAOpiLQjtO7DKZMLw/WxDKWCm4JawbFsehXKEZOY9jgvcLwKaYOwYNlcQTDJJwPBzYt/1a4DHDYFLCPIqoCQbjbG6SMcwDIMgxyvYSoUQFtViWFiuCEO8h3LHHEMXSAkgaISuzXWYXALRuJdmGKkgLVwP0cH9pDUz7Esg2DJ1a8Nc9mNna0bRw3cb6s2bpM4khAA%3D
