---
layout: page
title: "Exceptions"
collection: cpp
published: true
---

## Introduction to exceptions

Exceptions in C++ are a powerful mechanism used for handling errors and exceptional conditions that arise during program execution. Rather than allowing errors to propagate unchecked, leading to unpredictable behavior or crashes, exceptions provide a structured way to deal with such situations. They allow programmers to separate error handling code from normal code, enhancing code readability and maintainability.

Exceptions are objects that represent exceptional conditions, such as arithmetic errors (like division by zero), out-of-memory errors, file I/O errors, and many others. When an error occurs, an exception can be thrown, which interrupts the normal flow of the program.

### Benefits of exceptions

- **Separation of concerns**: exceptions allow code that detects an error to be separate from the code that handles it. This separation enhances code clarity and maintainability.
- **Unwinding the call stack**: when an exception is thrown, the call stack is unwound, allowing the program to jump to an appropriate catch block. This enables graceful termination or recovery from errors.
- **Error propagation**: exceptions can propagate up the call stack until they are caught, allowing errors to be handled at an appropriate level in the code.
- **Clearer code flow**: exception handling can make code flow clearer by removing error-checking clutter from the main logic.

### Differences from traditional error handling

- **Explicit error checking**: traditional error handling often involves explicitly checking return values or error codes after each function call that might fail, cluttering the code with error-checking logic.
- **Code clutter**: the code becomes more cluttered with error-checking statements, affecting readability and maintainability.
- **Difficulty in propagation**: handling errors in traditional methods may involve passing error codes or flags through multiple layers of code, making error propagation and handling complex.

## Exception handling mechanisms


In C++, try and catch blocks are used for handling exceptions (see on [Compiler Explorer][ce_try_catch]):

```cpp
try {
    throw std::out_of_range("invalid range!");
} catch (std::runtime_error& e) {
    std::cout << "Caught a runtime error: " << e.what() << std::endl;
} catch (std::out_of_range& e) {
    std::cout << "Caught a out_of_range error: " << e.what() << std::endl;
} catch (...) {
    std::cout << "Caught an exception" << std::endl;
}
```

The try block encloses the code that might throw an exception. If an exception is thrown within the try block, it's caught by an appropriate catch block that matches the exception type. Multiple catch blocks can be used to handle different types of exceptions. The ellipsis (...) catch block serves as a catch-all for any exceptions not caught by preceding blocks.

The try and catch blocks can also be nested, allowing exception handling at different levels (see on [Compiler Explorer][ce_nested]):

```cpp
try {
    try {
        throw std::out_of_range("invalid range!");
    } catch (...) {
        std::cout << "Well, there was an exception here!" << std::endl;
    }
} catch (...) {
    std::cout << "Caught an exception" << std::endl;
}
```

An inner try-catch block can handle exceptions specific to its scope. If the inner block doesn’t catch an exception, the outer block can handle it. The nesting structure helps manage exceptions at different levels of code execution.

## Standard exceptions

C++ provides [several standard exception][cppref_stdexcept] classes in the `<stdexcept>` header:
- `std::exception`: the base class for all standard exceptions
- `std::invalid_argument`: report invalid arguments
- `std::domain_error`: deport domain errors
- `std::length_error`: report attempts to exceed the maximum allowed size
- `std::out_of_range`: report arguments outside expected range
- `std::runtime_error`: indicate conditions only detectable at run time
- `std::range_error`: report range errors in internal computations
- `std::overflow_error`: report arithmetic overflow
- `std::underflow_error`: report arithmetic underflow

Using standard exceptions demonstrates how to handle specific error scenarios (see on [Compiler Explorer][ce_diff_except]):

```cpp
void performOperation(int value) {
    if (value < 0) {
        throw std::invalid_argument("Value must be positive!");
    }
}

int main() {
    try {
        performOperation(-5); // Throws an invalid_argument exception
    } catch (const std::invalid_argument& e) {
        std::cout << "Invalid argument: " << e.what() << std::endl;
    } catch (const std::exception& e) {
        std::cout << "Exception caught: " << e.what() << std::endl;
    }
}
```

The `performOperation` function throws an `std::invalid_argument` exception if the value is negative. In the `main()` function, the exception is caught and handled specifically for `std::invalid_argument`, providing a meaningful error message. The catch-all block ensures catching any other exceptions derived from `std::exception`.

## Exception specifications

[Exception specifications][except_spec] were introduced in earlier C++ versions (`throw()` and `noexcept`) to declare whether a function can throw exceptions.
- throw(): specifies that a function won't throw any exceptions. However, it's not commonly used due to its limitations and lack of enforcement.
- `noexcept`: indicates that a function doesn't throw exceptions, enhancing code optimization possibilities and ensuring better error handling. If a function marked as `noexcept` throws an exception, `std::terminate()` is called.

For example (see on [Compiler Explorer][ce_noexcept]):

```cpp
void no_except() noexcept { }
void yes_except() noexcept { throw std::runtime_error("nope"); }

int main()
{
    no_except();    // All good
    yes_except();   // std::terminate() is called.
}
```

## Custom exception classes


Custom exceptions typically inherit from `std::exception` or its derived classes to maintain consistency and utilize standard exception handling mechanisms. Extending the base exception class allows developers to include additional details specific to the error scenario (see on [Compiler Explorer][ce_custom_exception]):

```cpp
#include <exception>

class CustomException : public std::exception {
private:
    std::string errorMessage;

public:
    CustomException(const std::string& message) : errorMessage(message) {}

    const char* what() const noexcept override {
        return errorMessage.c_str();
    }
};

void performOperation(int value) {
    if (value < 0) {
        throw CustomException("Value must be positive!");
    }
}

int main() {
    try {
        performOperation(-5); // Throws a CustomException
    } catch (const CustomException& e) {
        std::cout << "Custom exception caught: " << e.what() << std::endl;
    } catch (const std::exception& e) {
        std::cout << "Exception caught: " << e.what() << std::endl;
    }
}
```

## Resources

- [Stack unwinding - ibm.com][stack_unwinding]
- [When and how should I use exception handling? - stackoverflow.com][when_to_use]
- [Exceptions and error handling - isocpp.org][except_standard]
- [C++ catch blocks - catch exception by value or reference? - stackoverflow.com][val_or_ref]

[stack_unwinding]: https://www.ibm.com/docs/en/zos/2.3.0?topic=only-stack-unwinding-c
[when_to_use]: https://stackoverflow.com/questions/4506369/when-and-how-should-i-use-exception-handling
[except_standard]: https://isocpp.org/wiki/faq/exceptions
[val_or_ref]: https://stackoverflow.com/questions/2522299/c-catch-blocks-catch-exception-by-value-or-reference
[except_spec]: https://en.cppreference.com/w/cpp/language/noexcept_spec
[cppref_stdexcept]: https://en.cppreference.com/w/cpp/header/stdexcept

[ce_try_catch]: https://godbolt.org/#g:!((g:!((g:!((h:codeEditor,i:(filename:'1',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,selection:(endColumn:6,endLineNumber:14,positionColumn:1,positionLineNumber:6,selectionStartColumn:6,selectionStartLineNumber:14,startColumn:1,startLineNumber:6),source:'%23include+%3Ciostream%3E%0A%23include+%3Cstdexcept%3E%0A%0Aint+main()%0A%7B%0A++++try+%7B%0A++++++++throw+std::out_of_range(%22invalid+range!!%22)%3B%0A++++%7D+catch+(std::runtime_error%26+e)+%7B%0A++++++++std::cout+%3C%3C+%22Caught+a+runtime+error:+%22+%3C%3C+e.what()+%3C%3C+std::endl%3B%0A++++%7D+catch+(std::out_of_range%26+e)+%7B%0A++++++++std::cout+%3C%3C+%22Caught+a+out_of_range+error:+%22+%3C%3C+e.what()+%3C%3C+std::endl%3B%0A++++%7D+catch+(...)+%7B%0A++++++++std::cout+%3C%3C+%22Caught+an+exception%22+%3C%3C+std::endl%3B%0A++++%7D%0A%7D'),l:'5',n:'0',o:'C%2B%2B+source+%231',t:'0')),k:61.01960784313726,l:'4',n:'0',o:'',s:0,t:'0'),(g:!((h:executor,i:(argsPanelShown:'1',compilationPanelShown:'0',compiler:g132,compilerName:'',compilerOutShown:'0',execArgs:'',execStdin:'',fontScale:14,fontUsePx:'0',j:2,lang:c%2B%2B,libs:!(),options:'',overrides:!(),runtimeTools:!(),source:1,stdinPanelShown:'1',wrap:'1'),l:'5',n:'0',o:'Executor+x86-64+gcc+13.2+(C%2B%2B,+Editor+%231)',t:'0')),k:38.98039215686274,l:'4',n:'0',o:'',s:0,t:'0')),l:'2',n:'0',o:'',t:'0')),version:4
[ce_nested]: https://godbolt.org/#g:!((g:!((g:!((h:codeEditor,i:(filename:'1',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,selection:(endColumn:6,endLineNumber:18,positionColumn:1,positionLineNumber:6,selectionStartColumn:6,selectionStartLineNumber:18,startColumn:1,startLineNumber:6),source:'%23include+%3Ciostream%3E%0A%23include+%3Cstdexcept%3E%0A%0Aint+main()%0A%7B%0A++++try+%7B%0A++++++++try+%7B%0A++++++++++++throw+std::out_of_range(%22invalid+range!!%22)%3B%0A++++++++%7D+catch+(...)+%7B%0A++++++++++++std::cout+%3C%3C+%22Well,+there+was+an+exception+here!!%22+%3C%3C+std::endl%3B%0A++++++++%7D%0A++++%7D+catch+(std::runtime_error%26+e)+%7B%0A++++++++std::cout+%3C%3C+%22Caught+a+runtime+error:+%22+%3C%3C+e.what()+%3C%3C+std::endl%3B%0A++++%7D+catch+(std::out_of_range%26+e)+%7B%0A++++++++std::cout+%3C%3C+%22Caught+a+out_of_range+error:+%22+%3C%3C+e.what()+%3C%3C+std::endl%3B%0A++++%7D+catch+(...)+%7B%0A++++++++std::cout+%3C%3C+%22Caught+an+exception%22+%3C%3C+std::endl%3B%0A++++%7D%0A%7D'),l:'5',n:'0',o:'C%2B%2B+source+%231',t:'0')),k:61.01960784313726,l:'4',n:'0',o:'',s:0,t:'0'),(g:!((h:executor,i:(argsPanelShown:'1',compilationPanelShown:'0',compiler:g132,compilerName:'',compilerOutShown:'0',execArgs:'',execStdin:'',fontScale:14,fontUsePx:'0',j:2,lang:c%2B%2B,libs:!(),options:'',overrides:!(),runtimeTools:!(),source:1,stdinPanelShown:'1',wrap:'1'),l:'5',n:'0',o:'Executor+x86-64+gcc+13.2+(C%2B%2B,+Editor+%231)',t:'0')),k:38.98039215686274,l:'4',n:'0',o:'',s:0,t:'0')),l:'2',n:'0',o:'',t:'0')),version:4
[ce_diff_except]: https://godbolt.org/#g:!((g:!((g:!((h:codeEditor,i:(filename:'1',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,selection:(endColumn:2,endLineNumber:20,positionColumn:2,positionLineNumber:20,selectionStartColumn:2,selectionStartLineNumber:20,startColumn:2,startLineNumber:20),source:'%23include+%3Ciostream%3E%0A%23include+%3Cstdexcept%3E%0A%0Avoid+performOperation(int+value)+%7B%0A++++if+(value+%3C+0)+%7B%0A++++++++throw+std::invalid_argument(%22Value+must+be+positive!!%22)%3B%0A++++%7D%0A%7D%0A%0Aint+main()+%7B%0A++++try+%7B%0A++++++++performOperation(-5)%3B+//+Throws+an+invalid_argument+exception%0A++++%7D+catch+(const+std::invalid_argument%26+e)+%7B%0A++++++++std::cout+%3C%3C+%22Invalid+argument:+%22+%3C%3C+e.what()+%3C%3C+std::endl%3B%0A++++%7D+catch+(const+std::exception%26+e)+%7B%0A++++++++std::cout+%3C%3C+%22Exception+caught:+%22+%3C%3C+e.what()+%3C%3C+std::endl%3B%0A++++%7D%0A%0A++++return+0%3B%0A%7D'),l:'5',n:'0',o:'C%2B%2B+source+%231',t:'0')),k:61.01960784313726,l:'4',n:'0',o:'',s:0,t:'0'),(g:!((h:executor,i:(argsPanelShown:'1',compilationPanelShown:'0',compiler:g132,compilerName:'',compilerOutShown:'0',execArgs:'',execStdin:'',fontScale:14,fontUsePx:'0',j:2,lang:c%2B%2B,libs:!(),options:'',overrides:!(),runtimeTools:!(),source:1,stdinPanelShown:'1',wrap:'1'),l:'5',n:'0',o:'Executor+x86-64+gcc+13.2+(C%2B%2B,+Editor+%231)',t:'0')),k:38.98039215686274,l:'4',n:'0',o:'',s:0,t:'0')),l:'2',n:'0',o:'',t:'0')),version:4
[ce_noexcept]: https://godbolt.org/#g:!((g:!((g:!((h:codeEditor,i:(filename:'1',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,selection:(endColumn:1,endLineNumber:1,positionColumn:1,positionLineNumber:1,selectionStartColumn:1,selectionStartLineNumber:1,startColumn:1,startLineNumber:1),source:'%23include+%3Cstdexcept%3E%0A%0Avoid+no_except()+noexcept+%7B+%7D%0Avoid+yes_except()+noexcept+%7B+throw+std::runtime_error(%22nope%22)%3B+%7D%0A%0Aint+main()%0A%7B%0A++++no_except()%3B++++//+All+good%0A++++yes_except()%3B+++//+std::terminate()+is+called.%0A%7D'),l:'5',n:'0',o:'C%2B%2B+source+%231',t:'0')),k:61.01960784313726,l:'4',n:'0',o:'',s:0,t:'0'),(g:!((h:executor,i:(argsPanelShown:'1',compilationPanelShown:'0',compiler:g132,compilerName:'',compilerOutShown:'0',execArgs:'',execStdin:'',fontScale:14,fontUsePx:'0',j:2,lang:c%2B%2B,libs:!(),options:'',overrides:!(),runtimeTools:!(),source:1,stdinPanelShown:'1',wrap:'1'),l:'5',n:'0',o:'Executor+x86-64+gcc+13.2+(C%2B%2B,+Editor+%231)',t:'0')),k:38.98039215686274,l:'4',n:'0',o:'',s:0,t:'0')),l:'2',n:'0',o:'',t:'0')),version:4
[ce_custom_exception]: https://godbolt.org/#g:!((g:!((g:!((h:codeEditor,i:(filename:'1',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,selection:(endColumn:20,endLineNumber:2,positionColumn:20,positionLineNumber:2,selectionStartColumn:20,selectionStartLineNumber:2,startColumn:20,startLineNumber:2),source:'%23include+%3Cexception%3E%0A%23include+%3Ciostream%3E%0A%23include+%3Cstring%3E%0A%0Aclass+CustomException+:+public+std::exception+%7B%0Aprivate:%0A++++std::string+errorMessage%3B%0A%0Apublic:%0A++++CustomException(const+std::string%26+message)+:+errorMessage(message)+%7B%7D%0A%0A++++const+char*+what()+const+noexcept+override+%7B%0A++++++++return+errorMessage.c_str()%3B%0A++++%7D%0A%7D%3B%0A%0Avoid+performOperation(int+value)+%7B%0A++++if+(value+%3C+0)+%7B%0A++++++++throw+CustomException(%22Value+must+be+positive!!%22)%3B%0A++++%7D%0A%7D%0A%0Aint+main()+%7B%0A++++try+%7B%0A++++++++performOperation(-5)%3B+//+Throws+a+CustomException%0A++++%7D+catch+(const+CustomException%26+e)+%7B%0A++++++++std::cout+%3C%3C+%22Custom+exception+caught:+%22+%3C%3C+e.what()+%3C%3C+std::endl%3B%0A++++%7D+catch+(const+std::exception%26+e)+%7B%0A++++++++std::cout+%3C%3C+%22Exception+caught:+%22+%3C%3C+e.what()+%3C%3C+std::endl%3B%0A++++%7D%0A%7D'),l:'5',n:'0',o:'C%2B%2B+source+%231',t:'0')),k:61.01960784313726,l:'4',n:'0',o:'',s:0,t:'0'),(g:!((h:executor,i:(argsPanelShown:'1',compilationPanelShown:'0',compiler:g132,compilerName:'',compilerOutShown:'0',execArgs:'',execStdin:'',fontScale:14,fontUsePx:'0',j:2,lang:c%2B%2B,libs:!(),options:'',overrides:!(),runtimeTools:!(),source:1,stdinPanelShown:'1',wrap:'1'),l:'5',n:'0',o:'Executor+x86-64+gcc+13.2+(C%2B%2B,+Editor+%231)',t:'0')),k:38.98039215686274,l:'4',n:'0',o:'',s:0,t:'0')),l:'2',n:'0',o:'',t:'0')),version:4