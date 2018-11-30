---
title: chuck5
date: 2018-02-15 21:40:10
categories: 
    - 编程与生活
tags: 
    - ChucK
---

operators & operations

* => (chuck)
* + - * / (arithmetic)
* % (modulo)
* && || == != >= <=" (logic)
* & | ^ (bitwise)
* ++ -- (inc / dec)
* ! + - new (unary)

You may have seen many of the operators in other programming languages (C/Java). *Some others are native to ChucK*. We start with the family of ChucK operators. 

#=> (the ChucK operator) 

The ChucK operator (`=>`) is a massively(大量地) overloaded operator that, depending on the types involved(包含), performs various actions(执行各种操作). `It denotes(表示) **action**, can be chained(链接), and imposes(强制执行) and clarifies order(理清顺序) (always goes *from left to right*).` The ChucK operator is the means by which work is done in ChucK. Furthermore(此外), the ChucK operator is not a single operator, but a family of operators.

##=> (foundational(基础的) ChucK operator) 

We start with the standard, plain-vanilla(普通的，无修饰的) ChucK operator (`=>`). It is left-associative (**all ChucK operators are**), which allows us to specify(指定) any ordered flow of data/tasks/modules (such as unit generator connection) from left to right, as in written (English) text. **What `=>` does depends on the context(环境).** It always depends on the type of the entity(实体) on the left (the chucker(投手)) and the one on the right (the chuckee(接球手)), and it sometimes also depends on the nature of the entity (such as whether it is a variable(变量的) or not).

Some examples:
(用了c的高亮，至少好看点)

```c
// a unit generator patch - the signal flow is apparent
// (in this case, => connects two unit generators)
SinOsc b => Gain g => BiQuad f => dac;

// add 4 to foo, chuck result to new 'int' variable 'bar'
// (in this case, => assigns a value to a variable (int)
4 + foo => int bar;

// chuck values to a function == function call
// (same as Math.rand2f( 30, 1000))
( 30, 1000 ) => Math.rand2f;
```

There are many other well-defined(定义明确的) uses of the ChucK operator, depending on the context.

##`@=>` (explicit(明确的) assignment(分配) ChucK operator)

In ChucK, *there is no stardard assignment operator (=)*, found in many other programming languages. Assignment is carried out using ChucK operators. In the previous examples, we have used => for assignment:

```c
// assign 4 to variable foo
4 => int foo;

// assign 1.5 to variable bar
1.5 => float bar;

// assign duration of 100 millisecond to duh
100::ms => dur duh;

// assign the time "5 second from now" to later
5::second + now => time later;
```

The `@=>` explicit assignment ChucK operator behaves exactly the same for the above types (int, float, dur, time). However, the difference is that `@=>` can also be used for reference assignments(分配) of objects (see objects and classes) whereas(然而) `=>` only does assignment on primitive(原始的) types (int, float, dur, time). The behavior(行为) of `=>` on objects is completely context-dependent(上下文相关的).

```c
// using @=> is same as => for primitive types
4 @=> int foo;

// assign 1.5 to variable bar
1.5 @=> float bar;

// (only @=> can perform reference assignment on objects)

// reference assign moe to larry
// (such that both moe and larry reference the same object)
Object moe @=> Object @ larry;

// array initialization
[ 1, 2 ] @=> int ar[];

// using new
new Object @=> moe;
```

In its own screwed-up(糟透了的) way, this is kind of nice because there is no confusion(混淆) between assignment (`@=>` or `=>`) and equality (`==`). In fact,  the following is not a valid ChucK statement(声明):

    // not a valid ChucK statement!
    int foo = 4;

##+=>  -=>  *=>  /=>  etc. (arithmetic(算数) ChucK operators)

These operators are used with variables(变量) (using 'int' and 'float') to perform one operation with assignment(执行一个有分配的操作).

```c    
// add 4 to foo and assign result to foo
foo + 4 => foo;

// add 4 to foo and assign result to foo
4 +=> foo;

// subtract 10 from foo and assign result to foo
// remember this is (foo-10), not (10-foo)
10 -=> foo;

// 2 times foo assign result to foo
2 *=> foo;

// divide 4 into foo and assign result to foo
// again remember this is (foo/4), not (4/foo)
4 /=> foo;
```

It is important to note the relationship between the value and variable when using `-=>` and `/=>`, since these operations are not commutative(交换的).

```c
// mod foo by T and assign result to foo
T %=> foo;

// bitwise(按位) AND 0xff and bar and assign result to bar
0xff &=> bar;

// bitwise OR 0xff and bar and assign result to bar
0xff |=> bar;
```

#+  -  *  /  (arithmetic)

```c
// divide (and assign)
16 / 4 => int four;

// multiply
2 * 2 => four;

// add
3 + 1 => four;

// subtract
93 - 89 => four;
```

**cast(转换)**

*ChucK implicitly(隐式地) casts int values to float when float is expected, but not the other around.* The latter could result in a loss of information and requires an explicit(明确的) cast.

```c
// adding float and int produces a float
9.1 + 2 => float result;

// however, going from float to int requires cast
4.8 $ int => int foo;  // foo == 4
//这里的$是什么意思？

// this function expects two floats
Math.rand2f( 30.0, 1000.0 );

// this is ok because of implicit cast
Math.rand2f( 30, 1000 );
```

#% (modulo(取模))

The modulo operator `%` computes the remainder(余数) after integer(整数), floating point, duration, and time/duration division.

```c
// 7 mod 4 (should yield(产生) 3)
7 % 4 => int result;

// 7.3 mod 3.2 floating point mod (should yield .9)
7.3 % 3.2 => float resultf;

// duration mod
5::second % 2::second => dur foo;

// time/duration mod
now % 5::second => dur bar;
```

the latter (time/duration mod) is one of many ways to dynamically(动态地) synchronize(同步) timing in shreds. the examples `otf_01.ck` through `otf_07.ck` (see under `examples`) make use of this to on-the-fly(运行时) synchronize its various parts, no matter when each shred is added to the virtual(虚拟的) machine:

```c
// define period (agreed upon by several shreds)
.5::second => dur T;

// compute the remainder(余数) of the current period ...
// and advance time by that amount
T - (now % T) => now;

// when we reach this point, we are synchronized to T period boundary

// the rest of the code
// ...
```

This is one of many ways to compute and reason about(思考) time in ChucK. The appropriate(适当的) solution(解决方案)(s) in each case depends on the intended(打算的) functionality(功能). 

#&&  ||  ==  !=  >  >=  <  <=  (logic)(逻辑)

Logical operators - each of these need two operands(操作数). **The result is an integer value of 0 or 1.**

* && : and
* || : or
* == : equals
* != : does not equal
* > : greater than
* >= : greater than or equal to
* < : less than
* <= : less than or equal to 

```c
// test some universal truths(普遍的真理)
if( 1 <= 4 && true )
    <<<"horray">>>;
```

#>>  <<  &  |  ^  (bitwise)(按位)

These are used on int values at the bit level, often for bit masking.

```c
>> : shift bits right(右移位) ( 8 >> 1 = 4 )
<< : shift bits left ( 8 << 1 = 16 )
& : bitwise AND
| : bitwise OR
^ : bitwise XOR 
```

#++  --  (inc / dec)
Values may be incremented or decremented by appending(附加) the `++` or `--` operators.

```c
4 => int foo;
foo++;
foo--;
```

#! + - new (unary(一元的))

These operators come before(位于...之前) one operand(操作数).

```c
// logical invert
if( !true == false )
    <<<"yes">>>;

// negative
-1 => int foo;

// instantiate object
new object @=> object @ bar;
```

