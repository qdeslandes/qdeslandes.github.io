---
layout: page
title:  "Introduction to C++"
collection: cpp
---

## C++ as a language

<center>
    <a title="Julia Kryuchkova, CC BY-SA 2.5 &lt;https://creativecommons.org/licenses/by-sa/2.5&gt;, via Wikimedia Commons" href="https://commons.wikimedia.org/wiki/File:Bjarne-stroustrup_(cropped).jpg">
        <figure style="float: right" >
            <img width="200" style="padding: 20px;" alt="Bjarne-stroustrup (cropped)" src="https://upload.wikimedia.org/wikipedia/commons/thumb/0/0e/Bjarne-stroustrup_%28cropped%29.jpg/256px-Bjarne-stroustrup_%28cropped%29.jpg">
            <figcaption>Bjarne Stroustrup</figcaption>
        </figure>
    </a>
</center>

[C++][cpp_wiki] is a powerful and versatile programming language with a rich history and wide-ranging applications. Created by Danish computer scientist [Bjarne Stroustrup][bjarne_stroustrup_wiki] in 1985, C++ began as an extension to the C programming language. Over the years, it evolved to include object-oriented, generic, and functional features, making it one of the most influential languages in the world of programming.

C++ underwent standardization by the International Organization for Standardization (ISO) in 1998, ([C++98][cpp_98_std]). Subsequent amendments in 2003 ([C++03][cpp_03_std]), 2011 ([C++11][cpp_11_std]), 2014 ([C++14][cpp_14_std]), 2017 ([C++17][cpp_17_std]), and 2020 ([C++20][cpp_20_std]) brought significant enhancements to the language. The next release is expected to be [C++23][cpp_23_std], to be released in December 2023.

C++ finds applications in a wide variety of contexts, from system software to video games, desktop applications, databases, web browsers, and the financial sector. Its versatility is one of its hallmarks, offering high-level functionalities, an extensive standard library, and support for object-oriented programming. What sets C++ apart is its ability to provide performance that rivals, if not surpasses, lower-level languages, thanks to its capabilities for low-level memory management and optimization.

## Learning C++, as a C developer

Let's summarise the main differences between C and C++, from a C developer point of view.

### `nullptr` vs. `NULL`

In C, you might be familiar with the use of [`NULL`][null_cppref], which is a macro defined in various headers of the standard library as `(void *)0`. In C++, there's a more precise alternative called [`nullptr`][nullptr_cppref] (see on [Compiler Explorer][ce_nullptr]). This change reflects C++'s commitment to type safety and clarity in code.

In C++, `nullptr` is a keyword specifically designed to represent a null pointer. Unlike `NULL`, which is essentially just an integer value (`0`) cast to a pointer, `nullptr` has its own distinct type: [`std::nullptr_t`][nullptr_t_cppref]. This type provides a safer and more specific way to express the absence of a valid pointer.

One significant advantage of `nullptr` is that it's implicitly convertible to any pointer type. This means you can use `nullptr` with pointers of different types without worrying about type casting or compatibility issues. For example, you can assign `nullptr` to a pointer to an integer, a pointer to a class object, or any other pointer type without encountering type mismatch problems.

However, `nullptr` is not convertible to integer types, which further ensures type safety. This is in contrast to `NULL`, which could potentially be used in confusing ways, as it's essentially an integer value.

### Includes

When working with C++ and its standard library, you'll notice that the headers included in your code don't have file extensions. For example, to use `std::cout`, you would include the header like this:

```cpp
#include <iostream>
```

It's important to note that the absence of file extensions, like `.h`, in standard library headers is a distinct characteristic of C++. In contrast, C++ projects that define their own headers would contain file extensions, such as `.h` or `.hh`.

The exact reason for this difference is [not](https://stackoverflow.com/questions/901216/why-stl-header-files-have-no-extension) entirely [clear](https://stackoverflow.com/questions/441568/when-can-you-omit-the-file-extension-in-an-include-directive/441683#441683) and can vary based on historical and practical considerations.

### Comments

While C89 only supports `/* */`, C99+ and C++ supports `//` and `/* */` comments.

### Booleans

In C, the concept of a boolean type was formally introduced in [C99][c99_wiki]. The boolean type is named `_Bool`. To make it more user-friendly, the C standard library header [`stdbool.h`][stdbool_cppref] redefines `_Bool` as `bool`. In this context, `true` and `false` are macros representing `1` and `0`, respectively.

With the upcoming C23 standard, a significant change is on the horizon. `bool` will become the default boolean type, replacing `_Bool`, and `true` and `false` will transition from macros to official keywords. This change aims to improve clarity and consistency in C code.

In C++, on the other hand, `bool` is a primary type. It is designed to work seamlessly with boolean values and expressions. Unlike C, C++ doesn't rely on macros for `true` and `false`. These values are built-in constants, making the code more intuitive and type-safe.

Additionally, C++ provides features like [`std::boolalpha`](https://en.cppreference.com/w/cpp/io/manip/boolalpha) (see on [Compiler Explorer][ce_boolalpha]), a manipulator that allows you to format `bool` values as strings.

### Type inference

Type inference is a valuable language feature that allows the compiler or interpreter to automatically determine the data type of a variable or expression. While this feature is not available in C, in C++, it empowers developers to write more concise and maintainable code.

For example, instead of explicitly defining a type for a variable, you can use [`auto`][cpp_auto_cppref], as shown in this code snippet:

```cpp
extern std::vector<unsigned long long> fibonacci(unsigned char limit);
auto results = fibonacci(45);
```

In this case, the compiler automatically infers the type of `results` as `std::vector<unsigned long long>`, making the code more readable and reducing redundancy.

> **But `auto` exists in C, [it's a valid keyword][c_auto_cppref]!**
>
> C does have the `auto` keyword, but its purpose differs from its use in C++. It refers to the default behavior when declaring a variable, indicating automatic duration and no linkage.

In the context of "when should I use type inference," there is no one-size-fits-all answer. It largely depends on your coding style and the clarity of your code. The key is to use type inference judiciously, aiming to keep your code clear and your intent explicit. When it enhances code readability and maintainability, using `auto` and type inference in C++ can be a powerful tool.


### Namespaces

Namespaces are a valuable mechanism used to encapsulate symbols and prevent naming conflicts in code. They offer a means of organizing and categorizing code elements. Let's explore how namespaces work and their applications in C++.

In C, there is no native support for namespaces. As a result, all symbols are defined in a single global namespace. This limitation can lead to naming clashes when different code elements share the same names.

For example, consider this C code snippet (see on [Compiler Explorer][ce_c_name_clash]):

```c
int my_function(int a);
void my_function(unsigned long a);
```

Both `int my_function(int a)` and `void my_function(unsigned long a)` are defined within the same global namespace, resulting in a naming conflict.

In contrast, C++ offers a robust namespace feature that allows you to define multiple namespaces. This effectively isolates symbols within those namespaces, reducing the likelihood of naming conflicts. Here's an example of C++ code using namespaces (see on [Compiler Explorer][ce_cpp_namespaces]):

```cpp
namespace a
{
    int my_function(int a)
    {
        return a;
    }
};

namespace b
{
    void my_function(unsigned long a)
    {
        std::cout << a << std::endl;
    }
}
```

By defining symbols within distinct namespaces, you can prevent naming clashes. While it's still possible to have conflicts within the same namespace, developers often use their own namespaces for specific projects, giving them control over naming and the ability to resolve potential clashes.

To use symbols from a given namespace, there are several approaches:

- **Direct Use**: You can directly call a symbol by prefixing it with its namespace. For example, `std::cout`. If namespaces are nested, you need to provide the full path to the symbol, such as `foo::bar::myFunction`.

- **`using namespace`**: You can import an entire namespace into the current namespace, as in `using namespace std;`. However, this approach is not recommended because it brings all the symbols from the imported namespace into the current namespace, potentially creating clashes. In this case, `std::cout` could be called using `cout` only.

- **`using symbol`**: You can import a specific symbol from a namespace into the current namespace to avoid the need for multiple namespace prefixes and prevent namespace pollution. For example, `using std::cout;` imports the `cout` symbol.

Additionally, namespaces can be aliased to simplify lengthy calls to symbols within nested namespaces:

```cpp
namespace foo {
    namespace bar {
        namespace baz {
            void print() {}
        };
    };
};

namespace fbz = foo::bar::baz;

int main() {
    foo::bar::baz::print(); // Using full path
    fbz::print();           // Using the alias
}
```

### Input and output operations with `<iostream>`

In C++, [`<iostream>`][iostream_cppref] is a standard library header that provides essential functions for basic input and output operations. These operations are crucial for interacting with the user and displaying information. This header introduces three primary stream objects: `std::cout`, `std::cerr`, and `std::cin`, which are associated with the standard output (`stdout`), standard error (`stderr`), and standard input (`stdin`), respectively.

Here's an example of how these stream objects are used (see on [Compiler Explorer][ce_iostream]):

```cpp
#include <iostream>

int main() {
    int a, b;

    // Printing to stdout using std::cout
    std::cout << "Enter 2 values" << std::endl;

    // Reading from stdin using std::cin
    std::cin >> a >> b;

    // Printing to stderr using std::cerr
    std::cerr << "Values are: " << a << " and " << b << std::endl;
}
```

The stream operators (`<<` and `>>`) play a central role in these operations. They allow you to insert data into output streams and extract data from input streams.

Additionally, C++ provides a special stream manipulator called [`std::endl`][std_endl_cppref]. It not only inserts a newline character (`\n`) into the stream but also flushes the stream's buffer. Flushing ensures that the data is immediately written to the output, which can be particularly useful for real-time or interactive applications.

In the example, `std::endl` is used to add a newline and flush the buffer when displaying the values with `std::cerr`.

### Overload

Function overloading in C++ allows you to define multiple functions with the same name but different parameters. This provides flexibility and code clarity by allowing functions with the same name to operate on different types or numbers of arguments.

Here's a C++ example of function overloading (see on [Compiler Explorer][ce_overload]):

```cpp
int add(int a, int b) {
    return a + b;
}

std::string add(std::string str1, std::string str2) {
    return str1 + str2;
}
```

In C++, function overloads are resolved based on the function's signature, which includes the function name, [cv-qualifiers][cv_cppref], and parameter types. This allows you to create functions with the same name but distinct parameter lists, enabling more intuitive and type-safe code.

In contrast, C offers a somewhat similar feature through [`_Generic`][c_generic_cppref]. For example ([see on Compiler Explorer][ce_generic]):

```c
#define print_value(v)        \
    _Generic((v),             \
        int: print_int,       \
        double: print_double)

void print_int(int v) {
    printf("%d", v);
}

void print_double(double v) {
    printf("%ld", v);
}
```

In this C example, `_Generic` checks the type of its argument and selects the corresponding expression from the list of types provided. This allows for the selection of the correct function based on the type of `v`. However, C's `_Generic` is not as flexible or intuitive as C++'s function overloading, as it requires explicit enumeration of the types.

One notable difference is that in C++, function overloads are resolved based on the function's signature, which includes parameter types but not the return type. In C++, two functions with the same parameter types, but different return types are not considered overloads and may lead to compilation errors.

### Default argument

> **Argument or parameter?**
>
> A parameter is the variable which is part of the function's signature (the declaration of the function), while an argument is an expression used when calling the function.

C++ supports default arguments, which allow you to provide default values for function parameters. When calling a function, you are not obligated to pass values for parameters with default arguments; if you omit them, the default values are used.

Here's an example of how to define and use default arguments in C++ (see on [Compiler Explorer][ce_def_arg]):

```cpp
int multiply(int a, int b = 2) {
    return a * b;
}
```

In this example, `multiply` has a default argument for the parameter `b`, which is set to `2`. When the function is called with `b` missing, the default value of `2` is automatically used.

It's important to note that when declaring a function with default arguments, those parameters with default values should come after any parameters without default values. This ensures that the function call remains unambiguous, allowing you to specify values for non-default parameters if needed.

### Pointers or references?

In C++, a reference is essentially an alias or an alternative name for an existing variable. References are declared using the ampersand symbol (`&`) and **must** be initialized at the point of declaration. They are a powerful feature that enhances code clarity and can simplify certain programming tasks.

Here's an example of how references work in C++ (see on [Compiler Explorer][ce_ref]):

```cpp
int a = 32;
int &a_ref = a;
```

In this code, `a_ref` is a reference to `a`. It essentially points to the same value in memory and shares the same address. Any modifications made to `a_ref` will directly affect the value of `a`.

References are often used as function arguments, especially when working with large data structures, to avoid the overhead of copying data. For instance (see on [Compiler Explorer][ce_function_ref]):

```cpp
int myComputation(struct BigStruct &s) {
    return s.field0 + s.field1;
}
```

In this example, `s` is passed to the `myComputation` function by reference. This means that the function operates on the original `BigStruct` data, making it more efficient than copying the entire structure.

One key distinction between references and pointers is that there's no need to dereference references using `*`, as you would with pointers. References provide a more straightforward and convenient way to work with existing data without the need for explicit pointer dereferencing.

## Writing modern C++ code

When transitioning from C to C++, it's important to do more than just adjust the syntax; it's about embracing the features and best practices that make C++ a powerful and expressive language. "Modern C++" refers to writing C++ code that takes full advantage of the language's features and follows best practices. It often encompasses the changes introduced in C++11 and later standards.

Here are some key aspects of writing modern C++ code:

1. **RAII (Resource Acquisition Is Initialization)**: RAII is a fundamental concept in modern C++. It involves binding the lifecycle of a resource, such as memory or file handles, to the lifetime of an object. By using classes and objects to manage resources, you ensure that resources are automatically acquired when an object is created and released when it goes out of scope. This prevents resource leaks and simplifies resource management.

2. **Standard library containers and algorithms**: Modern C++ encourages the use of standard library containers like `std::vector`, `std::map`, and `std::set`, along with algorithms provided by the Standard Template Library (STL). These containers and algorithms are efficient, well-tested, and provide a high level of abstraction, making code more readable and maintainable.

3. **Templates and the STL**: Templates allow you to write generic code that works with different data types. The Standard Template Library (STL) is a powerful part of modern C++, providing a collection of template classes and functions for common data structures and algorithms. It encourages code reuse and type safety.

4. **Smart pointers**: Smart pointers, such as `std::shared_ptr`, `std::unique_ptr`, and `std::weak_ptr`, are a safer and more modern alternative to manual memory management. They automatically manage the lifetime of dynamically allocated objects, helping to prevent memory leaks and use-after-free errors.

To further enhance your C++ coding skills and ensure you're following best practices, you can refer to the [C++ Core Guidelines](https://isocpp.github.io/CppCoreGuidelines/). These guidelines provide a comprehensive set of recommendations to help developers write clean, maintainable, and efficient C++ code. Following these guidelines can lead to code that is not only more robust but also easier to understand and collaborate on with other developers.

## Resources

- [Function Overloading - learn.microsoft.com](https://learn.microsoft.com/en-us/cpp/cpp/function-overloading?view=msvc-170)
- [C++ ISO - isocpp.org](https://isocpp.org/std/the-standard)

[cpp_wiki]: https://en.wikipedia.org/wiki/C%2B%2B
[bjarne_stroustrup_wiki]: https://en.wikipedia.org/wiki/Bjarne_Stroustrup
[cpp_98_std]: https://www.lirmm.fr/~ducour/Doc-objets/ISO+IEC+14882-1998.pdf
[cpp_03_std]: https://web.archive.org/web/20180922024431/https://cs.nyu.edu/courses/fall11/CSCI-GA.2110-003/documents/c++2003std.pdf
[cpp_11_std]: https://www.open-std.org/jtc1/sc22/wg21/docs/papers/2012/n3337.pdf
[cpp_14_std]: https://github.com/cplusplus/draft/blob/main/papers/n4140.pdf
[cpp_17_std]: https://www.open-std.org/jtc1/sc22/wg21/docs/papers/2017/n4659.pdf
[cpp_20_std]: https://isocpp.org/files/papers/N4860.pdf
[cpp_23_std]: https://open-std.org/jtc1/sc22/wg21/docs/papers/2023/n4950.pdf
[null_cppref]: https://en.cppreference.com/w/cpp/types/NULL
[nullptr_cppref]: https://en.cppreference.com/w/cpp/language/nullptr
[nullptr_t_cppref]: https://en.cppreference.com/w/cpp/types/nullptr_t
[c99_wiki]: https://en.cppreference.com/w/c/language/arithmetic_types#Boolean_type
[stdbool_cppref]: https://en.cppreference.com/w/c/types/boolean
[cpp_auto_cppref]: https://en.cppreference.com/w/cpp/language/auto
[c_auto_cppref]: https://en.cppreference.com/w/c/language/storage_duration
[iostream_cppref]: https://en.cppreference.com/w/cpp/header/iostream
[std_endl_cppref]: https://en.cppreference.com/w/cpp/io/manip/endl
[cv_cppref]: https://en.cppreference.com/w/cpp/language/cv
[c_generic_cppref]: https://en.cppreference.com/w/c/language/generic

[ce_nullptr]: https://godbolt.org/#g:!((g:!((g:!((h:codeEditor,i:(filename:'1',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,selection:(endColumn:22,endLineNumber:5,positionColumn:22,positionLineNumber:5,selectionStartColumn:22,selectionStartLineNumber:5,startColumn:22,startLineNumber:5),source:'%23include+%3Ciostream%3E%0A%0Aint+main()%0A%7B%0A++++int+*a+%3D+nullptr%3B%0A%0A++++std::cout+%3C%3C+a+%3C%3C+std::endl%3B%0A%7D'),l:'5',n:'0',o:'C%2B%2B+source+%231',t:'0')),k:50,l:'4',n:'0',o:'',s:0,t:'0'),(g:!((h:executor,i:(argsPanelShown:'1',compilationPanelShown:'1',compiler:g132,compilerName:'',compilerOutShown:'0',execArgs:'',execStdin:'',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,libs:!(),options:'',overrides:!(),source:1,stdinPanelShown:'1',wrap:'1'),l:'5',n:'0',o:'Executor+x86-64+gcc+13.2+(C%2B%2B,+Editor+%231)',t:'0')),header:(),k:50,l:'4',n:'0',o:'',s:0,t:'0')),l:'2',n:'0',o:'',t:'0')),version:4
[ce_boolalpha]: https://godbolt.org/#z:OYLghAFBqd5QCxAYwPYBMCmBRdBLAF1QCcAaPECAMzwBtMA7AQwFtMQByARg9KtQYEAysib0QXACx8BBAKoBnTAAUAHpwAMvAFYTStJg1DIApACYAQuYukl9ZATwDKjdAGFUtAK4sGIM6SuADJ4DJgAcj4ARpjEEtIADqgKhE4MHt6%2B/qRJKY4CIWGRLDFxUraY9vkMQgRMxAQZPn4BdpgOabX1BIUR0bHxtnUNTVmtwz2hfSUDUgCUtqhexMjsHOYAzKHI3lgA1CYbbk4Kh9gmGgCCm9u7mAdHJwTEmKxnF9dXoQR7LEyhEDmHxMAHYrFc9pC9goCOgQCglj9Dm5kQczGYsFQmF5aD9nl52GizA8UUc9vj7sjUWB1gBWNwMGkfKEskmo8wYzBYnE/LG0JQgIlssl8pTCtx7Jn0xnrCGsyFUskwuEgKKoTxiBIIJgHOXyxUSjlqjW0LU6imCjni8nEAnWqUMpl61kGonGgym7V7UWEq2un32umO2WXeUKo6o5Xwhiod2ar3MsOujkxuOe82233o60UwMmaVO0NJiNklOx9Ues3esQCoX%2BmuUksSh0yw7g64ggAiHAWtE4tN4fg4WlIqE4KMs1mhSxWlLMGx4pAImh7CwA1iBaRp9JxJIOV6POLwFCBt8vhz3SHBYEhMKp2l4iGQKBB6sAFMpDJUhAhUAB3IdFzQFgEjoJhqk/MJaB/f8hxHYDQPoOJgC4DYAgQuhYnCVg1l4DCkIAeUfGCAIPO92kuYh3yPQJ72QWp8CHXh%2BEEEQxHYcoWPkJQ1APXQuH0QxjGsax9DwKIT0gBZUASaoTw4XhUAAN1iYg8CwSTAUWZZVj0ZVQkg79f1I7heGeTB2G3P9iCYBJOB4Xt%2B33C9Dw4bA6MfEg9lUAAOAA2ABaPzJD2YBkGQPZUIAOmJCAJysSxSD2XBCC8zYuDmMyVzmddN23PsOD3Ug4MUmiTzPbLHI4MxnJHMcFKXSrSBU4gUmcSQgA%3D%3D%3D
[ce_c_name_clash]: https://godbolt.org/#g:!((g:!((g:!((h:codeEditor,i:(filename:'1',fontScale:14,fontUsePx:'0',j:1,lang:___c,selection:(endColumn:1,endLineNumber:19,positionColumn:1,positionLineNumber:19,selectionStartColumn:1,selectionStartLineNumber:19,startColumn:1,startLineNumber:19),source:'%23include+%3Cstdio.h%3E%0A%0Aint+my_function(int+a)%0A%7B%0A++++return+a%3B%0A%7D%0A%0Avoid+my_function(unsigned+long+a)%0A%7B%0A++++printf(%22%25l%5Cn%22,+a)%3B%0A%7D%0A%0Aint+main(void)%0A%7B%0A++++printf(%22%25d%5Cn%22,+my_function(3))%3B%0A++++my_function(4UL)%3B%0A++++return+0%3B%0A%7D%0A'),l:'5',n:'0',o:'C+source+%231',t:'0')),k:50,l:'4',n:'0',o:'',s:0,t:'0'),(g:!((h:executor,i:(argsPanelShown:'1',compilationPanelShown:'0',compiler:cg132,compilerName:'',compilerOutShown:'0',execArgs:'',execStdin:'',fontScale:14,fontUsePx:'0',j:1,lang:___c,libs:!(),options:'',overrides:!(),source:1,stdinPanelShown:'1',tree:0,wrap:'1'),l:'5',n:'0',o:'Executor+x86-64+gcc+13.2+(C,+Editor+%231)',t:'0')),k:50,l:'4',n:'0',o:'',s:0,t:'0')),l:'2',n:'0',o:'',t:'0')),version:4
[ce_cpp_namespaces]: https://godbolt.org/#g:!((g:!((g:!((h:codeEditor,i:(filename:'1',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,selection:(endColumn:1,endLineNumber:23,positionColumn:1,positionLineNumber:23,selectionStartColumn:1,selectionStartLineNumber:23,startColumn:1,startLineNumber:23),source:'%23include+%3Ciostream%3E%0A%0Anamespace+a%0A%7B%0A++++int+my_function(int+a)%0A++++%7B%0A++++++++return+a%3B%0A++++%7D%0A%7D%3B%0A%0Anamespace+b%0A%7B%0A++++void+my_function(unsigned+long+a)%0A++++%7B%0A++++++++std::cout+%3C%3C+a+%3C%3C+std::endl%3B%0A++++%7D%0A%7D%0A%0Aint+main()%0A%7B%0A++++return+a::my_function(3)%3B%0A%7D%0A'),l:'5',n:'0',o:'C%2B%2B+source+%231',t:'0')),k:50,l:'4',n:'0',o:'',s:0,t:'0'),(g:!((h:executor,i:(argsPanelShown:'1',compilationPanelShown:'0',compiler:g132,compilerName:'',compilerOutShown:'0',execArgs:'',execStdin:'',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,libs:!(),options:'',overrides:!(),source:1,stdinPanelShown:'1',tree:0,wrap:'1'),l:'5',n:'0',o:'Executor+x86-64+gcc+13.2+(C%2B%2B,+Editor+%231)',t:'0')),k:50,l:'4',n:'0',o:'',s:0,t:'0')),l:'2',n:'0',o:'',t:'0')),version:4
[ce_iostream]: https://godbolt.org/#g:!((g:!((g:!((h:codeEditor,i:(filename:'1',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,selection:(endColumn:1,endLineNumber:15,positionColumn:1,positionLineNumber:15,selectionStartColumn:1,selectionStartLineNumber:15,startColumn:1,startLineNumber:15),source:'%23include+%3Ciostream%3E%0A%0Aint+main()+%7B%0A++++int+a,+b%3B%0A%0A++++//+Printing+to+stdout+using+std::cout%0A++++std::cout+%3C%3C+%22Enter+2+values%22+%3C%3C+std::endl%3B%0A%0A++++//+Reading+from+stdin+using+std::cin%0A++++std::cin+%3E%3E+a+%3E%3E+b%3B%0A%0A++++//+Printing+to+stderr+using+std::cerr%0A++++std::cerr+%3C%3C+%22Values+are:+%22+%3C%3C+a+%3C%3C+%22+and+%22+%3C%3C+b+%3C%3C+std::endl%3B%0A%7D%0A'),l:'5',n:'0',o:'C%2B%2B+source+%231',t:'0')),k:50,l:'4',n:'0',o:'',s:0,t:'0'),(g:!((h:executor,i:(argsPanelShown:'1',compilationPanelShown:'0',compiler:g132,compilerName:'',compilerOutShown:'0',execArgs:'',execStdin:'',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,libs:!(),options:'',overrides:!(),source:1,stdinPanelShown:'1',tree:0,wrap:'1'),l:'5',n:'0',o:'Executor+x86-64+gcc+13.2+(C%2B%2B,+Editor+%231)',t:'0')),k:50,l:'4',n:'0',o:'',s:0,t:'0')),l:'2',n:'0',o:'',t:'0')),version:4
[ce_overload]: https://godbolt.org/#g:!((g:!((g:!((h:codeEditor,i:(filename:'1',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,selection:(endColumn:2,endLineNumber:11,positionColumn:1,positionLineNumber:4,selectionStartColumn:2,selectionStartLineNumber:11,startColumn:1,startLineNumber:4),source:'%23include+%3Ciostream%3E%0A%0A//+Function+to+add+two+integers%0Aint+add(int+a,+int+b)+%7B%0A++++return+a+%2B+b%3B%0A%7D%0A%0A//+Function+to+concatenate+two+strings%0Astd::string+add(std::string+str1,+std::string+str2)+%7B%0A++++return+str1+%2B+str2%3B%0A%7D%0A%0Aint+main()+%7B%0A++++int+num1+%3D+5,+num2+%3D+3%3B%0A++++std::string+text1+%3D+%22Hello,+%22%3B%0A++++std::string+text2+%3D+%22world!!%22%3B%0A%0A++++int+result1+%3D+add(num1,+num2)%3B+++++++++++++//+Calls+the+first+overload%0A++++std::string+result2+%3D+add(text1,+text2)%3B+++//+Calls+the+second+overload%0A%0A++++std::cout+%3C%3C+%22Result+of+integer+addition:+%22+%3C%3C+result1+%3C%3C+std::endl%3B%0A++++std::cout+%3C%3C+%22Result+of+string+concatenation:+%22+%3C%3C+result2+%3C%3C+std::endl%3B%0A%0A++++return+0%3B%0A%7D'),l:'5',n:'0',o:'C%2B%2B+source+%231',t:'0')),k:50,l:'4',n:'0',o:'',s:0,t:'0'),(g:!((h:executor,i:(argsPanelShown:'1',compilationPanelShown:'0',compiler:g132,compilerName:'',compilerOutShown:'0',execArgs:'',execStdin:'',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,libs:!(),options:'',overrides:!(),source:1,stdinPanelShown:'1',tree:0,wrap:'1'),l:'5',n:'0',o:'Executor+x86-64+gcc+13.2+(C%2B%2B,+Editor+%231)',t:'0')),k:50,l:'4',n:'0',o:'',s:0,t:'0')),l:'2',n:'0',o:'',t:'0')),version:4
[ce_generic]: https://godbolt.org/#g:!((g:!((g:!((h:codeEditor,i:(filename:'1',fontScale:14,fontUsePx:'0',j:1,lang:___c,selection:(endColumn:24,endLineNumber:3,positionColumn:24,positionLineNumber:3,selectionStartColumn:24,selectionStartLineNumber:3,startColumn:24,startLineNumber:3),source:'%23include+%3Cstdio.h%3E%0A%0A%23define+print_value(v)++++++++%5C%0A++++_Generic((v),+++++++++++++%5C%0A++++++++int:+print_int,+++++++%5C%0A++++++++double:+print_double)%0A%0Avoid+print_int(int+v)+%7B%0A++++printf(%22%25d%5Cn%22,+v)%3B%0A%7D%0A%0Avoid+print_double(double+v)+%7B%0A++++printf(%22%25ld%5Cn%22,+v)%3B%0A%7D%0A%0Aint+main(void)+%7B%0A++++print_value(3)%3B%0A++++print_value(3.6)%3B%0A++++return+0%3B%0A%7D'),l:'5',n:'0',o:'C+source+%231',t:'0')),k:50,l:'4',n:'0',o:'',s:0,t:'0'),(g:!((h:executor,i:(argsPanelShown:'1',compilationPanelShown:'0',compiler:cg132,compilerName:'',compilerOutShown:'0',execArgs:'',execStdin:'',fontScale:14,fontUsePx:'0',j:2,lang:___c,libs:!(),options:'',overrides:!(),source:1,stdinPanelShown:'1',wrap:'1'),l:'5',n:'0',o:'Executor+x86-64+gcc+13.2+(C,+Editor+%231)',t:'0')),k:50,l:'4',n:'0',o:'',s:0,t:'0')),l:'2',n:'0',o:'',t:'0')),version:4
[ce_def_arg]: https://godbolt.org/#g:!((g:!((g:!((h:codeEditor,i:(filename:'1',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,selection:(endColumn:2,endLineNumber:11,positionColumn:2,positionLineNumber:11,selectionStartColumn:2,selectionStartLineNumber:11,startColumn:2,startLineNumber:11),source:'%23include+%3Ciostream%3E%0A%0Aint+multiply(int+a,+int+b+%3D+2)+%7B%0A++++return+a+*+b%3B%0A%7D%0A%0Aint+main()%0A%7B%0A++++std::cout+%3C%3C+%223+*+default+%3D+%22+%3C%3C+multiply(3)+%3C%3C+std::endl%3B%0A++++std::cout+%3C%3C+%224+*+10+%3D+%22+%3C%3C+multiply(4,+10)+%3C%3C+std::endl%3B%0A%7D'),l:'5',n:'0',o:'C%2B%2B+source+%231',t:'0')),k:50,l:'4',n:'0',o:'',s:0,t:'0'),(g:!((h:executor,i:(argsPanelShown:'1',compilationPanelShown:'0',compiler:g132,compilerName:'',compilerOutShown:'0',execArgs:'',execStdin:'',fontScale:14,fontUsePx:'0',j:2,lang:c%2B%2B,libs:!(),options:'',overrides:!(),source:1,stdinPanelShown:'1',wrap:'1'),l:'5',n:'0',o:'Executor+x86-64+gcc+13.2+(C%2B%2B,+Editor+%231)',t:'0')),k:50,l:'4',n:'0',o:'',s:0,t:'0')),l:'2',n:'0',o:'',t:'0')),version:4
[ce_ref]: https://godbolt.org/#g:!((g:!((g:!((h:codeEditor,i:(filename:'1',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,selection:(endColumn:8,endLineNumber:9,positionColumn:8,positionLineNumber:9,selectionStartColumn:8,selectionStartLineNumber:9,startColumn:8,startLineNumber:9),source:'%23include+%3Ciostream%3E%0A%0Aint+main()%0A%7B%0A++++int+a+%3D+3%3B%0A++++int+%26a_ref+%3D+a%3B%0A%0A++++//+The+following+will+emit+a+compilation+error%0A++++//+int+%26b+%3D+0%3B%0A%0A++++std::cout+%3C%3C+%22a%3D%22+%3C%3C+a+%3C%3C+%22+%26a%3D%22+%3C%3C+%26a+%3C%3C+std::endl%3B%0A++++std::cout+%3C%3C+%22a_ref%3D%22+%3C%3C+a_ref+%3C%3C+%22+%26a_ref%3D%22+%3C%3C+%26a_ref+%3C%3C+std::endl%3B%0A%7D'),l:'5',n:'0',o:'C%2B%2B+source+%231',t:'0')),k:50,l:'4',n:'0',o:'',s:0,t:'0'),(g:!((h:executor,i:(argsPanelShown:'1',compilationPanelShown:'0',compiler:g132,compilerName:'',compilerOutShown:'0',execArgs:'',execStdin:'',fontScale:14,fontUsePx:'0',j:2,lang:c%2B%2B,libs:!(),options:'',overrides:!(),source:1,stdinPanelShown:'1',wrap:'1'),l:'5',n:'0',o:'Executor+x86-64+gcc+13.2+(C%2B%2B,+Editor+%231)',t:'0')),k:50,l:'4',n:'0',o:'',s:0,t:'0')),l:'2',n:'0',o:'',t:'0')),version:4
[ce_function_ref]: https://godbolt.org/#g:!((g:!((g:!((h:codeEditor,i:(filename:'1',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,selection:(endColumn:2,endLineNumber:21,positionColumn:2,positionLineNumber:21,selectionStartColumn:2,selectionStartLineNumber:21,startColumn:2,startLineNumber:21),source:'%23include+%3Ciostream%3E%0A%0Astruct+BigStruct%0A%7B%0A++++int+field0%3B%0A++++int+field1%3B%0A%7D%3B%0A%0Aint+myComputation(struct+BigStruct+%26s)+%7B%0A++++return+s.field0+%2B+s.field1%3B%0A%7D%0A%0Aint+main()%0A%7B%0A++++struct+BigStruct+myStruct+%3D+%7B%0A++++++++.field0+%3D+1,%0A++++++++.field1+%3D+2%0A++++%7D%3B%0A%0A++++std::cout+%3C%3C+myComputation(myStruct)+%3C%3C+std::endl%3B%0A%7D'),l:'5',n:'0',o:'C%2B%2B+source+%231',t:'0')),k:50,l:'4',n:'0',o:'',s:0,t:'0'),(g:!((h:executor,i:(argsPanelShown:'1',compilationPanelShown:'0',compiler:g132,compilerName:'',compilerOutShown:'0',execArgs:'',execStdin:'',fontScale:14,fontUsePx:'0',j:2,lang:c%2B%2B,libs:!(),options:'',overrides:!(),source:1,stdinPanelShown:'1',wrap:'1'),l:'5',n:'0',o:'Executor+x86-64+gcc+13.2+(C%2B%2B,+Editor+%231)',t:'0')),k:50,l:'4',n:'0',o:'',s:0,t:'0')),l:'2',n:'0',o:'',t:'0')),version:4
