---
title: chuck10
date: 2018-02-15 21:40:10
categories: 
    - 编程与生活
tags: 
    - ChucK
---

classes and objects

    introduction
    existing classes
    working with objects
    writing a class
    members (data + functions)
    static (data + functions)
    inheritance
    overloadation (overloading + overriding) 

#introduction

Chuck implements(实现) an object system that borrows from both C++ and Java conventions. In our case this means:

* You can define(定义) custom classes as new types and instantiate objects自定义类
* ChucK supports *polymorphic inheritance(多态继承)* (this is the same model used in Java, and also known as *virtual inheritance* in C++)
* All object variables(变量) are *references* (like Java), but instantiation(实例化) resembles(类似于) C++. We will discuss this in detail below.
* There is a default class library.
* All objects inherit(继承) from the Object class (as in Java)

For the sake(目的) of clarity(清楚) we will define(定义) these terms:

* a class is an *abstraction* of data (**members**) and behavior(行为) (**methods**)
* a class is a *type*.
* an *object* is an *instantiation* of that class
* a *reference* variable refers indirectly to an object - it is not the object itself. **All ChucK *object variables* are reference variables**(变量) (like in Java).
* similarly, *reference assignment(引用赋值)* duplicates a reference to an object and assigns(分配) the reference to a reference variable. The object itself is not duplicated. **All ChucK object assignments are reference assignments.所有对象赋值都是引用赋值**

#built-in classes

ChucK has a number of classes defined(定义) within the language.

* `Object` : **base class to all ChucK objects.**
* `Event` : ChucK's basic synchronization(同步) mechanism(机制); may be extended(延伸) to *create custom Event* functionality(功能) (discussed [here](http://chuck.cs.princeton.edu/doc/language/event.html)).
* `Shred` : basic abstraction(抽象) for a non-preemptive ChucK process(非抢占式进程).
* `UGen` : base unit generator class (discussed [here](http://chuck.cs.princeton.edu/doc/language/ugen.html)).

These are some of the more commonly used classes in ChucK. 

#working with objects

Let's begin with some examples. For these examples, let's assume Foo is a defined class.

    // create a Foo object; stored in reference variable bar
    Foo bar;

The above code does two things:

* a reference variable bar is declared; its type is Foo.
* a new instance of Foo is created, and its reference is assigned to bar.

Note that in contrast to(与...相比) Java, *this statement both declares a reference variable and instantiates a instance of that class and assigns the reference to the variable*. 
Also note that in contrast to C++, *bar is a reference, and does not represent the object itself*.

To declare a reference variable that refers to nothing (also called a *null reference*):

    // create a null reference to a Foo object
    Foo @ bar;

The above code only declare a reference and initializes it to null. (random note: the above statement may be read as "Foo at bar")

We can assign a new instance to the reference variable:

    // assign new instance of Foo to bar
    new Foo @=> Foo @ bar;

    // (this statement is equivalent to 'Foo bar', above)

The `new` operator creates an instance of a class, in this case `Foo`. The `@=>` operator performs the *reference assignment*. (see [here](http://chuck.cs.princeton.edu/doc/language/oper.html) for more information on `@=>`)

It is possible to make many references to same object:

    // make a Foo
    Foo bar;

    // reference assign to duh
    bar @=> Foo @ duh;

    // (now both bar and duh points to the same object)

ChucK objects are reference counted(引用计数) and garbage collection takes place automatically. (note: this is still being implemented!)引用计数，垃圾回收

As stated above(如上所述), *a classes may contain data and behavior*, in the form of *member variables* and *member functions*, respectively. Members are accessed by using 'dot notation' - *reference.memberdata* and *reference.memberfunc()*. To invoke(调用) a member function of an object (assuming class `Foo` has a member function called `compute` that takes two integers and returns an integer):

```c
    // make a Foo
    Foo bar;

    // call compute(), store result in boo
    bar.compute( 1, 2 ) => int boo;
```

#writing a class

If a class has already *been defined in the ChucK virtual machine* (either in the same file or as a public class in a different file) then it can be instantiated *similar to primitive types*.

Unless declared *public*, class definitions are scoped to the shred and will not conflict with identically named classes in other running shreds.除非声明是公共的，类的定义作用于该进程，并将不会与在其他运行进程中的命名相同的类发生冲突。

Classes encapsulate(封装) a set of behaviors and data. To define a new object type, the keyword `class` is used followed by the name of that class.

```c++
// define class X
class X
{
    // insert code here
}
```

If a class is defined as `public`, it is integrated(整合) into *the central namespace* (instead of the local one), and can be instantiated from other programs that are subsequently compiled.
在随后编译的其他程序中，公共类也可以被实例化。

**There can be at most one public class per file.**(每个文件中至多一个公共类)

```c++
// define public class MissPopular
public class MissPopular
{
    // ...
}

// define non-public class Flarg
class Flarg
{
    // ...
}

// both MissPopular and Flarg can be used in this file
// only MissPopular can be used from another file
```

We define `member data` and `methods` to specify the **data types and functionality(数据类型和功能)** required of the class. Members, or instance data and instance functions are associated with *individual instances of a class*, whereas(然而) static data and functions are only associated with the class (and shared by the instances).
成员 或者 实例数据和实例函数，关联各个类实例，而静态数据和函数是关联于类的，各实例共享。

#members (instance data + functions)

Instance data and methods are associated with an object.

```c++
// define class X
class X
{
    // declare instance variable 'm_foo'
    int m_foo;
    // another instance variable 'm_bar'
    float m_bar;
    // yet another, this time an object
    Event m_event;

    // function that returns value of m_foo
    fun int getFoo() { return m_foo; }

    // function to set the value of m_foo
    fun void setFoo( int value ) { value => m_foo; }

    // calculate something
    fun float calculate( float x, float y )
    {
        // insert code
    }

    // print some stuff
    fun void print()
    {
        <<< m_foo, m_bar, m_event >>>;
    }
}

// instantiate an X
X x;

// set the Foo
x.setFoo( 5 );
// print the Foo
<<< x.getFoo() >>>;

// call print
x.print();
```

**class constructors**

>In the initial release(初始版本), we do not support *constructors* yet. However, we have a single pre-constructor. The code immediately inside a class definiton(定义) (and not inside any functions) is run every time an instance of that class is created.
类实例化时就自动运行类定义里而不是任何函数里的代码。

``` python
// define class X
class X
{
    // we can put any ChucK statements here as pre-constructor
    
    // initialize an instance data
    109 => int m_foo;

    // loop over stuff
    for( 0 => int i; i < 5; i++ )
    {
        // print out message how silly
        <<< "part of class pre-constructor...", this, i >>>;
    }

    // function
    fun void doit()
    {
        // ...
    }
}

// when we instantiate X, the pre-constructor is run
X x;

// print out m_foo
<<< x.m_foo >>>;
```
#static (data + functions)

Static data and functions are associated with a *class*, and are *shared by all instances of that class* -- in fact, static elements can be accessed without an instance, by using the name of the class: `Classname.element`.

```c++
// define class X
class X
{
    // static data
    static int our_data;

    // static function
    fun static int doThatThing()
    {
        // return the data
        return our_data;
    }
}

// do not need an instance to access our_data
2 => X.our_data;
// print out
<<< X.our_data >>>;
// print
<<< X.doThatThing() >>>;

// create instances of X
X x1;
X x2;

// print out their static data - should be same
<<< x1.our_data, x2.our_data >>>;

// change use one
5 => x1.our_data;

// the other should be changed as well
<<< x1.our_data, x2.our_data >>>;
```
静态数据是同一块内存

#inheritance

Inheritance in **object-oriented(面向对象的)** code allows the programmer to take an existing class to extend(扩展) or alter(改变) its functionality.  In doing so we can create a taxonomy(分类法) of classes that all share a specific(特定的) set of behaviors(行为), while implementing(实施) those behaviors in different, yet well-defined(定义明确的), ways. **We indicate(表明) that a new class inherits(继承) from another class using the `extends` keyword.** The class from which we inherit(继承) is referred to as the `parent class`, and the inheriting class is the `child class`. The *Child class receives all of the member data and functions from the parent class*, although functions from the parent class may be overridden(重写) ( below ). 
Because the children contain the functionality(功能) of the parent class, references to instances(实例) of a child class may be assigned(分配) to a parent class reference type. 

For now, access modifiers(存取修改器) (public, protected, private) are included but not fully implemented. *Everything is public by default*.

```c++
// define class X
class X
{
    // define member function
    fun void doThatThing()
    {
        <<<"Hallo">>>;
    }

    // define another
    fun void hey()
    {
        <<<"Hey!!!">>>;
    }

    // data
    int the_data;
}

// define child class Y
class Y extends X
{
    // override doThatThing()
    fun void doThatThing()
    {
        <<<"No! Get away from me!">>>;
    }
}

// instantiate a Y
Y y;

// call doThatThing
y.doThatThing();

// call hey() - should use X's hey(), since we didn't override
y.hey();

// data is also inherited from X
<<< y.the_data >>>;
```

>Inheritance provides us a way of efficiently sharing code between classes which **perform similar roles**. We can define a particular complex pattern of behavior, while changing the way that certain aspects of the behavior operate.

```c++
// parent class defines some basic data and methods 
class Xfunc
{ 
    int x; 

    fun int doSomething( int a, int b ) { 
        return 0; 
    }
}

// child class, which overrides the doSomething function with an addition operation
class Xadds extends Xfunc
{ 
    fun int doSomething ( int a, int b )
    { 
         return a + b ; 
    }
}

// child class, which overrides the doSomething function with a multiply operation 
class Xmuls extends Xfunc
{ 
    fun int doSomething ( int a, int b )
    { 
         return a * b; 
    }
}

// array of references to Xfunc
Xfunc @ operators[2];

// instantiate two children and assign reference to the array 
new Xadds @=> operators[0];
new Xmuls @=> operators[1];

// loop over the Xfunc
for( 0 => int i; i < operators.cap(); i++ )
{
    // doSomething, potentially different for each Xfunc
    <<< operators[i].doSomething( 4, 5 ) >>>;
}
```

because Xmuls and Xadds each redefine(重新定义) doSomething( int a, int b ) with their own code, we say that they have `overridden`(重写) the behavior of the parent class. They observe the same interface, but have potentially different implementation. 
This is known as **polymorphism(多态性)**.

#Overloading

Function overloading in classes is similar to that of regular functions. see functions.
