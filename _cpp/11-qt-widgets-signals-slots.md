---
layout: page
title: "Widgets, signals, and slots"
collection: cpp
published: true
---

## Introduction to Qt Widgets

### What are Qt Widgets?
Qt widgets are pre-built user interface components that provide a visual representation of an application's functionality. They include common elements such as buttons, labels, text fields, menus, toolbars, and many others. These widgets are highly customizable and can be combined in various ways to create complex user interfaces.

### Why use Qt Widgets?
There are several reasons why developers choose to use Qt widgets for their GUI development:
- **Cross-platform compatibility**: Qt widgets can be used to create applications that run on multiple platforms with a consistent look and feel. This saves time and effort in developing separate versions of an application for each platform.
- **Rich set of UI components**: Qt provides a comprehensive set of UI components that cover most of the common use cases in GUI development. This allows developers to focus on the core functionality of their application rather than building UI components from scratch.
- **Customizability**: Qt widgets are highly customizable, allowing developers to change their appearance, behavior, and layout to match the specific needs of their application.
- **High performance**: Qt widgets are designed to be fast and efficient, making them suitable for developing high-performance applications.

### Basic Concepts of Qt Widgets
Before diving into creating Qt widgets, it's important to understand some basic concepts:
- **Object hierarchy**: Qt widgets are organized in a hierarchical structure, where each widget can have one or more child widgets. This allows developers to create complex layouts by nesting widgets inside other widgets.
- **`QObject` class**: all Qt widgets inherit from the `QObject` class, which provides the basic infrastructure for creating and managing objects in Qt. The `QObject` class includes features such as object naming, signal and slot connections, and property management.
- **Signals and slots**: signals and slots are a key concept in Qt widgets for event handling and inter-object communication. A signal is emitted when a specific event occurs, such as a button click, and can be connected to a slot, which is a function that handles the event. This allows for decoupling of objects and makes it easier to maintain and extend the code.

## Creating Qt Widgets with QML

Qt provides a declarative language called [QML][qml] (Qt Modeling Language) that allows developers to create user interfaces in a more intuitive and concise way than traditional C++ code. In this section, we will cover the basics of creating Qt widgets with QML.

### What is QML?
QML is a declarative language based on JavaScript that allows developers to define the user interface of an application in a more visual and intuitive way. It provides a set of basic building blocks, such as rectangles, text fields, and buttons, that can be combined to create complex user interfaces.

### Basic QML Syntax
The basic syntax of QML consists of defining objects and their properties using JSON-like syntax. For example, a simple rectangle object can be defined as follows:

```qml
Rectangle {
    width: 100
    height: 50
    color: "red"
}
```

This defines a rectangle with a width of 100 pixels, a height of 50 pixels, and a red color.

To create a simple Qt widget with QML, we need to define a QML file that includes the UI components and any necessary logic. Here's an example of a simple QML file that creates a button:

```qml
import QtQuick 2.0

Button {
    text: "Click me!"
    onClicked: {
        console.log("Button clicked")
    }
}
```

This defines a button with the text "Click me!" and a callback function that logs a message to the console when the button is clicked.

### Positioning and Sizing Qt Widgets with QML
In QML, widgets can be positioned and sized using various layout containers, such as [`GridLayout`][gridlayou], [`ColumnLayout`][columnlayout], and [`RowLayout`][rowlayout]. These containers automatically adjust the size and position of their child widgets based on their contents and the available space.

For example, to create a simple form with two text fields and a button, we can use a `GridLayout` container as follows:
```qml
import QtQuick 2.0

GridLayout {
    columns: 2

    TextField {
        id: nameField
        text: "Name:"
    }

    TextField {
        id: emailField
        text: "Email:"
    }

    Button {
        text: "Submit"
        onClicked: {
            console.log("Name: " + nameField.text + ", Email: " + emailField.text)
        }
    }
}
```

This defines a grid with two columns and three rows, where the first two rows contain the text fields and the third row contains the button. The label property is used to display a label next to each text field, and the [`onClicked`][onclicked] callback function is used to log the contents of the text fields when the button is clicked.

## Signals and Slots in Qt

Signals and slots are a key concept in Qt that allow for communication between objects.

A signal is a message that is emitted by an object when a specific event occurs, such as a button click or a text field change. A slot is a function that is called in response to a signal. When a signal is emitted, it can be connected to one or more slots that will be executed when the signal is received.

### How to Use Signals and Slots in Qt
To use signals and slots in Qt, we need to define both the signal and the slot in the objects that will communicate with each other. Here's an example of a simple signal and slot implementation:

```c++
class MyObject : public QObject
{
    Q_OBJECT

public:
    MyObject(QObject *parent = nullptr) : QObject(parent) {}

signals:
    void mySignal();

public slots:
    void mySlot() {
        qDebug() << "My slot called";
    }
};
```

In this example, `MyObject` is a subclass of `QObject` that defines a signal called `mySignal` and a slot called `mySlot`. The `Q_OBJECT` macro is used to enable the use of signals and slots in this class.

### Connecting Signals and Slots in Qt
Once we have defined the signal and slot in our objects, we need to connect them using the connect function. Here's an example of connecting the signal and slot from the previous example:

```c++
MyObject obj1;
MyObject obj2;
QObject::connect(&obj1, &MyObject::mySignal, &obj2, &MyObject::mySlot);
```

This connects the `mySignal` signal of `obj1` to the `mySlot` slot of `obj2`. Now, whenever `obj1` emits the `mySignal` signal, `obj2`'s `mySlot` function will be called.

### Using Default and Custom Signals and Slots
Qt provides several default signals and slots that can be used out-of-the-box, such as `clicked` for buttons and `textChanged` for text fields. However, we can also define our own custom signals and slots to meet the specific needs of our application.

For example, we could define a custom signal in `MyObject` to notify other objects when a specific event occurs:

```c++
class MyObject : public QObject
{
    Q_OBJECT

public:
    MyObject(QObject *parent = nullptr) : QObject(parent) {}

signals:

    void myCustomSignal(int value);

public slots:
    void mySlot(int value) {
        qDebug() << "My slot called with value:" << value;
    }
};
```

In this example, `MyObject` defines a custom signal called `myCustomSignal` that takes an integer parameter. We can connect this signal to a slot in another object that expects an integer parameter:

```c++
MyObject obj1;
QObject obj2;
QObject::connect(&obj1, &MyObject::myCustomSignal, &obj2, [](int value) {
    qDebug() << "My custom slot called with value:" << value;
});
```

Now, whenever `obj1` emits the `myCustomSignal` signal with an integer parameter, `obj2`'s anonymous function slot will be called with the same parameter. A signal can be emitted by either calling the signal as we would call any function, or use the `emit` macro.

## Interaction between C++ and QML

Qt defines [many methods to access C++ objects from QML and vice versa][interaction]. This involves creating a C++ class that represents the object, and implementing any necessary functions or properties. Here's an example:

```c++
class MyObject : public QObject
{
    Q_OBJECT
    Q_PROPERTY(int myProperty MEMBER m_myProperty)

private:
    int m_myProperty;

public:
    MyObject(QObject *parent = nullptr) : QObject(parent) {}
};

int main()
{
    QQmlApplicationEngine engine;
    MyObject *myObject = new MyObject();

    engine->rootContext()->setContextProperty("myObjectInQml", myObject);
    engine.load(QUrl(QStringLiteral("qrc:/main.qml")));

    return 0;
}
```

In this example, we define a `MyObject` class that inherits from `QObject`. This class has a private property called `m_myProperty`. In the main function, our application engine has been given a context property, which is a pointer to an object of type `MyObject` we defined above. This means that our `MyObject` object will be accessible from QML through the name `myObjectInQml`:

```qml
Button {
    text: "Click me!"
    onClicked: console.log(myObjectInQml);
}
```

What about the [`Q_PROPERTY`][qproperty] macro then? This is a macro provided by Qt, allowing us to make some of our object's properties accessible from QML. In this case, Qt will automatically create a QML property name `myProperty` for every object of type `MyObject`:

```qml
Button {
    text: "Click me!"
    onClicked: {
        myObjectInQml.myProperty = 13;
    }
}
```

This macro allows various parameters: the property can be modified through setters and getters (to validate or modify its value), it can be constant, ...

By using the `Q_INVOKABLE` macro, you could also call C++ functions from your C++ object directly from QML:

```c++
class MyObject : public QObject
{
    Q_OBJECT

public:
    Q_INVOKABLE void sayHello() { std::cout << "Hello" << std::endl; }
};
```

With the following QML:
```qml
Button {
    text: "Click me!"
    onClicked: {
        myObjectInQml.sayHello();
    }
}
```

Because C++ is a typed system, the property you would modify or the arguments you would call your C++ functions with would be converted into C++. See [Qt's documentation for more details][convert_qml_cpp].

[qml]: https://doc.qt.io/qt-6/qtqml-index.html
[gridlayout]: https://doc.qt.io/qt-6/qml-qtquick-layouts-gridlayout.html
[columnlayout]: https://doc.qt.io/qt-6/qml-qtquick-layouts-columnlayout.html
[rowlayout]: https://doc.qt.io/qt-6/qml-qtquick-layouts-rowlayout.html
[onclicked]: https://doc.qt.io/qt-5/qml-qtquick-controls2-abstractbutton.html#clicked-signal
[qproperty]: https://doc.qt.io/qt-6/properties.html
[convert_qml_cpp]: https://doc.qt.io/qt-6/qtqml-cppintegration-data.html
[interaction]: https://doc.qt.io/qt-6/qtqml-cppintegration-overview.html
