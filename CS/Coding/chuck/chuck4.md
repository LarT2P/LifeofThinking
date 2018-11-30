---
title: chuck4
date: 2018-02-15 21:40:10
categories: 
    - 编程与生活
tags: 
    - ChucK
---

arrays
    * declaring
    * multi-dimensional
    * lookup
    * associative arrays
    * assignment 

#Arrays

This sections specifies(指定) how arrays are declared and used in ChucK. Some quick notes:
    * arrays can be indexed by integer(整数) (0-indexed).
    * **any array can also be used as an associative(联想的) map, indexed by strings.**
    * it is important to note that the *integer-indexed portion(部分)* and the *associative portion* of an array are stored in separate namespaces
    * arrays are objects, and will behave similarly under *reference assignment(引用赋值)* and other operations common to objects. 

#declaration

Arrays(数组) can be declared in the following way:

    // declare 10 element array of int, called foo
    int foo[10];

    // since array of int (primitive type), the contents
    // are automatically initialized to 0

Arrays can also be initialized(初始化):

    // chuck intializer(初始化器，程序) to array reference
    [ 1, 1, 2, 3, 5, 8 ] @=> int foo[];

In the above code, there are several things to note:
    * initializers must contain *the same or similar types*. the compiler(编译器) will attempt to find *the highest common base type of all the elements(元素)*. **if no such common element is found, an error is reported.**
    * the type of the initializer `[ 1, 1, 2, 3, 5, 8 ]` is int[]. *the intializer is an array that can be used directly* when arrays are expected.
    * the at-chuck operator (`@=>`) means assignment(分配), and is discussed at length(详细地) in operators and operations.
    * `int foo[]` is declaring an empty reference(引用) to an array(数组). the statement(声明) assigns(分配) the initializer array to foo.
    * arrays are objects. 

When declaring an array of objects, the objects inside the array are automatically(自动的) instantiated(实例化).

    // objects in arrays are automatically instantiated
    Object group[10];

If you only want an array of object references:(对象引用数组)

    // array of null object references
    Object @ group[10];

[Check here](http://chuck.cs.princeton.edu/doc/language/class.html) more information on object declaration and instantation in Chuck.

The above examples are 1-dimensional arrays (or vectors(矢量)).

#multi-dimensional arrays

It is possible (and equally easy) to declare multi-dimensional arrays:

    // declare 4 by 6 by 8 array of float
    float foo3D[4][6][8];//和C还是很像

Initializers work in similar ways:

    // declare 2 by 2 array of int
    [ [1,3], [2,4] ] @=> int bar[][];

In the above code, note the two `[][]` since we make a matrix. 


#lookup

Elements in an array can be accessed using [] /(in the appropriate quantities).

    // declare an array of floats
    [ 3.2, 5.0, 7 ] @=> float foo[];

    // access the 0th element (debug print)
    <<< foo[0] >>>; // hopefully 3.2

    // set the 2nd element
    8.5 => foo[2];

Looping over the elements of an array:

    // array of floats again
    [ 1, 2, 3, 4, 5, 6 ] @=> int foo[];
    //官方的例子这里是float，但是实际测试，整型不可以被分配到float里。

    // loop over the entire array
    for( 0 => int i; i < foo.cap(); i++ )//foo.cap()
    {
        // do something (debug print)
        <<< foo[i] >>>;//自带换行
    }

Accessing multi-dimensional array:

    // 2D array
    int foo[4][4];

    // set an element
    10 => foo[2][2];

If the index exceeds(超过) the bounds of the array in any dimension(标出尺寸), an exception(异常) is issued(被放出) and the current shred(片段) is halted(停止).

    // array capacity is 5
    int foo[5];

    // this should cause ArrayOutOfBoundsException
    // access element 6 (index 5)
    <<< foo[5] >>>;

a longer program: `otf_06.ck` from [examples](http://chuck.cs.princeton.edu/doc/examples/):

    // the period
    .5::second => dur T;
    // synchronize to period (for on-the-fly(运行中) synchronization)
    T - (now % T) => now;

    // our patch
    SinOsc s => JCRev r => dac;//JCRev是指什么类型？
    // initialize
    .05 => s.gain;
    .25 => r.mix;

    // scale (pentatonic(五音阶的); in semitones(半音程))
    [ 0, 2, 4, 7, 9 ] @=> int scale[];

    // infinite time loop
    while( true )
    {
        // pick something from the scale
        scale[ Math.rand2(0,4) ] => float freq;
        // get the final freq
        Std.mtof( 69 + (Std.rand2(0,3)*12 + freq) ) => s.freq;
        // reset phase for extra bandwidth(为额外带宽重置相位)
        0 => s.phase;

        // advance time
        if( Std.randf() > -.5 ) .25::T => now;
        else .5::T => now;
    }

#associative arrays(关联数组)
(类似于python中的字典，用字符串索引)

Any array(数组) can be used also as an associative array, indexed on strings. Once the regular array is instantiated, no further work has to be done to make it associative as well - just start using it as such.

    // declare regular array(正规数组) (capacity(容量) doesn't matter so much)
    float foo[4];

    // use as int-based array
    2.5 => foo[0];

    // use as associative array
    4.0 => foo["yoyo"];

    // access as associative (print)
    <<< foo["yoyo"] >>>;

    // access empty element
    <<< foo["gaga"] >>>;  // -> should print 0.0

It is important to note (again), that the address space of the integer(整数) portion(部分) and the associative portion of the array are completely separate. For example:

    // declare array
    int foo[2];

    // put something in element 0
    10 => foo[0];

    // put something in element "0"
    20 => foo["0"];

    // this should print out 10 20
    <<< foo[0], foo["0"] >>>;

The capacity(容量) of an array relates only to the integer portion of it. An array with an integer portion of capacity 0, for example, can *still have any number of associative indexes.*

    // declare array of 0 capacity
    int foo[0];

    // put something in element "here"
    20 => foo["here"];

    // this should print out 20
    <<< foo["here"] >>>

    // this should cause an exception
    <<< foo[0] >>>

>**Note**: The associative capacity of an array is not defined(定义), so objects used in the associative namespace must be explicitly(明确的) instantiated, in contrast(对比) to those in the integer namespace
(**此处不甚明白**)

Accessing an uninstantiated element(元素) of the associate array will return a null reference. Please check the [class documentation page](http://chuck.cs.princeton.edu/doc/language/class.html) for an explanation of ChucK objects and references.

    class Item { 
       float weight; 
    }
    
    Item box[10]; 

    // integer indices(指数；目录（index的复数）) ( up to capacity ) are pre-instantiated.
    1.2 => box[1].weight; 

    // instantiate element "lamp";
    new Item @=> box["lamp"]; 

    // access allowed to "lamp"
    2.0 => box["lamp"].weight; 

    // access causes a `NullPointerException`    
    2.0 => box["sweater"].weight; 

#array assignment

Arrays are objects. So when we declare an array, we are actually 

    (1) declaring a reference to array (reference variable(引用变量))
    (2) instantiating a new array and reference assigned(分配) to the variable. 

A (null) reference is a reference variable that points to no object or null. A null reference to an array can be created in this fashion:

    // declare array reference (by not specifying(指定) a capacity)
    int foo[];

    // we can now assign any int[] to foo
    [ 1, 2, 3 ] @=> foo;

    // print out 0th element
    <<< foo[0] >>>;

This is also useful in declaring functions that have arrays(数组) as arguments or return type.

    // our function
    fun void print( int bar[] )
    {
        // print it
        for( 0 => int i; i < bar.cap(); i++ )
            <<< bar[0] >>>;
    }

    // we can call the function with a literal
    print( [ 1, 2, 3, 4, 5 ] );

    // or can we can pass a reference variable
    int foo[10];
    print( foo );

Like other objects, it is possible make multiple references(多个引用) to a single array. Like other objects, all assignments(分配) are *reference assignments*, meaning the contents are NOT copied, only a reference to array is duplicated(被复制).(传递引用)

    // our single array
    int the_array[10];

    // assign reference to foo and bar
    the_array => int foo[] => int bar[];

    // (the_array, foo, and bar now all reference the same array)

    // we change the_array and print foo...
    // they reference the same array, changing one is like changing the other
    // 连锁反应，内存中的存储信息改变了
    5 => the_array[0];
    <<< foo[0] >>>; // should be 5

It is possible to reference sub-sections(小节) of multi-dimensional(多维) arrays.

    // a 3D array
    int foo3D[4][4][4];

    // we can make a reference to a sub-section
    foo3D[2] => int bar[][];

    // (note that the dimensions must add up!)

