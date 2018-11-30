---
title: chuck8
date: 2018-02-15 21:40:10
categories: 
    - 编程与生活
tags: 
    - ChucK
---

functions

    writing
    calling
    overloading 

#writing

keyword `fun` (or `function`) followed by the *return type* and then *the name* of the function. After the name of the function parentheses(括号) must be opened to declare the types of the input arguments.

```c
    // define function call 'funk'
    fun void funk( int arg )
    {
        // insert code here
    }
```
```c
    // define function 'addOne'
    fun int addOne(int x)
    {
        // result
        return x + 1;
    }
```

#calling

```c
    // define 'hey'
    fun int hey( int a, int b )
    {
        // do something
        return a + b;
    }

    // call the function; store result
    hey( 1, 2 ) => int result;
```

You can also use the ChucK operator to call functions!

```c
    // call hey
    ( 1, 2 ) => hey => int result;

    // same
    hey( 1, 2 ) => int result;

    // several in a row
    ( 10, 100 ) => Std.rand2 => Std.mtof => float foo;

    // same 返回值做参数
    Std.mtof( Std.rand2( 10, 100 ) ) => float foo;
```

#overloading

Overloading a function allows functions *with the same name* to be defined(定义) *with different arguments*. The function must be written in **separate instances(实例) to handle the input**, and the **return type must agree(一致)**.

```c
    // funk( int )
    fun int add(int x)
    {
        return x + x;
    }

    // funk( int, int )
    fun int add(int x, int y)
    {
        return x + y;
    }

    // compiler automatically choose the right one to call
    add( 1 ) => int foo;
    add( 1, 2 ) => int bar;
```

