---
title: chuck9
date: 2018-02-15 21:40:10
categories: 
    - 编程与生活
tags: 
    - ChucK
---

concurrency, processes, shreds

    sporking shreds
    the 'me' keyword
    using machine.add()
    inter-shred communication 

#Concurrency & Shreds

...is able to run many processes concurrently...
 
**A ChucKian process is called a shred.** To *spork* a shred means **creating and adding a new process to the virtual(虚拟的) machine**. Shreds may be sporked from a variety of places(各种各样的地方), and may themselves spork new shreds.

(直接代换理解：spork a shred == creating & adding a process)

>ChucK supports sample-synchronous(采样同步), non-preemptive concurrency(非抢占式并发). Via the timing mechanism(机制). any number of programs/shreds can be automatically(自动的) *shreduled*(往后看，便于理解，实际上就是创建添加的过程，感觉就像是shred动作化了) and synchronized(同步的) use the timing information.A *shreduler* in the virtual(虚拟的) machine does the *shreduling*. The concurrency(并发性) is 'sample-synchronous', meaning that inter-process audio timing is guaranteed(保证) to be precise(精确的) to the sample(样品). **Note** that each process/shred does not necessarily need to know about each other - it only has to deal with time locally. The virtual machine will make sure things happen correctly "across the board(全体，全面)". Finally, concurrency - like timing - is deterministic(确定性的) in ChucK. 

The simplest way to to run shreds concurrently is to specify(指定) them on the command line:

    chuck foo.ck bar.ck boo.ck

The above command runs chuck on foo.ck, bar.ck, and boo.ck concurrently. There are other ways to run shreds concurrently (see [on-the-fly programming commands](http://chuck.cs.princeton.edu/doc/program/otfp.html)). Next, we show how to create new shreds from within ChucK programs.

#sporking shreds (in code)

To *spork* means to *shredule a new shred.*

To spork a shred, use the *spork* keyword/operator:

* *spork* dynamically(动态地) sporks shred from a function call从函数调用中动态创建进程
* this operation is sample-synchronous, the new shred is shreduled to execute(执行) immediately新进程创建来立即执行
* the parent shred continues to execute, until time is advanced (see manipulating time) or until the parent explicitly(显式地) yields(产生) (see next section).父进程继续执行，直到时间被推进或者直到父进程显示产生。
* in the current implementation(实现), when a parent shred exits, all child shreds all exit (this behavior(行为) will be enhanced(提高) in the future.)在当前实现中，当父进程退出，所有的子进程会退出。
* *sporking* a functions returns reference to the new shred, **note** that this operation does not return what functions returns - *the ability to get back the return value at some later point in time will be provided in a future release.*产生一个函数返回新进程的引用，注意这个操作不会返回函数返回值-返回在某个靠后的时间点上的返回值的能力就被在未来的版本中提供。

```c
// define function go()
fun void go()
{
    // insert code
}

// spork a new shred to start running from go()
spork ~ go();

// spork another, store reference to new shred in offspring
spork ~ go() => Shred @ offspring;
```

a slightly longer example:

```c
// define function
fun void foo( string s )
{
   // infinite time loop
   while( true )
   {
       // print s
       <<< s >>>;
       // advance time
       5::second => now;
   }
}

// spork shred, passing in "you" as argument to foo
spork ~ foo( "you" );
// advance time by 250 ms
2::second => now;
// spork another shred
spork ~ foo( "me" );

// infinite time loop - to keep child shreds around
while( true )
    1::second => now;

//测量：
//输出you后，第2s输出me，第5s再次输出

```

    两个进程互有交错，"you"先输出，"me"后输出，循环输出。

    个人理解：
    `1::second => now;`感觉上就像是将进程挂起，sleep()的感觉
    就像是1s后再跳到这个程序

```sequence
you->me:2s
me->you:3s
```

#the 'me' keyword

The `me` keyword (type `Shred`) refers the current shred. 

Sometimes it is useful to suspend(暂停) the current shred without advancing time, and let other shreds shreduled for the current time to execute(实行). `me.yield()` does exactly that. **This is often useful immediately after sporking a new shred, and you would like for that shred to have a chance to run but you do not want to advance time yet for yourself.**

```c
// spork shred
spork ~ go();

// suspend the current shred ...
// ... give other shreds (shreduled for 'now') a chance to run
me.yield();
```

It may also be useful to exit the current shred. For example if a MIDI device(装置) fails to open, you may exit the current shred.

```c
// make a MidiIn object
MidiIn min;

// try to open device 0 (chuck --probe to list all device)
if( !min.open( 0 ) )
{
    // print error message
    <<< "can't open MIDI device" >>>;
    // exit the current shred
    me.exit();
}
```

You can get the shred id:

```c
// print out the shred id
<<< me.id(); >>>;
```

These functions are common to all shreds, but `yield()` and `exit()` are commonly used with the current shred.

#using machine.add()

`Machine.add( string path )` takes the path to a chuck program, and sporks it. *Unlike `spork ~`, there is no parent-child relationship between the shred that calls the function and the new shred that is added*. This is useful for dynamically(动态地) running stored programs.

```c
// spork "foo.ck"
Machine.add( "foo.ck" );
```

Presently, **this returns the id of the new shred**, not a reference to the shred. *This will likely be changed in the future.*

Similarly, you can remove shreds from the virtual(虚拟的) machine.

```c
// add
Machine.add( "foo.ck" ) => int id;

// remove shred with id
Machine.remove( id );

// add
Machine.add( "boo.ck" ) => id

// replace shred with "bar.ck"
Machine.replace( id, "bar.ck" );
```

#inter-shred communication

>Shreds sporked in the same file can share the same global variables(变量). They can *use time and events to synchronize to each other*. (see `events`) **Shreds sporked from different files can *share data* (including events).**For now, this is done through a public class with static data (see `classes`). Static data is not completely implemented! We will fix this very soon!

#command line arguments

**ChucK supports passing arbitrary(任意的) data from the command line into ChucK programs using optional(可选择的) command line arguments.** An argument is specified(指定) by appending(附加) a colon(冒号) character ":" to the name of the ChucK program to which you wish to send that argument, followed by the argument itself.

    chuck foo.ck:foo

Multiple arguments can be specified, each separated by the colon character.

    chuck foo.ck:foo:bar:boo

Furthermore(此外), each ChucK program has *its own set of arguments*, which are *specified separately*.

    chuck foo.ck:foo bar.ck:bar boo.ck

Command line arguments can also be used when using on-the-fly programming facilities(即时编程工具) of ChucK.

    chuck + foo.ck:foo bar.ck:bar:boo

(后面有一篇博文会讲解相关参数功能的，包括这里的`+`)

Machine.add() and Machine.replace() accept command line arguments in a similar fashion.

```c
// add foo.ck
// pass foo and bar as command line arguments
Machine.add( "foo.ck:foo:bar" ) => int id;

// replace shred with "bar.ck"
// pass foo and bar as command line arguments
Machine.replace( id, "bar.ck:foo:bar" );
```

To access command line arguments within a ChucK program, use the `me.args()` and `me.arg()` functions.

```c
// print out all arguments
for( int i; i < me.numArgs(); i++ )
    <<< me.arg( i ) >>>;
```
>直接运行会输出：
C:\Users\abc1\Desktop>chuck test.ck:foo
[test.ck]:line(2): class 'Shred' has no member 'numArgs'

(真就尴尬了)
