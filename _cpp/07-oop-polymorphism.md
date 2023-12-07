---
layout: page
title:  "OOP - Polymorphism"
collection: cpp
published: true
---

## Understanding polymorphism

Polymorphism is a fundamental concept in object-oriented programming (OOP) that allows objects of different types to be treated as objects of a common superclass. It enables a single interface to be used for entities of various types, promoting flexibility and extensibility in code.

Polymorphism, derived from Greek words meaning "many shapes," refers to the ability of different objects to be treated as instances of a common parent class. It allows for the implementation of methods in various ways across different derived classes.

## Compile-time polymorphism

Compile-time resolution occurs when the compiler selects the method to call based on the reference or pointer type. This is also called early-binding, or static binding.

Consider a scenario where we have a Calculator class with different overloaded methods for performing arithmetic operations at compile time (see on [Compiler Explorer][ce_comp_poly]):

```cpp
class Calculator {
public:
    int add(int a, int b) {
        return a + b;
    }

    double add(double a, double b) {
        return a + b;
    }
};
```

In this case, when we call the add method, the compiler determines the appropriate method to execute based on the method signature and the argument types at compile time. For instance (see on [Compiler Explorer][ce_comp_poly]):

```cpp
int main() {
    Calculator calc;

    int sum_int = calc.add(5, 10);          // Calls int add(int a, int b)
    double sum_double = calc.add(3.5, 2.5); // Calls double add(double a, double b)

    return 0;
}
```

The method resolution for the add function is determined during compilation based on the argument types, known as compile-time polymorphism or static binding.

## Runtime polymorphism

runtime resolution dynamically selects the appropriate method based on the actual object type during program execution. Late binding allows for greater flexibility in program design by enabling code to adapt to changes at runtime. This dynamic behavior is particularly useful in scenarios involving inheritance and polymorphic behavior.

Runtime polymorphism is achieved through inheritance and the use of pointers or references to the base class. This allows the base class pointer to refer to objects of derived classes, facilitating dynamic method resolution.

When a base class pointer points to a derived class object and a virtual function is called through that pointer, the function corresponding to the actual object type is executed. This dynamic resolution occurs at runtime, ensuring the correct behavior based on the object type.

Let's use inheritance and virtual functions to demonstrate runtime polymorphism (see on [Compiler Explorer][ce_run_poly]):

```cpp
// Base class
class Shape {
public:
    virtual void draw() const {
        std::cout << "Drawing a shape...\n";
    }
};

// Derived classes
class Circle : public Shape {
public:
    void draw() const override {
        std::cout << "Drawing a circle...\n";
    }
};

class Square : public Shape {
public:
    void draw() const override {
        std::cout << "Drawing a square...\n";
    }
};

int main() {
    Shape* shape1 = new Circle();
    Shape* shape2 = new Square();

    shape1->draw(); // Outputs: Drawing a circle...
    shape2->draw(); // Outputs: Drawing a square...

    delete shape1;
    delete shape2;

    return 0;
}
```

Here, the method draw is resolved at runtime based on the actual object type pointed to by the base class pointer. This is runtime polymorphism or dynamic binding, as the correct method is determined during program execution based on the object being referred to by the pointer.


## Polymorphism and abstraction
Polymorphism facilitates the implementation of abstraction by allowing the use of generic interfaces to operate on objects of different types. It encourages code reusability and simplifies complex systems by promoting a high level of abstraction (see on [Compiler Explorer][ce_poly_abs]):

```cpp
// Abstract base class
class Shape {
public:
    virtual void draw() const = 0; // Pure virtual function makes Shape abstract
};

// Derived classes
class Circle : public Shape {
public:
    void draw() const override {
        std::cout << "Drawing a circle...\n";
    }
};

class Square : public Shape {
public:
    void draw() const override {
        std::cout << "Drawing a square...\n";
    }
};

// Somewhere in a drawing application...
void drawShape(const Shape& shape) {
    shape.draw(); // Draws the specific shape based on the object type
}

int main() {
    Circle circle;
    Square square;
    
    drawShape(circle); // Outputs: Drawing a circle...
    drawShape(square); // Outputs: Drawing a square...
    
    return 0;
}
```

Here, the Shape class serves as an abstract base class, and Circle and Square are concrete shapes. The drawShape function demonstrates polymorphism by invoking the correct draw method based on the actual object type.

## Virtual functions and late binding

The key role of virtual functions lies in their ability to enable runtime polymorphism. This allows the correct method to be invoked at runtime based on the actual object type, rather than the reference or pointer type.

Virtual methods and the `virtual` keyword are described more extensively in the previous lesson. Feel free to refer to it if needed.

## Pitfalls and Best Practices

### Managing Object Slicing
Object slicing occurs when only the base class part of an object is copied or manipulated, resulting in the loss of derived class-specific information. It's crucial to use pointers or references to avoid slicing and retain polymorphic behavior (see on [Compiler Explorer][ce_slicing]):

```cpp
class Base {
public:
    int baseValue;

    Base(int val) : baseValue(val) {}
    virtual void display() {
        cout << "Base value: " << baseValue << endl;
    }
};

class Derived : public Base {
public:
    int derivedValue;

    Derived(int baseVal, int derivedVal) : Base(baseVal), derivedValue(derivedVal) {}
    void display() override {
        cout << "Base value: " << baseValue << ", Derived value: " << derivedValue << endl;
    }
};

int main() {
    Base baseObj(10);
    Derived derivedObj(20, 30);

    baseObj = derivedObj; // Slicing occurs here

    baseObj.display(); // Will only show the base class values

    return 0;
}
```

### Virtual Destructors for Proper Resource Cleanup

When using inheritance and polymorphism, it's important to define virtual destructors in base classes. This ensures that destructors of derived classes are invoked properly when objects are destroyed via base class pointers, preventing memory leaks.

## Resources

- [What is object slicing - stackoverflow.com](https://stackoverflow.com/questions/274626/what-is-object-slicing)

[ce_comp_poly]: https://godbolt.org/#g:!((g:!((g:!((h:codeEditor,i:(filename:'1',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,selection:(endColumn:21,endLineNumber:20,positionColumn:21,positionLineNumber:20,selectionStartColumn:21,selectionStartLineNumber:20,startColumn:21,startLineNumber:20),source:'%23include+%3Ciostream%3E%0A%0Aclass+Calculator+%7B%0Apublic:%0A++++int+add(int+a,+int+b)+%7B%0A++++++++std::cout+%3C%3C+%22Using:+int+add(int+a,+int+b)%22+%3C%3C+std::endl%3B%0A++++++++return+a+%2B+b%3B%0A++++%7D%0A%0A++++double+add(double+a,+double+b)+%7B%0A++++++++std::cout+%3C%3C+%22Using:+double+add(double+a,+double+b)%22+%3C%3C+std::endl%3B%0A++++++++return+a+%2B+b%3B%0A++++%7D%0A%7D%3B%0A%0Aint+main()%0A%7B%0A++++Calculator+c%3B%0A++++c.add(1,+1)%3B%0A++++c.add(1.6,+2.6)%3B%0A%7D'),l:'5',n:'0',o:'C%2B%2B+source+%231',t:'0')),k:61.01960784313726,l:'4',n:'0',o:'',s:0,t:'0'),(g:!((h:executor,i:(argsPanelShown:'1',compilationPanelShown:'0',compiler:g132,compilerName:'',compilerOutShown:'0',execArgs:'',execStdin:'',fontScale:14,fontUsePx:'0',j:2,lang:c%2B%2B,libs:!(),options:'',overrides:!(),runtimeTools:!(),source:1,stdinPanelShown:'1',wrap:'1'),l:'5',n:'0',o:'Executor+x86-64+gcc+13.2+(C%2B%2B,+Editor+%231)',t:'0')),k:38.98039215686274,l:'4',n:'0',o:'',s:0,t:'0')),l:'2',n:'0',o:'',t:'0')),version:4
[ce_run_poly]: https://godbolt.org/#g:!((g:!((g:!((h:codeEditor,i:(filename:'1',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,selection:(endColumn:2,endLineNumber:37,positionColumn:2,positionLineNumber:37,selectionStartColumn:2,selectionStartLineNumber:37,startColumn:2,startLineNumber:37),source:'%23include+%3Ciostream%3E%0A%0A//+Base+class%0Aclass+Shape+%7B%0Apublic:%0A++++virtual+void+draw()+const+%7B%0A++++++++std::cout+%3C%3C+%22Drawing+a+shape...%5Cn%22%3B%0A++++%7D%0A%7D%3B%0A%0A//+Derived+classes%0Aclass+Circle+:+public+Shape+%7B%0Apublic:%0A++++void+draw()+const+override+%7B%0A++++++++std::cout+%3C%3C+%22Drawing+a+circle...%5Cn%22%3B%0A++++%7D%0A%7D%3B%0A%0Aclass+Square+:+public+Shape+%7B%0Apublic:%0A++++void+draw()+const+override+%7B%0A++++++++std::cout+%3C%3C+%22Drawing+a+square...%5Cn%22%3B%0A++++%7D%0A%7D%3B%0A%0Aint+main()+%7B%0A++++Shape*+shape1+%3D+new+Circle()%3B%0A++++Shape*+shape2+%3D+new+Square()%3B%0A%0A++++shape1-%3Edraw()%3B+//+Outputs:+Drawing+a+circle...%0A++++shape2-%3Edraw()%3B+//+Outputs:+Drawing+a+square...%0A%0A++++delete+shape1%3B%0A++++delete+shape2%3B%0A%0A++++return+0%3B%0A%7D'),l:'5',n:'0',o:'C%2B%2B+source+%231',t:'0')),k:61.01960784313726,l:'4',n:'0',o:'',s:0,t:'0'),(g:!((h:executor,i:(argsPanelShown:'1',compilationPanelShown:'0',compiler:g132,compilerName:'',compilerOutShown:'0',execArgs:'',execStdin:'',fontScale:14,fontUsePx:'0',j:2,lang:c%2B%2B,libs:!(),options:'',overrides:!(),runtimeTools:!(),source:1,stdinPanelShown:'1',wrap:'1'),l:'5',n:'0',o:'Executor+x86-64+gcc+13.2+(C%2B%2B,+Editor+%231)',t:'0')),k:38.98039215686274,l:'4',n:'0',o:'',s:0,t:'0')),l:'2',n:'0',o:'',t:'0')),version:4
[ce_poly_abs]: https://godbolt.org/#g:!((g:!((g:!((h:codeEditor,i:(filename:'1',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,selection:(endColumn:1,endLineNumber:3,positionColumn:1,positionLineNumber:3,selectionStartColumn:1,selectionStartLineNumber:3,startColumn:1,startLineNumber:3),source:'%23include+%3Ciostream%3E%0A%0A//+Abstract+base+class%0Aclass+Shape+%7B%0Apublic:%0A++++virtual+void+draw()+const+%3D+0%3B+//+Pure+virtual+function+makes+Shape+abstract%0A%7D%3B%0A%0A//+Derived+classes%0Aclass+Circle+:+public+Shape+%7B%0Apublic:%0A++++void+draw()+const+override+%7B%0A++++++++std::cout+%3C%3C+%22Drawing+a+circle...%5Cn%22%3B%0A++++%7D%0A%7D%3B%0A%0Aclass+Square+:+public+Shape+%7B%0Apublic:%0A++++void+draw()+const+override+%7B%0A++++++++std::cout+%3C%3C+%22Drawing+a+square...%5Cn%22%3B%0A++++%7D%0A%7D%3B%0A%0A//+Somewhere+in+a+drawing+application...%0Avoid+drawShape(const+Shape%26+shape)+%7B%0A++++shape.draw()%3B+//+Draws+the+specific+shape+based+on+the+object+type%0A%7D%0A%0Aint+main()+%7B%0A++++Circle+circle%3B%0A++++Square+square%3B%0A++++%0A++++drawShape(circle)%3B+//+Outputs:+Drawing+a+circle...%0A++++drawShape(square)%3B+//+Outputs:+Drawing+a+square...%0A++++%0A++++return+0%3B%0A%7D'),l:'5',n:'0',o:'C%2B%2B+source+%231',t:'0')),k:61.01960784313726,l:'4',n:'0',o:'',s:0,t:'0'),(g:!((h:executor,i:(argsPanelShown:'1',compilationPanelShown:'0',compiler:g132,compilerName:'',compilerOutShown:'0',execArgs:'',execStdin:'',fontScale:14,fontUsePx:'0',j:2,lang:c%2B%2B,libs:!(),options:'',overrides:!(),runtimeTools:!(),source:1,stdinPanelShown:'1',wrap:'1'),l:'5',n:'0',o:'Executor+x86-64+gcc+13.2+(C%2B%2B,+Editor+%231)',t:'0')),k:38.98039215686274,l:'4',n:'0',o:'',s:0,t:'0')),l:'2',n:'0',o:'',t:'0')),version:4
[ce_slicing]: https://godbolt.org/#g:!((g:!((g:!((h:codeEditor,i:(filename:'1',fontScale:14,fontUsePx:'0',j:1,lang:c%2B%2B,selection:(endColumn:20,endLineNumber:1,positionColumn:20,positionLineNumber:1,selectionStartColumn:20,selectionStartLineNumber:1,startColumn:20,startLineNumber:1),source:'%23include+%3Ciostream%3E%0A%0Aclass+Base+%7B%0Apublic:%0A++++int+baseValue%3B%0A%0A++++Base(int+val)+:+baseValue(val)+%7B%7D%0A%0A++++virtual+void+display()+%7B%0A++++++++std::cout+%3C%3C+%22Base+value:+%22+%3C%3C+baseValue+%3C%3C+std::endl%3B%0A++++%7D%0A%7D%3B%0A%0Aclass+Derived+:+public+Base+%7B%0Apublic:%0A++++int+derivedValue%3B%0A%0A++++Derived(int+baseVal,+int+derivedVal)+:+Base(baseVal),+derivedValue(derivedVal)+%7B%7D%0A%0A++++void+display()+override+%7B%0A++++++++std::cout+%3C%3C+%22Base+value:+%22+%3C%3C+baseValue+%3C%3C+%22,+Derived+value:+%22+%3C%3C+derivedValue+%3C%3C+std::endl%3B%0A++++%7D%0A%7D%3B%0A%0Aint+main()+%7B%0A++++Base+baseObj(10)%3B%0A++++Derived+derivedObj(20,+30)%3B%0A%0A++++baseObj+%3D+derivedObj%3B+//+Slicing+occurs+here%0A%0A++++baseObj.display()%3B+//+Will+only+show+the+base+class+values%0A%0A++++return+0%3B%0A%7D'),l:'5',n:'0',o:'C%2B%2B+source+%231',t:'0')),k:61.01960784313726,l:'4',n:'0',o:'',s:0,t:'0'),(g:!((h:executor,i:(argsPanelShown:'1',compilationPanelShown:'0',compiler:g132,compilerName:'',compilerOutShown:'0',execArgs:'',execStdin:'',fontScale:14,fontUsePx:'0',j:2,lang:c%2B%2B,libs:!(),options:'',overrides:!(),runtimeTools:!(),source:1,stdinPanelShown:'1',wrap:'1'),l:'5',n:'0',o:'Executor+x86-64+gcc+13.2+(C%2B%2B,+Editor+%231)',t:'0')),k:38.98039215686274,l:'4',n:'0',o:'',s:0,t:'0')),l:'2',n:'0',o:'',t:'0')),version:4