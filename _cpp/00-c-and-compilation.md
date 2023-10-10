---
layout: page
title:  "C and compilation"
collection: cpp
index: 0
---

## Let's start with C

[According to the C standard](http://port70.net/%7Ensz/c/c11/n1570.html#5.1.2.2.1), a valid C `main()` function has the following signature (prototype):

```c
int main(void);
// or
int main(int argc, char *argv[]);
```

The first signature (`int main(void)`) defines a `main()` function returning an integer (`int`) and taking no argument. The second signature (`int main(int argc, char *argv[])`) defines a `main()` function also returning an integer, but taking both an integer and an array of pointers to `char` as arguments.

Here's what a "Hello, world" program in C would look like:

```c
#include <stdio.h>

int main(int argc, char *argv[])
{
    printf("Hello, world!\n");

    return 0;
}
```

Step by step, the previous snippet will:
- Include `stdio.h` header file. `stdio.h` contains the declaration of the `libc` functions [related to input/output](https://pubs.opengroup.org/onlinepubs/7908799/xsh/stdio.h.html). In this case, it has to be included, so the compiler knows about the `printf()` function.
- Within the `main()` function:
    - Print the string "Hello, world!" on `stdout`, following by a newline character (`\n`).
    - Return `0`. Standard command line tools on Linux use the return code `0` on success, and a negative integer value on error.

>**`stdout`? `stdin`? `stderr`?**
>
>`stdout`, `stdin`, and `stderr` are the [3 standard streams](https://en.wikipedia.org/wiki/Standard_streams) each C program (and C++) has access to. The system itself is responsible for creating the streams, and connect them to the program. It is completely transparent to the user. When running a binary program from the command line, those 3 streams are usually provided by your shell (meaning writing to `stdout` or `stderr` will output in the current shell, and reading from `stdin` will read from the current shell).
>
>What are they?
>- `stdout`: standard output stream, you write to this stream using `printf()`.
>- `stderr`: standard error output stream, used to print runtime error from your program. You can write to `stderr` using [`fprintf(stderr, "my message")`](https://man7.org/linux/man-pages/man3/fprintf.3p.html).
>- `stdin`: standard input stream, used to receive user input using, for example, [`scanf()`](https://man7.org/linux/man-pages/man3/scanf.3.html).

### Basic C concepts

#### Data type and data representation

C is a statically typed language, meaning the variables' type is checked at compilation: if you define `int a`, then your compiler will raise an error during compilation if you try to assign it a non-integer value, for example `int a = 3.1`.

The memory in C is merely a huge array of addressable bytes:

<p align="center">
  <img src="/assets/cpp/0-memory_layout.png" width=350/>
</p>

Then, depending on your data type, your variable will use more or less bytes. While the C standard doesn't define a specific size for each data type, it guarantees a minimal size:
- `char`: signed character, **at least** 8 bits.
- `short`: signed short integer, **at least** 16 bits.
- `int`: signed integer, **at least** 16 bits.
- `double`: double precision floating point. Its minimal size is not defined by the C standard, so compilers usually follow the [IEEE 754 double-precision binary floating-point format](https://en.wikipedia.org/wiki/Double-precision_floating-point_format#IEEE_754_double-precision_binary_floating-point_format:_binary64), using 64 bits.

>If you want to know the range of values you can store in a given data type in C (and C++), see [`limits.h`](https://pubs.opengroup.org/onlinepubs/009695399/basedefs/limits.h.html). It defines various macro representing the minimum and maximum values that can be stored in a given data type:
>`printf("char min=%d, max=%d\n", CHAR_MIN, CHAR_MAX);`

The various standard implementations (your compiler, for example) are responsible for defining which size is each data type. It generally depends on the combination of architecture (x86, x86-64, arm64), operating system, and compiler. [On an `arm64` Linux host](https://github.com/qdeslandes/qdeslandes.github.io/tree/main/_cpp/code/0.%20C%20and%20compilation/data_size.c):

```shell
❯ make data_size && ./data_size
signed char: 1 bytes
unsigned char: 1 bytes
signed short: 2 bytes
unsigned short: 2 bytes
signed int: 4 bytes
unsigned int: 4 bytes
```

Going back to our memory, let's see what `int myvar = 2;` would look like, stored on a machine:

<p align="center">
  <img src="/assets/cpp/0-var_in_memory.png" width=400/>
</p>

Because `myvar` is an integer, it uses 4 bytes of memory on my system. All 4 bytes are contiguous (one after the other) and `myvar` starts at address `0x01`.

#### Pointers

A pointer variable is a variable containing the address to another variable:

```c
int a = 3;
int *p = &a;
```

`a` is a 4 bytes integer initialized with `3`, and `p` is a variable (area in memory) big enough to store a memory address. We also know that the memory pointed to by `p` contains an integer. The size required to store a memory address (i.e. the size of a pointer variable) depends on the system. 64 bits processors can address up to 64 bits of memory, meaning addresses are 64 bits (8 bytes).

<p align="center">
  <img src="/assets/cpp/0-pointer_to_var_memory.png" width=500/>
</p>

>**Incrementing a pointer**
>
>Incrementing a pointer will update the pointer's value to `current_value + sizeof(data_pointed_to)`. Hence, an `int *p = 0; ++p;` won't lead to `p = 0x1`, but `p = sizeof(int)`.

#### Loops and flow control statements

Loops and flow control statements are a way to modulate the program's behaviour depending on a given condition. Loops will also repeat a piece of code as long as the condition is valid.

```c
int b = 30;

if (b < 20)
    printf("b is smaller than 20\n");
else
    printf("b is bigger or equal to 20\n");
```

This program would print `b is bigger or equal to 20`.

```c
int b = 0;

while (b < 10) {
    printf("b = %d\n", b);
    ++b;
}
```

This program would print the value of `b` on each loop, then increment it, until `b < 10` is false. A similar result could be achieved with the following `for` loop: `for (int b = 0; b < 10; ++b) { ... }`.

#### Functions

Functions are reusable pieces of code. You can define a function to perform a specific operation and call it as many times as you want:

```c
int hello(int count)
{
    for (int i = 0; i < count; ++i)
        printf("Hello, world!\n");

    return count;
}
```

`hello()` is a function returning an integer value and taking an integer as argument. The function prints `Hello, world!` to `stdout` X times, X being defined by `count`. It then returns `count`.

`int hello(int count)` is called the function's prototype (or signature). Prototypes are usually declared within header files (`.h`) so other translation units (`.c` files) can use it.

## Preprocessor, compiler, assembler, link

Compilation is a non-trivial step aiming to convert one or more source C file(s) into an executable binary file. While most compilers are able to perform the whole process by themselves, it actually involves 4 different tools:
- **Preprocessor**: process preprocessor directives (e.g. `#include <stdio.h>`) to output a text file containing C code, without any preprocessor directive.
- **Compiler**: convert source code from a language to another. In this case, our compiler will convert the C source code into assembly.
- **Assembler**: generate a binary representation of the program out of assembly code (text).
- **Linker**: link various binary parts of the program together, and link external libraries (`libc` for example).

<p align="center">
  <img src="/assets/cpp/0-compilation.png" width=700/>
</p>

File extensions here are completely arbitrary: `main.c` could be called `main.myCfile` and it wouldn't change anything. `main.c` is a C source file, and the C preprocessor (`cpp`) expects a C source file. Same as every other file extension on a Linux system.

## Resources

- ["Programming languages -- C" - C11 standard draft from April 12, 2011.](http://port70.net/~nsz/c/c11/n1570.html)
- ["The C Programming Language" - Brian W. Kernighan, Dennis M. Ritchie](https://kremlin.cc/k&r.pdf)
- [Related source code - github.com/qdeslandes](https://github.com/qdeslandes/qdeslandes.github.io/tree/main/_cpp/code)
