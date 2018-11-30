---
title: chuck2
date: 2018-02-15 21:40:10
categories: 
    - 编程与生活
tags: 
    - ChucK
---

#Overview

ChucK is a strongly-typed, strongly-timed, concurrent(并发的) audio and multimedia(多媒体的) programming language. It is compiled(编译) into virtual(虚拟的) instructions, which is immediately run in the ChucK Virtual Machine. This guide documents the features(特色) of the Language, Compiler, and Virtual Machine for a ChucK programmer. 

#running ChucK

...this is a quick overview(综述), see [VM options](http://chuck.cs.princeton.edu/doc/program/vm.html) for a more complete guide to command line options. 

To run ChucK with a program/patch called foo.ck simply run chuck and then the name of the file:

    chuck foo.ck 

To run ChucK with multiple patches concurrently(同时发生) (or the same one multiple times):

    chuck foo.ck bar.ck bar.ck boo.ck 

...the following probes(探查) the audio system and prints out all available audio devices and MIDI devices. You may then refer to them (by number usually) from the command line or from your program. (again, see VM Options for a complete list)

    chuck --probe 

ChucK can be run in a different terminal as a host/listener that patches may be sent to. The server should invoke(调用) the --loop flag to specify that the virtual machine should not halt(停止) automatically(自动地) (when the current programs exit).

    chuck --loop     

(See the guide to [On-the-fly Programming](http://chuck.cs.princeton.edu/doc/program/otfp.html) for more information)

If a ChucK listener is running, we can (from a second terminal) send a program/patch to the listener by using the `+` command line option:

    chuck + foo.ck 

Similarly, you can use `-` and `=` to *remove/replace* a patch in the listener, and use `^` to find out the status. Again, see [On-the-fly Programming](http://chuck.cs.princeton.edu/doc/program/otfp.html) for more information.

To run most of the code or examples in this language specification, you only need to use the basic chuck program.

#comments(注释)

(Note: block comments cannot be nested(嵌入的))

    // this is a comment
    int foo; // another comment

    /* 
       this is a block comment
       still going...
    */

#debug print(调试输出)

...we have provided a debug print syntax(语法):

    // prints out value of expression
    <<< expression >>>;

This will print the values and types of any expressions placed within them. This debug print construction may be placed around any non-declaration expression ( non l-value ) and will not affect the execution(执行) of the code. Expressions which represent an object will print the value of that object's reference(引用) address:

    // assign 5 to a newly declared variable
    5 => int i;
    // prints "5 : (int)"
    <<<i>>>;

    // prints "hello! : (string)"
    <<<"hello!">>>; //prints "hello! : (string)"

    // prints "3.5 : (float)"
    <<<1.0 + 2.5 >>> => float x;

(ChucK中似乎是没有了print这一类)
(Chuck中有`;`，还是类似C的)

For more formatted(格式化) data output(输出), a comma-separated list of expressions will print only their respective(分别的) values (with one space between):

    //接上段
    // prints "the value of x is 3.5" (x from above)
    <<<"the value of x is" , x >>>;

    // prints "4 + 5 is 9"
    <<<"4 + 5 is", 4 + 5>>>;

    // prints "here are 3 random numbers ? ? ?"
    <<<"here are 3 random numbers", 
        Std.rand2(0,9), 
        Std.rand2(0,9),
        Std.rand2(0,9) >>>; 

>输出：
C:\Users\abc1\Desktop>chuck "debug print.ck"
5 :(int)
"hello!" : (string)
3.500000 :(float)
the value of x is 3.500000
4 + 5 is 9
here are 3 random numbers 1 8 8

#reserved words(关键字)

    (primitive types原始类型)
        int
        float
        time
        dur
        void
        same (unimplemented未实现的) 

    (control structures)
        if
        else
        while
        until
        for
        repeat
        break
        continue
        return
        switch (unimplemented) 

    (class keywords)
        class
        extends
        public
        static
        pure
        this
        super (unimplemented)
        interface (unimplemented)
        implements (unimplemented)
        protected (unimplemented)
        private (unimplemented) 

    (other chuck keywords)
        function
        fun
        spork
        const
        new 

    (special values)
        now
        true
        false
        maybe
        null
        NULL
        me
        pi 

    (special : default durations(默认持续时间))
        samp
        ms
        second
        minute
        hour
        day
        week 

    (special : global ugens(不理解))
        dac
        adc
        blackhole 


