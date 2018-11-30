---
title: chuck3
date: 2018-02-15 21:40:10
categories: 
    - 编程与生活
tags: 
    - ChucK
---

#Types, Values, and Variables(变量)

ChucK is a strongly-typed language, meaning that types *are resolved(决定) at compile-time*. However, it is not quite statically-typed, because **the compiler/type system is a part of the ChucK virtual(虚拟的) machine, and is a runtime component(成分)**. This type system helps to impose(强加) precision(精度) and clarity(清楚) in the code, and naturally lends to organization of complex(复杂的) programs. At the same time, it is also *dynamic* in that changes to the type system can take place (in a well-defined(定义明确的) manner) at runtime. This dynamic(动态的) aspect(方面) forms the basis(基础) for [on-the-fly programming](http://on-the-fly.cs.princeton.edu/). 

This section deals with *types, values, and the declaration(声明) and usage(使用) of variables*. As in other strongly-typed programming languages, we can think of a type as associated(关联的) behaviors(行为) of data. (For example, an 'int' is a type that means integer(整数), and adding two integer is defined(定义) to produce a third integer representing the sum.) *Classes and objects* allow us to extend(延伸) the type system with our own custom types, but we won't cover them in this section. **We will focus mainly on *primitive(原始的) types* here, and leave the discussion of more complex types for [classes and objects](http://chuck.cs.princeton.edu/doc/language/class.html).**


#primitive types(原始类型)

The primitive(原始的), or intrinsic(本质的) types are those which are simple datatypes (they have no additional(附加的) data attributes(属性)). *Objects are not primitive types.* **Primitive types are passed by value. Primitive types cannot be extended(延伸).** The primitive types in ChucK are:

    int : integer(整数) (signed)
    float : floating point number (in ChucK, a float is by default double-precision(双精度))
    time : ChucKian time
    dur : ChucKian duration(持续)
    void : (no type)
    complex : complex number in rectangular(矩形的) form a + bi (see [below][1])
    polar: complex number in polar(极面) form (see [below][1])

For a summary of operations on these types, go to [operations and operators][2].

All other types are derived(派生) from 'object', either as part of the ChucK standard library, or as a new class that you create. For specification(规格), go to [classes and objects][3].

#values (literals(字面值))

Literal values are specified(指定) explicitly(明确地) in code and are assigned(分配) a type by the compiler(编译器).The following are some examples of literal values:

    int:

    42

    int (hexidecimal):

    0xaf30

    float:

    1.323

    dur:

    5.5::second
    //In the above code, second is an existing duration(持续) variable(变量). 
    //For more on durations, see the [manipulating time(时间调节)][4] section. 

#variables

**Variables(变量) are locations in memory that hold data.** *Variables have to be declared in ChucK before they are used.* For example, to declare a variable of type int called foo:

    // declare an 'int' called 'foo'
    int foo;

We can assign(分配) a value to *an existing variable* by using the ChucK operator (`=>`). This is one of the most commonly used operators in ChucK, it's the way to do work and take action! (后面会讨论)
    
    // assign value of 2 to 'foo'
    2 => foo;

It is possible to combine the two statements(声明) into one:

    // assign 2 to a new variable 'foo' of type 'int'
    2 => int foo;

To use a variable, just refer to it by name:

    // debug-print the value of foo
    <<< foo >>>;

To update the value of foo, for example:

    // multiply 'foo' by 10, assign back to 'foo'
    foo * 10 => foo;

You can also do the above using a `*=>` (mult-chuck):

    // multiply 'foo' by 10, and then assign to 'foo'
    10 *=> foo;

Here is an example of a duration(持续):

    // assign value of '5 seconds' to new variable bar
    5::second => dur bar;

Once you have bar, you can inductively(归纳的) use it to construct new durations:

    // 4 bar, a measure?
    4::bar => dur measure;

>输出：
C:\Users\abc1\Desktop>chuck test.ck
882000.000000 :(dur)

Since time is central to programming ChucK, it is important to understand time, dur, the relationship and operations between them. There is more information in the [manipulating time][4] section. 

#reference types(引用类型)

Reference(参考) types are types which inherit(继承) from the object class. Some default reference types include:

    Object : base type that all classes inherit from (directly or indirectly)所有类继承的基本类型
    array : N-dimensional ordered(有序的) set of data (of the same type)
    Event : fundamental(基本的), extendable(可扩展的), synchronization(同步) mechanism(机制)
    UGen : extendable unit generator(单元发生器) base class (U = unit, Gen = generator)
    string : string (of characters)

New classes can be created. **All classes are reference types.** We will leave the full discussion to the [objects and classes][3] section.

#complex types
(虚数：直角坐标系，极坐标系)

Two special `primitive types` are available to to represent complex (复杂的)data, such as the output (输出)of an FFT: complex and polar. A complex number of the form a + bi can be declared as

    #(2,3) => complex cmp; //cmp is now 2 + 3i 

where the #(...) syntax(语法) explicitly(明确地) denotes(表示) a complex number in rectangular form(矩形). Similarly, explicit complex numbers can be manipulated(操纵) directly:

    #(5, -1.5) => complex cmp; // cmp is 5 - 1.5i
    #(2,3) + #(5,6) + cmp => complex sum; // sum is now 12 + 7.5i

The (floating point) real and imaginary parts(实虚部) of a complex number can be accessed(存取) with the .re and .im components of a complex number:

    #(2.0,3.5) => complex cmp;
    cmp.re => float x; // x is 2.0
    cmp.im => float y; //y is 3.5

The polar type(极坐标) offers an equivalent(等价的), alternative(供选择的) representation(表示) of complex numbers in terms of a magnitude(大小) and phase(相) value. A polar representation of a complex number can be declared as

    %(2, .5*pi) => polar pol; // pol is 2∠.5π(没有想到，竟然有这个(●'◡'●))

The magnitude(大小) and phase(相) values can be accessed via .mag and .phase:

    %(2, .5*pi) => polar pol;
    pol.mag => float m; // m is 2
    pol.phase => float p; //p is .5π

polar and complex representations(代表) can be cast to each other and multiplied/added/assigned(分配)/etc.:

    %(2, .5*pi) => polar pol;
    #(3, 4) => complex cmp;
    pol $ complex + #(10, 3) + cmp => complex cmp2;
    cmp $ polar + %(10, .25*pi) - pol => polar pol2;

(这里的$实现了两种坐标系下表示的转换)
>输出：
C:\Users\abc1\Desktop>chuck test.ck
%(2.0000,0.5000*pi) :(polar)
\#(3.0000,4.0000) :(complex)
\#(13.0000,9.0000) :(complex)
%(13.5540,0.2334*pi) :(polar)

[1]: http://chuck.cs.princeton.edu/doc/language/type.html#complex
[2]: http://chuck.cs.princeton.edu/doc/language/oper.html
[3]: http://chuck.cs.princeton.edu/doc/language/class.html
[4]: http://chuck.cs.princeton.edu/doc/language/time.html
