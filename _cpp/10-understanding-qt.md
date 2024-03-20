---
layout: page
title: "Understanding Qt"
collection: cpp
published: true
---

This lesson aims to provide a high-level overview of Qt and its core concepts, before diving more into it later.

## What's a graphics library?

A graphics library is a software library or a set of tools that simplifies the process of creating graphical user interfaces (GUIs) and handling graphics-related tasks in a software application. It provides a structured approach to designing and implementing GUIs, managing the complexities of rendering graphics, handling user interactions, and ensuring compatibility across different platforms and devices.

In essence, a graphics library helps developers create visually appealing and user-friendly interfaces without having to deal with the low-level details of graphics programming.

There are several well-known graphics library, each with its own strengths and weaknesses. Some of the most popular ones include:
- [OpenGL][opengl]: a cross-language, cross-platform API for rendering 2D and 3D vector graphics. It's widely used in CAD, virtual reality, video games, and flight simulation.
- [DirectX][directx]: a collection of APIs for handling tasks related to multimedia, especially game programming and video, on Microsoft platforms.
- [Vulkan][vulkan]: a low-overhead, cross-platform 3D graphics and computing API. It provides high-performance, real-time 3D graphics for applications such as video games and interactive media.
- [WebGL][webgl]: a JavaScript API for rendering interactive 2D and 3D graphics within any compatible web browser without the use of plugins.

<center>
    <figure class="image">
        <img style="max-width: 600px" src="../assets/10-opengl-on-the-system.png" alt="OpenGL on Linux">
        <figcaption>OpenGL in the Linux graphics stack</figcaption>
    </figure>
</center>

You probably noticed that Qt is not cited amongst the graphics libraries above, why is that? Qt isn't really a graphics library, but a framework to develop GUIs. It provides an API to developer end-to-end desktop applications, including filesystem manipulation, network communication, and much more. Qt is multiplatform, and relies on different graphics libraries to achieve its purpose.

## More about Qt

### History and Purpose of Qt

Qt was first released in 1995 by Trolltech, a Norwegian company. The primary goal behind its creation was to provide a single framework that could be used to develop applications for various platforms without requiring significant changes to the source code. Over the years, Qt has evolved and expanded, but its primary objective remains the same: to simplify the process of developing cross-platform GUI applications.

Qt offers a wide range of features that make it a versatile and powerful framework for GUI development. These include:
- **Cross-platform compatibility**: Qt applications can be developed and run on various platforms, including Windows, MacOS, Linux, and more, with minimal to no changes in the source code.
- **Rich set of widgets**: Qt provides a comprehensive set of widgets for creating GUIs, including buttons, text boxes, sliders, and more.
- **Advanced graphics support**: Qt supports advanced graphics and visual effects, including OpenGL for 3D graphics.
- **Multimedia support**: Qt includes classes for playing audio and video, as well as for accessing camera and radio functionality.
- **Networking and database access**: Qt provides classes for network programming and accessing SQL databases.
- **Multithreading support**: Qt provides a high-level API for managing threads, making it easier to create responsive applications.
- **Internationalization**: Qt supports internationalization, allowing applications to be easily translated into different languages.

### Widgets? What are those?

Widgets are the basic building blocks for creating graphical user interfaces. They represent visual elements on the screen and can be customized and combined to create complex interfaces: a window, a button, a text field, you name it!

Nowadays, Qt's development team is pushing towards [QML][qml] (Qt Meta-object Language) instead of widgets. While we will discuss a bit about widgets in this course, we'll mainly focus on QML to develop graphical applications.

<center>
    <figure class="image">
        <img style="max-width: 600px" src="../assets/10-qt-widget.webp" alt="Qt application">
        <figcaption>Application developed using Qt</figcaption>
    </figure>
</center>

Both have their own strengths:
- **Widgets**: they are C++ classes that provide numerous pre-defined UI elements, such as buttons, text boxes, sliders, and more. They are more mature and stable, with a very rich set of UI elements. They lack features when it comes to fluid and animated UIs, and they are less suited for multi-platform applications.
- **QML**: it's a declarative language that allows you to design UIs using JavaScript and CSS-like syntax. QML allows developers to easily create dynamic, animated UIs. It's much easier to separate UI and logic with QML, making the code more maintainable. However, QML is less mature (although very stable), and can lack in performance compared to widgets, as the interfaces are interpreted at runtime.

Both widgets and QML uses similar concepts, but implement it differently. While we will learn about those generic concepts, we will use QML to implement our applications.

## Qt 101

When it comes to creating graphical applications, Qt is based around 3 major concepts: widgets, layouts, and signals/slots.

Something to keep in mind here, widgets can be used to describe two things: the generic "widget" concept used to describe a graphical element in an application, this applies to both Qt Widgets and QML; and the `Widgets` way of creating a Qt application, as opposed to QML. In this section, "widget" refers to the generic graphical concept.

### Widgets

A widget is a basic building block for creating graphical user interfaces. It represents a visual element on the screen, such as a button, a text box, a slider, or a window.

Widgets can be customized in terms of appearance and behavior, and they can contain other widgets, forming a parent-child hierarchy. This hierarchical organization of widgets is used to build complex user interfaces.

Widgets handle user input and system events, and they can emit signals (more on that later) to notify other parts of the application when something of interest happens, such as a button being clicked.

### Layouts

A layout is a way to manage the positioning and sizing of widgets within a container widget, such as a window or a frame. Layouts automatically adjust the size and position of widgets when the container is resized, ensuring a consistent and responsive user interface.

Layouts can be nested, meaning you can have layouts within layouts to create complex user interface structures. They take care of the tedious task of calculating the size and position of widgets, allowing developers to focus on the overall design and functionality of the user interface.

### Signals and slots

Signals and slots are a mechanism for communication between objects. They are a key part of Qt's event-driven programming model and are used to connect different parts of an application in a loosely coupled way.
- **Signals**: a signal is a message that an object can send when a particular event occurs. For example, a button object might emit a signal when it is clicked.
- **Slots**: a slot is a function that can be connected to a signal. When the signal is emitted, the connected slot function is automatically called. For example, you might have a slot that shows a message box, and you could connect this slot to the button's click signal.

<center>
    <figure class="image">
        <img style="max-width: 600px" src="../assets/10-signals-slots.png" alt="Qt signals and slots">
        <figcaption>Representation of signals and slots in Qt</figcaption>
    </figure>
</center>

The signals/slots mechanism provided by Qt is loosely coupled (a signal has no knowledge of the slots receiving it, nor does it need to know), type safe (connections between signals and slots are checked at compile time), and flexible (signals/slots have a many-to-many relationship).

We will explore those concepts more in the future lessons!

[opengl]: https://en.wikipedia.org/wiki/OpenGL
[directx]: https://en.wikipedia.org/wiki/DirectX
[vulkan]: https://en.wikipedia.org/wiki/Vulkan
[webgl]: https://en.wikipedia.org/wiki/WebGL
[qml]: https://doc.qt.io/qt-6/qtqml-index.html
