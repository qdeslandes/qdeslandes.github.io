---
layout: page
title:  "OOP - Encapsulation"
collection: cpp
published: true
---

A couple of lessons ago, we started using `public` and `private` for our class members. I remember mentioning, "We'll talk about it later". Well, we are now "later".

## Attributes and methods visibility

Access specifiers are a way to define who can access our object's attributes and methods. They are part of the class definition and apply subsequently defined attributes and members.

### Private

Let's try the following class (see on [Compiler Explorer][ce_private_access]):
```cpp
class Log
{
private:
    std::string m_msg;

public:
    Log(std::string msg):
        m_msg(msg)
    {}
};
```

We are greeted with a build failure and the following message: "'std::string Log::m_msg' is private within this context". That's true, `m_msg` is defined within the `private` section of our class!

A private member of a class is only accessible to the members of that class, regardless of whether the members are on the same or different instances. For example:

```cpp
class Log
{
public:
    // ...

    bool compare(Log &other)
    {
        return other.m_msg == m.msg;
    }
};

int main()
{
    Log l("Hello");
    Lob b("world!");

    l.compare(b);
}
```

`l` and `b` are different instances of the same class, so `l` can access `b`'s private members. `private` members are useful to provide encapsulation.

Not only attributes but also methods can be private (see on [Compiler Explorer][ce_pv_method]):
```cpp
class Log
{
private:
    // ...

    std::string prefixed(const std::string &prefix = "Log: ")
    {
        return prefix + m_msg;
    }

public:
    // ...
    void print()
    {
        std::cout << prefixed() << std::endl;
    }
}

int main()
{
    Log l("Hello, world!");

    l.print();
}
```

The private method `prefixed()` can only be called from an instance of `Log`. By default (if no access specifier is mentioned) members of a class are private.

### Public

`public` is the opposite of `private`: a public member of a class is accessible anywhere (see on [Compiler Explorer][ce_public]):
```cpp
class Log
{
public:
    std::string m_msg;

    // ...
};

int main()
{
    Log l("Hello, world!");

    std::cout << l.m_msg << std::endl;

    l.m_msg = "Goodbye, world!";

    std::cout << l.m_msg << std::endl;
}
```

### Protected

Protected attributes and methods have a level of visibility between public and private. They are accessible from derived classes (subclasses), but not from external code. This access specifier is used in class inheritance to allow derived classes to access certain members while still restricting access to external code.

`protected` specifier is not very relevant for now, we'll learn how to use it when we'll discuss inheritance.

### As a general rule

The choice of access specifier depends on the design and encapsulation goals of your class. Public members define the interface, private members hide implementation details, and protected members enable access to derived classes. It's important to make these decisions carefully to ensure proper encapsulation and maintainability of your code.

## Static attributes and methods

The [static keyword][static_cppref] in C++ is not primarily a tool for encapsulation, but it can be used in a way that affects encapsulation indirectly, especially when applied to class methods. Its primary purpose is to create class-level (static) members that are shared among all objects of the class, rather than instance-specific (non-static) members.

A static method belongs to the class itself. It can be used for utility functions that are not related to a specific instance of a class (see on [Compiler Explorer][ce_static_method]):
```cpp
class Log
{
public:
    static std::string getProgramName()
    {
        return "cpp_static_lesson";
    }
}

int main()
{
    std::cout << Log::getProgramName() << std::endl;
}
```

We can access a static method through `CLASS_NAME::METHOD_NAME`, as you can see in the example above. The access specifiers still apply here: a private static method can only be called by the class' static methods or instances.

Similarly, static attributes are class-level attributes belonging to the class itself. However, like any attribute in a class, static attributes are only a declaration, and they need storage to be allocated! Hence, static attributes need to be defined outside the class (see on [Compiler Explorer][ce_static_attr]):
```cpp
class Log
{
private:
    static std::string programName; // Declare the static attribute

    // ...

public:
    static std::string getProgramName()
    {
        return Log::programName;
    }

    // ...
};

// Define the static attribute
std::string Log::programName = "cpp_static_lesson";

int main()
{
    std::cout << Log::getProgramName() << std::endl;
}
```

Access specifiers also apply to static attributes!

[static_cppref]: https://en.cppreference.com/w/cpp/language/static

[ce_private_access]: https://godbolt.org/#g:!((g:!((g:!((h:codeEditor,i:(filename:'1',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,selection:(endColumn:22,endLineNumber:19,positionColumn:22,positionLineNumber:19,selectionStartColumn:22,selectionStartLineNumber:19,startColumn:22,startLineNumber:19),source:'%23include+%3Ciostream%3E+//+For+std::cout,+std::endl,+...%0A%23include+%3Cstring.h%3E+//+For+strdup%0A%0Aclass+Log%0A%7B%0Aprivate:%0A++++std::string+m_msg%3B%0A%0Apublic:%0A++++Log(std::string+msg):%0A++++++++m_msg(msg)%0A++++%7B%7D%0A%7D%3B%0A%0Aint+main()%0A%7B%0A++++Log+l(%22Hello,+world!!%22)%3B%0A%0A++++std::cout+%3C%3C+l.m_msg+%3C%3C+std::endl%3B%0A%7D'),l:'5',n:'0',o:'C%2B%2B+source+%231',t:'0')),k:59.6078431372549,l:'4',n:'0',o:'',s:0,t:'0'),(g:!((h:executor,i:(argsPanelShown:'1',compilationPanelShown:'0',compiler:g132,compilerName:'',compilerOutShown:'0',execArgs:'',execStdin:'',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,libs:!(),options:'-O0+-Wall+-Wextra',overrides:!(),source:1,stdinPanelShown:'1',tree:0,wrap:'1'),l:'5',n:'0',o:'Executor+x86-64+gcc+13.2+(C%2B%2B,+Editor+%231)',t:'0')),k:40.3921568627451,l:'4',n:'0',o:'',s:0,t:'0')),l:'2',n:'0',o:'',t:'0')),version:4
[ce_pv_method]: https://godbolt.org/#g:!((g:!((g:!((h:codeEditor,i:(filename:'1',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,selection:(endColumn:2,endLineNumber:30,positionColumn:2,positionLineNumber:30,selectionStartColumn:2,selectionStartLineNumber:30,startColumn:2,startLineNumber:30),source:'%23include+%3Ciostream%3E+//+For+std::cout,+std::endl,+...%0A%23include+%3Cstring.h%3E+//+For+strdup%0A%0Aclass+Log%0A%7B%0Aprivate:%0A++++std::string+m_msg%3B%0A%0A++++std::string+prefixed(const+std::string+%26prefix+%3D+%22Log:+%22)%0A++++%7B%0A++++++++return+prefix+%2B+m_msg%3B%0A++++%7D%0A%0Apublic:%0A++++Log(std::string+msg):%0A++++++++m_msg(msg)%0A++++%7B%7D%0A%0A++++void+print()%0A++++%7B%0A++++++++std::cout+%3C%3C+prefixed()+%3C%3C+std::endl%3B%0A++++%7D%0A%7D%3B%0A%0Aint+main()%0A%7B%0A++++Log+l(%22Hello,+world!!%22)%3B%0A%0A++++l.print()%3B%0A%7D'),l:'5',n:'0',o:'C%2B%2B+source+%231',t:'0')),k:54.352941176470594,l:'4',n:'0',o:'',s:0,t:'0'),(g:!((h:executor,i:(argsPanelShown:'1',compilationPanelShown:'0',compiler:g132,compilerName:'',compilerOutShown:'0',execArgs:'',execStdin:'',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,libs:!(),options:'-O0+-Wall+-Wextra',overrides:!(),source:1,stdinPanelShown:'1',tree:0,wrap:'1'),l:'5',n:'0',o:'Executor+x86-64+gcc+13.2+(C%2B%2B,+Editor+%231)',t:'0')),k:45.647058823529406,l:'4',n:'0',o:'',s:0,t:'0')),l:'2',n:'0',o:'',t:'0')),version:4
[ce_public]: https://godbolt.org/#g:!((g:!((g:!((h:codeEditor,i:(filename:'1',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,selection:(endColumn:26,endLineNumber:9,positionColumn:26,positionLineNumber:9,selectionStartColumn:26,selectionStartLineNumber:9,startColumn:26,startLineNumber:9),source:'%23include+%3Ciostream%3E+//+For+std::cout,+std::endl,+...%0A%23include+%3Cstring%3E+++//+For+std::string%0A%0Aclass+Log%0A%7B%0Apublic:%0A++++std::string+m_msg%3B%0A%0A++++Log(std::string+msg):%0A++++++++m_msg(msg)%0A++++%7B%7D%0A%7D%3B%0A%0Aint+main()%0A%7B%0A++++Log+l(%22Hello,+world!!%22)%3B%0A%0A++++std::cout+%3C%3C+l.m_msg+%3C%3C+std::endl%3B%0A%0A++++l.m_msg+%3D+%22Goodbye,+world!!%22%3B%0A%0A++++std::cout+%3C%3C+l.m_msg+%3C%3C+std::endl%3B%0A%7D'),l:'5',n:'0',o:'C%2B%2B+source+%231',t:'0')),k:54.352941176470594,l:'4',n:'0',o:'',s:0,t:'0'),(g:!((h:executor,i:(argsPanelShown:'1',compilationPanelShown:'0',compiler:g132,compilerName:'',compilerOutShown:'0',execArgs:'',execStdin:'',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,libs:!(),options:'-O0+-Wall+-Wextra',overrides:!(),source:1,stdinPanelShown:'1',tree:0,wrap:'1'),l:'5',n:'0',o:'Executor+x86-64+gcc+13.2+(C%2B%2B,+Editor+%231)',t:'0')),k:45.647058823529406,l:'4',n:'0',o:'',s:0,t:'0')),l:'2',n:'0',o:'',t:'0')),version:4
[ce_static_method]: https://godbolt.org/#g:!((g:!((g:!((h:codeEditor,i:(filename:'1',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,selection:(endColumn:2,endLineNumber:22,positionColumn:2,positionLineNumber:22,selectionStartColumn:2,selectionStartLineNumber:22,startColumn:2,startLineNumber:22),source:'%23include+%3Ciostream%3E+//+For+std::cout,+std::endl,+...%0A%0Aclass+Log%0A%7B%0Aprivate:%0A++++std::string+m_msg%3B%0A%0Apublic:%0A++++static+std::string+getProgramName()%0A++++%7B%0A++++++++return+%22cpp_static_lesson%22%3B%0A++++%7D%0A%0A++++Log(std::string+msg):%0A++++++++m_msg(msg)%0A++++%7B%7D%0A%7D%3B%0A%0Aint+main()%0A%7B%0A++++std::cout+%3C%3C+Log::getProgramName()+%3C%3C+std::endl%3B%0A%7D'),l:'5',n:'0',o:'C%2B%2B+source+%231',t:'0')),k:54.352941176470594,l:'4',n:'0',o:'',s:0,t:'0'),(g:!((h:executor,i:(argsPanelShown:'1',compilationPanelShown:'0',compiler:g132,compilerName:'',compilerOutShown:'0',execArgs:'',execStdin:'',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,libs:!(),options:'-O0+-Wall+-Wextra',overrides:!(),source:1,stdinPanelShown:'1',tree:0,wrap:'1'),l:'5',n:'0',o:'Executor+x86-64+gcc+13.2+(C%2B%2B,+Editor+%231)',t:'0')),k:45.647058823529406,l:'4',n:'0',o:'',s:0,t:'0')),l:'2',n:'0',o:'',t:'0')),version:4
[ce_static_attr]: https://godbolt.org/#g:!((g:!((g:!((h:codeEditor,i:(filename:'1',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,selection:(endColumn:2,endLineNumber:26,positionColumn:1,positionLineNumber:4,selectionStartColumn:2,selectionStartLineNumber:26,startColumn:1,startLineNumber:4),source:'%23include+%3Ciostream%3E+//+For+std::cout,+std::endl,+...%0A%23include+%3Cstring%3E+++//+For+std::string%0A%0Aclass+Log%0A%7B%0Aprivate:%0A++++static+std::string+programName%3B%0A++++std::string+m_msg%3B%0A%0Apublic:%0A++++static+std::string+getProgramName()%0A++++%7B%0A++++++++return+Log::programName%3B%0A++++%7D%0A%0A++++Log(std::string+msg):%0A++++++++m_msg(msg)%0A++++%7B%7D%0A%7D%3B%0A%0Astd::string+Log::programName+%3D+%22cpp_static_lesson%22%3B%0A%0Aint+main()%0A%7B%0A++++std::cout+%3C%3C+Log::getProgramName()+%3C%3C+std::endl%3B%0A%7D'),l:'5',n:'0',o:'C%2B%2B+source+%231',t:'0')),k:54.352941176470594,l:'4',n:'0',o:'',s:0,t:'0'),(g:!((h:executor,i:(argsPanelShown:'1',compilationPanelShown:'0',compiler:g132,compilerName:'',compilerOutShown:'0',execArgs:'',execStdin:'',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,libs:!(),options:'-O0+-Wall+-Wextra',overrides:!(),source:1,stdinPanelShown:'1',tree:0,wrap:'1'),l:'5',n:'0',o:'Executor+x86-64+gcc+13.2+(C%2B%2B,+Editor+%231)',t:'0')),k:45.647058823529406,l:'4',n:'0',o:'',s:0,t:'0')),l:'2',n:'0',o:'',t:'0')),version:4
