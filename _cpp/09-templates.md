---
layout: page
title: "Templates"
collection: cpp
published: true
---

This lesson will introduce you to the concept of template and generic programming in C++. This subject by itself can be very challenging and introduce a lot of new concepts. Hence, we'll focus on the basic definition and usage of templates in C++.

## About generic programming

Generic programming is a paradigm that emphasizes writing code independent of specific data types. It utilizes parameterized types to create reusable algorithms and data structures. The focus is on reusability, type safety, and abstraction. In C++, templates are a key mechanism for generic programming, allowing the creation of versatile code that works with various data types. Other languages like Java and C# also implement generics for similar purposes. The goal is to build flexible, reusable components with minimal code duplication.

## Introduction to templates in C++

In C++, templates provide a powerful mechanism for generic programming. They allow you to write functions and classes that work with different data types while maintaining type safety. Let's break down the key aspects of C++ templates into smaller steps (see on [Compiler Explorer][ce_template_intro]):

```cpp
template <typename T>
T add(T a, T b) {
    return a + b;
}
```

In this example, `typename T` indicates a template parameter, and `T` is used as a placeholder for the actual data type. The function `add` can now work with integers, floating-point numbers, or any other type that supports the `+` operator.

To use our template, we simply need to call it:
```cpp
// Prints 8
std::cout << add<int>(4, 3) << std::endl;

// Prints 17.5
std::cout << add<float>(10.1, 7.4) << std::endl;

// Prints "Hello, world!"
std::cout << add<std::string>(std::string("Hello, "), std::string("world!")) << std::endl;
```

To call our templated function, we use its name, a type to use the function with, and its arguments.

While the previous examples were focused on function templates, it's also possible to define a class templates in C++ (see on [Compiler Explorer][ce_class_template]):

```cpp
template <typename T>
class Box {
private:
    T value;
public:
    void setValue(T val) {
        value = val;
    }

    T getValue() const {
        return value;
    }
};
```

The `Box` class can store and retrieve values of any type, for example:

```cpp
Box<int> b;
b.setValue(3);

std::cout << b.getValue() << std::endl;
```

## Using templates

### Type inference

It's not mandatory to explicitly provide a type to our template function, as the compiler provides **type inference** (see on [Compiler Explorer][ce_type_inference]):

```cpp
// Prints 8
std::cout << add(4, 3) << std::endl;

// Prints 17.5
std::cout << add(10.1, 7.4) << std::endl;

// Prints "Hello, world!"
std::cout << add(std::string("Hello, "), std::string("world!")) << std::endl;
```

In this case, the compiler will automatically infer the template's argument based on the function argument's type.

### Template Specialization

Template specialization is a mechanism that allows you to provide a customized implementation for a specific data type within a template. It enables you to override the default behavior of a template for certain types, while retaining the generic implementation for others. This is particularly useful when you need to handle specific cases differently or optimize the code for particular types (see on [Compiler Explorer][ce_template_before_spe]):

```cpp
class Number
{
public:
    float value;

    Number(float v): value{v} {}
};

// This doesn't have, as the compiler doesn't know how to do Number + Number
Number n0(3.1);
Number n1(163.6);

Number total = add(n0, n1);
```

The example above doesn't work; the compiler complains about "invalid operands to binary expression ('Number' and 'Number')". The reasoning is quite straightforward: it doesn't know how to add two `Number` objects. One solution to solve this issue would be to define the `operator+()` for the `Number` object, or to specialize the template for the `Number` type (see on [Compiler Explorer][ce_template_after_spe])

The solution in this case would be to define a template specialization for the class `Number`:

```cpp
template <>
Number add<Number>(Number a, Number b) {
    return Number(a.value + b.value);
}

Number n0(3.1);
Number n1(163.6);

// Works as expected!
Number total = add(n0, n1);
```

Now that our compiler has found the explicit template specialization for the `Number` class, it doesn't complain anymore and can compile the code properly!

### Multiple template parameters

Keep in mind that, like any function or method, templates can have more than one parameter (see on [Compiler Explorer][ce_template_multi_param]):

```cpp
template <typename Param, typename Return>
Return add(Param a, Param b)
{
    return static_cast<Return>(a + b);
}

// Then we would call, for example:
std::cout << add<int, int>(3, 4) << std::endl;
```

Our template now has two parameters: the type of the input values, and the type of the return value. This could be useful (or not) in a project to get a different return type, for example (see on [Compiler Explorer][ce_diff_ret]):

```cpp
std::cout << add<float, int>(3.1, 4.5) << std::endl;
```

We could also assign a default type to a template parameter. In this case, the code would behave in the same way as a function with a default parameter value (see on [Compiler Explorer][ce_template_default]):

```cpp
template <typename Param, typename Return = Param>
Return add(Param a, Param b)
{
    return static_cast<Return>(a + b);
}
```

While the example above uses a template parameter as the default value for another template parameter, we might as well use a type, such as: `template <typename Param, typename Return = int>`.

In this case, if no type is provided for `Return`, or if the compiler is not able to infer it, the default type will be used for `Return`.


[ce_template_intro]: https://godbolt.org/#g:!((g:!((g:!((h:codeEditor,i:(filename:'1',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,selection:(endColumn:27,endLineNumber:14,positionColumn:27,positionLineNumber:14,selectionStartColumn:27,selectionStartLineNumber:14,startColumn:27,startLineNumber:14),source:'%23include+%3Ciostream%3E%0A%0Atemplate+%3Ctypename+T%3E%0AT+add(T+a,+T+b)+%7B%0A++++return+a+%2B+b%3B%0A%7D%0A%0Aint+main()%0A%7B%0A++++//+Prints+8%0A++++std::cout+%3C%3C+add%3Cint%3E(4,+3)+%3C%3C+std::endl%3B%0A%0A++++//+Prints+17.5%0A++++std::cout+%3C%3C+add%3Cfloat%3E(10.1,+7.4)+%3C%3C+std::endl%3B%0A%0A++++//+Prints+%22Hello,+world!!%22%0A++++std::cout+%3C%3C+add%3Cstd::string%3E(std::string(%22Hello,+%22),+std::string(%22world!!%22))+%3C%3C+std::endl%3B%0A%0A++++return+0%3B%0A%7D'),l:'5',n:'0',o:'C%2B%2B+source+%231',t:'0')),k:42.24883566200932,l:'4',n:'0',o:'',s:0,t:'0'),(g:!((h:compiler,i:(compiler:clang1701,filters:(b:'0',binary:'1',binaryObject:'1',commentOnly:'0',debugCalls:'1',demangle:'0',directives:'0',execute:'0',intel:'0',libraryCode:'0',trim:'1'),flagsViewOpen:'1',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,libs:!(),options:'-Wall+-Wextra+-Wshadow',overrides:!(),selection:(endColumn:1,endLineNumber:1,positionColumn:1,positionLineNumber:1,selectionStartColumn:1,selectionStartLineNumber:1,startColumn:1,startLineNumber:1),source:1),l:'5',n:'0',o:'+x86-64+clang+17.0.1+(Editor+%231)',t:'0')),k:24.417831004657355,l:'4',n:'0',o:'',s:0,t:'0'),(g:!((h:output,i:(compilerName:'x86-64+gcc+12.2',editorid:1,fontScale:14,fontUsePx:'0',j:1,wrap:'1'),l:'5',n:'0',o:'Output+of+x86-64+clang+17.0.1+(Compiler+%231)',t:'0')),k:33.33333333333333,l:'4',n:'0',o:'',s:0,t:'0')),l:'2',n:'0',o:'',t:'0')),version:4
[ce_type_inference]: https://godbolt.org/#g:!((g:!((g:!((h:codeEditor,i:(filename:'1',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,selection:(endColumn:15,endLineNumber:17,positionColumn:9,positionLineNumber:10,selectionStartColumn:15,selectionStartLineNumber:17,startColumn:9,startLineNumber:10),source:'%23include+%3Ciostream%3E%0A%0Atemplate+%3Ctypename+T%3E%0AT+add(T+a,+T+b)+%7B%0A++++return+a+%2B+b%3B%0A%7D%0A%0Aint+main()%0A%7B%0A++++//+Prints+8%0A++++std::cout+%3C%3C+add(4,+3)+%3C%3C+std::endl%3B%0A%0A++++//+Prints+17.5%0A++++std::cout+%3C%3C+add(10.1,+7.4)+%3C%3C+std::endl%3B%0A%0A++++//+Prints+%22Hello,+world!!%22%0A++++std::cout+%3C%3C+add(std::string(%22Hello,+%22),+std::string(%22world!!%22))+%3C%3C+std::endl%3B%0A%0A++++return+0%3B%0A%7D'),l:'5',n:'0',o:'C%2B%2B+source+%231',t:'0')),k:42.24883566200932,l:'4',n:'0',o:'',s:0,t:'0'),(g:!((h:compiler,i:(compiler:clang1701,filters:(b:'0',binary:'1',binaryObject:'1',commentOnly:'0',debugCalls:'1',demangle:'0',directives:'0',execute:'0',intel:'0',libraryCode:'0',trim:'1'),flagsViewOpen:'1',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,libs:!(),options:'-Wall+-Wextra+-Wshadow',overrides:!(),selection:(endColumn:1,endLineNumber:1,positionColumn:1,positionLineNumber:1,selectionStartColumn:1,selectionStartLineNumber:1,startColumn:1,startLineNumber:1),source:1),l:'5',n:'0',o:'+x86-64+clang+17.0.1+(Editor+%231)',t:'0')),k:24.417831004657355,l:'4',n:'0',o:'',s:0,t:'0'),(g:!((h:output,i:(compilerName:'x86-64+gcc+12.2',editorid:1,fontScale:14,fontUsePx:'0',j:1,wrap:'1'),l:'5',n:'0',o:'Output+of+x86-64+clang+17.0.1+(Compiler+%231)',t:'0')),k:33.33333333333333,l:'4',n:'0',o:'',s:0,t:'0')),l:'2',n:'0',o:'',t:'0')),version:4
[ce_class_template]: https://godbolt.org/#g:!((g:!((g:!((h:codeEditor,i:(filename:'1',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,selection:(endColumn:16,endLineNumber:19,positionColumn:16,positionLineNumber:19,selectionStartColumn:16,selectionStartLineNumber:19,startColumn:16,startLineNumber:19),source:'%23include+%3Ciostream%3E%0A%0Atemplate+%3Ctypename+T%3E%0Aclass+Box+%7B%0Aprivate:%0A++++T+value%3B%0Apublic:%0A++++void+setValue(T+val)+%7B%0A++++++++value+%3D+val%3B%0A++++%7D%0A%0A++++T+getValue()+const+%7B%0A++++++++return+value%3B%0A++++%7D%0A%7D%3B%0A%0Aint+main()%0A%7B%0A++++Box%3Cint%3E+b%3B%0A++++b.setValue(3)%3B%0A%0A++++std::cout+%3C%3C+b.getValue()+%3C%3C+std::endl%3B%0A%0A++++return+0%3B%0A%7D'),l:'5',n:'0',o:'C%2B%2B+source+%231',t:'0')),k:57.5515635395875,l:'4',n:'0',o:'',s:0,t:'0'),(g:!((h:compiler,i:(compiler:clang1701,filters:(b:'0',binary:'1',binaryObject:'1',commentOnly:'0',debugCalls:'1',demangle:'0',directives:'0',execute:'0',intel:'0',libraryCode:'0',trim:'1'),flagsViewOpen:'1',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,libs:!(),options:'-Wall+-Wextra+-Wshadow',overrides:!(),selection:(endColumn:1,endLineNumber:1,positionColumn:1,positionLineNumber:1,selectionStartColumn:1,selectionStartLineNumber:1,startColumn:1,startLineNumber:1),source:1),l:'5',n:'0',o:'+x86-64+clang+17.0.1+(Editor+%231)',t:'0')),k:9.115103127079172,l:'4',n:'0',o:'',s:0,t:'0'),(g:!((h:output,i:(compilerName:'x86-64+gcc+12.2',editorid:1,fontScale:14,fontUsePx:'0',j:1,wrap:'1'),l:'5',n:'0',o:'Output+of+x86-64+clang+17.0.1+(Compiler+%231)',t:'0')),k:33.33333333333333,l:'4',n:'0',o:'',s:0,t:'0')),l:'2',n:'0',o:'',t:'0')),version:4
[ce_template_before_spe]: https://godbolt.org/#g:!((g:!((g:!((h:codeEditor,i:(filename:'1',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,selection:(endColumn:23,endLineNumber:22,positionColumn:23,positionLineNumber:22,selectionStartColumn:23,selectionStartLineNumber:22,startColumn:23,startLineNumber:22),source:'%23include+%3Ciostream%3E%0A%0Atemplate+%3Ctypename+T%3E%0AT+add(T+a,+T+b)+%7B%0A++++return+a+%2B+b%3B%0A%7D%0A%0Aclass+Number%0A%7B%0Apublic:%0A++++float+value%3B%0A%0A++++Number(float+v):+value%7Bv%7D+%7B%7D%0A%7D%3B%0A%0Aint+main()%0A%7B%0A++++//+This+doesn!'t+have,+as+the+compiler+doesn!'t+know+how+to+do+Number+%2B+Number%0A++++Number+n0(3.1)%3B%0A++++Number+n1(163.6)%3B%0A%0A++++Number+total+%3D+add(n0,+n1)%3B%0A%0A++++return+0%3B%0A%7D'),l:'5',n:'0',o:'C%2B%2B+source+%231',t:'0')),k:57.5515635395875,l:'4',n:'0',o:'',s:0,t:'0'),(g:!((h:compiler,i:(compiler:clang1701,filters:(b:'0',binary:'1',binaryObject:'1',commentOnly:'0',debugCalls:'1',demangle:'0',directives:'0',execute:'0',intel:'0',libraryCode:'0',trim:'1'),flagsViewOpen:'1',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,libs:!(),options:'-Wall+-Wextra+-Wshadow',overrides:!(),selection:(endColumn:1,endLineNumber:1,positionColumn:1,positionLineNumber:1,selectionStartColumn:1,selectionStartLineNumber:1,startColumn:1,startLineNumber:1),source:1),l:'5',n:'0',o:'+x86-64+clang+17.0.1+(Editor+%231)',t:'0')),k:9.115103127079172,l:'4',n:'0',o:'',s:0,t:'0'),(g:!((h:output,i:(compilerName:'x86-64+gcc+12.2',editorid:1,fontScale:14,fontUsePx:'0',j:1,wrap:'1'),l:'5',n:'0',o:'Output+of+x86-64+clang+17.0.1+(Compiler+%231)',t:'0')),k:33.33333333333333,l:'4',n:'0',o:'',s:0,t:'0')),l:'2',n:'0',o:'',t:'0')),version:4
[ce_template_after_spe]: https://godbolt.org/#g:!((g:!((g:!((h:codeEditor,i:(filename:'1',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,selection:(endColumn:1,endLineNumber:20,positionColumn:1,positionLineNumber:16,selectionStartColumn:1,selectionStartLineNumber:20,startColumn:1,startLineNumber:16),source:'%23include+%3Ciostream%3E%0A%0Atemplate+%3Ctypename+T%3E%0AT+add(T+a,+T+b)+%7B%0A++++return+a+%2B+b%3B%0A%7D%0A%0Aclass+Number%0A%7B%0Apublic:%0A++++float+value%3B%0A%0A++++Number(float+v):+value%7Bv%7D+%7B%7D%0A%7D%3B%0A%0Atemplate+%3C%3E%0ANumber+add%3CNumber%3E(Number+a,+Number+b)+%7B%0A++++return+Number(a.value+%2B+b.value)%3B%0A%7D%0A%0Aint+main()%0A%7B%0A++++//+This+doesn!'t+have,+as+the+compiler+doesn!'t+know+how+to+do+Number+%2B+Number%0A++++Number+n0(3.1)%3B%0A++++Number+n1(163.6)%3B%0A%0A++++Number+total+%3D+add(n0,+n1)%3B%0A%0A++++return+0%3B%0A%7D'),l:'5',n:'0',o:'C%2B%2B+source+%231',t:'0')),k:57.5515635395875,l:'4',n:'0',o:'',s:0,t:'0'),(g:!((h:compiler,i:(compiler:clang1701,filters:(b:'0',binary:'1',binaryObject:'1',commentOnly:'0',debugCalls:'1',demangle:'0',directives:'0',execute:'0',intel:'0',libraryCode:'0',trim:'1'),flagsViewOpen:'1',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,libs:!(),options:'-Wall+-Wextra+-Wshadow',overrides:!(),selection:(endColumn:1,endLineNumber:1,positionColumn:1,positionLineNumber:1,selectionStartColumn:1,selectionStartLineNumber:1,startColumn:1,startLineNumber:1),source:1),l:'5',n:'0',o:'+x86-64+clang+17.0.1+(Editor+%231)',t:'0')),k:9.115103127079172,l:'4',n:'0',o:'',s:0,t:'0'),(g:!((h:output,i:(compilerName:'x86-64+gcc+12.2',editorid:1,fontScale:14,fontUsePx:'0',j:1,wrap:'1'),l:'5',n:'0',o:'Output+of+x86-64+clang+17.0.1+(Compiler+%231)',t:'0')),k:33.33333333333333,l:'4',n:'0',o:'',s:0,t:'0')),l:'2',n:'0',o:'',t:'0')),version:4
[ce_template_multi_param]: https://godbolt.org/#g:!((g:!((g:!((h:codeEditor,i:(filename:'1',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,selection:(endColumn:51,endLineNumber:11,positionColumn:5,positionLineNumber:11,selectionStartColumn:51,selectionStartLineNumber:11,startColumn:5,startLineNumber:11),source:'%23include+%3Ciostream%3E%0A%0Atemplate+%3Ctypename+Param,+typename+Return%3E%0AReturn+add(Param+a,+Param+b)%0A%7B%0A++++return+static_cast%3CReturn%3E(a+%2B+b)%3B%0A%7D%0A%0Aint+main()%0A%7B%0A++++std::cout+%3C%3C+add%3Cint,+int%3E(3,+4)+%3C%3C+std::endl%3B%0A%0A++++return+0%3B%0A%7D'),l:'5',n:'0',o:'C%2B%2B+source+%231',t:'0')),k:57.5515635395875,l:'4',n:'0',o:'',s:0,t:'0'),(g:!((h:compiler,i:(compiler:clang1701,filters:(b:'0',binary:'1',binaryObject:'1',commentOnly:'0',debugCalls:'1',demangle:'0',directives:'0',execute:'0',intel:'0',libraryCode:'0',trim:'1'),flagsViewOpen:'1',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,libs:!(),options:'-Wall+-Wextra+-Wshadow',overrides:!(),selection:(endColumn:1,endLineNumber:1,positionColumn:1,positionLineNumber:1,selectionStartColumn:1,selectionStartLineNumber:1,startColumn:1,startLineNumber:1),source:1),l:'5',n:'0',o:'+x86-64+clang+17.0.1+(Editor+%231)',t:'0')),k:9.115103127079172,l:'4',n:'0',o:'',s:0,t:'0'),(g:!((h:output,i:(compilerName:'x86-64+gcc+12.2',editorid:1,fontScale:14,fontUsePx:'0',j:1,wrap:'1'),l:'5',n:'0',o:'Output+of+x86-64+clang+17.0.1+(Compiler+%231)',t:'0')),k:33.33333333333333,l:'4',n:'0',o:'',s:0,t:'0')),l:'2',n:'0',o:'',t:'0')),version:4
[ce_diff_ret]: https://godbolt.org/#g:!((g:!((g:!((h:codeEditor,i:(filename:'1',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,selection:(endColumn:57,endLineNumber:11,positionColumn:57,positionLineNumber:11,selectionStartColumn:57,selectionStartLineNumber:11,startColumn:57,startLineNumber:11),source:'%23include+%3Ciostream%3E%0A%0Atemplate+%3Ctypename+Param,+typename+Return%3E%0AReturn+add(Param+a,+Param+b)%0A%7B%0A++++return+static_cast%3CReturn%3E(a+%2B+b)%3B%0A%7D%0A%0Aint+main()%0A%7B%0A++++std::cout+%3C%3C+add%3Cfloat,+int%3E(3.1,+4.5)+%3C%3C+std::endl%3B%0A%0A++++return+0%3B%0A%7D'),l:'5',n:'0',o:'C%2B%2B+source+%231',t:'0')),k:48.369926813040586,l:'4',n:'0',o:'',s:0,t:'0'),(g:!((h:compiler,i:(compiler:clang1701,filters:(b:'0',binary:'1',binaryObject:'1',commentOnly:'0',debugCalls:'1',demangle:'0',directives:'0',execute:'0',intel:'0',libraryCode:'0',trim:'1'),flagsViewOpen:'1',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,libs:!(),options:'-Wall+-Wextra+-Wshadow',overrides:!(),selection:(endColumn:1,endLineNumber:1,positionColumn:1,positionLineNumber:1,selectionStartColumn:1,selectionStartLineNumber:1,startColumn:1,startLineNumber:1),source:1),l:'5',n:'0',o:'+x86-64+clang+17.0.1+(Editor+%231)',t:'0')),k:21.984418260253683,l:'4',n:'0',o:'',s:0,t:'0'),(g:!((h:output,i:(compilerName:'x86-64+gcc+12.2',editorid:1,fontScale:14,fontUsePx:'0',j:1,wrap:'1'),l:'5',n:'0',o:'Output+of+x86-64+clang+17.0.1+(Compiler+%231)',t:'0')),k:29.64565492670573,l:'4',n:'0',o:'',s:0,t:'0')),l:'2',n:'0',o:'',t:'0')),version:4
[ce_template_default]: https://godbolt.org/#g:!((g:!((g:!((h:codeEditor,i:(filename:'1',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,selection:(endColumn:43,endLineNumber:3,positionColumn:43,positionLineNumber:3,selectionStartColumn:43,selectionStartLineNumber:3,startColumn:43,startLineNumber:3),source:'%23include+%3Ciostream%3E%0A%0Atemplate+%3Ctypename+Param,+typename+Return+%3D+Param%3E%0AReturn+add(Param+a,+Param+b)%0A%7B%0A++++return+static_cast%3CReturn%3E(a+%2B+b)%3B%0A%7D%0A%0Aint+main()%0A%7B%0A++++std::cout+%3C%3C+add%3Cfloat%3E(3.1,+4.5)+%3C%3C+std::endl%3B%0A%0A++++return+0%3B%0A%7D'),l:'5',n:'0',o:'C%2B%2B+source+%231',t:'0')),k:48.369926813040586,l:'4',n:'0',o:'',s:0,t:'0'),(g:!((h:compiler,i:(compiler:clang1701,filters:(b:'0',binary:'1',binaryObject:'1',commentOnly:'0',debugCalls:'1',demangle:'0',directives:'0',execute:'0',intel:'0',libraryCode:'0',trim:'1'),flagsViewOpen:'1',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,libs:!(),options:'-Wall+-Wextra+-Wshadow',overrides:!(),selection:(endColumn:1,endLineNumber:1,positionColumn:1,positionLineNumber:1,selectionStartColumn:1,selectionStartLineNumber:1,startColumn:1,startLineNumber:1),source:1),l:'5',n:'0',o:'+x86-64+clang+17.0.1+(Editor+%231)',t:'0')),k:21.984418260253683,l:'4',n:'0',o:'',s:0,t:'0'),(g:!((h:output,i:(compilerName:'x86-64+gcc+12.2',editorid:1,fontScale:14,fontUsePx:'0',j:1,wrap:'1'),l:'5',n:'0',o:'Output+of+x86-64+clang+17.0.1+(Compiler+%231)',t:'0')),k:29.64565492670573,l:'4',n:'0',o:'',s:0,t:'0')),l:'2',n:'0',o:'',t:'0')),version:4
