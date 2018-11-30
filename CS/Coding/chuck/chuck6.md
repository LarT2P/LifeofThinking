---
title: chuck6
date: 2018-02-15 21:40:10
categories: 
    - 编程与生活
tags: 
    - ChucK
---

control structures

* if / else
* while & do...while...
* until
* for
* break / continue

#Control Structures

ChucK includes standard control structures similar to those in most programming languages. A condition (of type 'int') is evaluated and then a proceeding block is potentially executed. Blocks are separated either by semicolons(分号) or by curly brackets(花括号). 

#if / else

The if statement executes a block if the condition is evaluated as *non-zero*.

```c
if( condition )
{
    // insert code here
}
```

In the above code, condition is any expression that evaluates to an int. (条件是一个值为整数的表达式)

The else statement(声明) can be put after the if block to handle the case where the condition evaluates(求值) to 0.

```c
if( condition )
{
    // your code here
}
else
{
    // your other code here
}
```

If statements can be nested(嵌套).

#while & do...while...

```c
// here is an infinite loop
while( true )
{
    // your code loops forever!

    // (sometimes this is desirable because we can create
    // infinite time loops this way, and because we have
    // concurrency)
} 
```

```c
do {
    // your code executes here at least once
} while( condition );
```

A few more points:

- `while` statements can be nested.
- see `break/continue` for additional(附加的) control over your loops 

#until

The until statement is the opposite of while, semantically(语义地). A until loop repeatedly executes the body until the condition evaluates as non-zero.

```c
// an infinite loop
until( false )
{
    // your great code loops forever!
}
```

A few more points:

- `while` statements can be nested.
- see `break/continue` for additional(附加的) control over your loops 


#for

A loop that iterates(迭代) a given number of times.(迭代给定次数) A temporary(暂时的) variable(变量) is declared that keeps track(跟踪) of the current index and is evaluated and incremented at each iteration(迭代).

```c
// for loop
for( 0 => int foo; foo < 4 ; foo++ )
{
    // debug-print value of 'foo'
    <<<foo>>>;
}
```

#break / continue

`Break` allows the program flow to jump out of a loop.

```c
// infinite loop
while( 1 )
{
    if( condition ) 
        break;
}
```

`Continue` allows a loop to continue looping but not to execute(实行) the rest of the block for the iteration where continue was executed.(跳过当前的循环)

```c
// another infinite loop
while( 1 )
{
    // check condition
    if( condition )
        continue;

    // some great code that may get skipped (if continue is taken)
}
```

