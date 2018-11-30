---
title: chuck1
date: 2018-02-15 21:40:10
categories: 
    - 编程与生活
tags: 
    - ChucK
---

* [ Build/Install Instructions ](http://chuck.cs.princeton.edu/doc/build/)
如是直接安装（win）可以忽略上面的。*

* [The ChucK Tutorial ](http://chuck.cs.princeton.edu/doc/learn/tutorial.html)*

This tutorial(个别指导) was written for the command line version of ChucK (currently the most stable(稳定的) and widely supported). Other methods of running ChucK includes the miniAudicle (now on all major platforms) and the Audicle (in pre-pre-alpha). *The code is the same, but the way to run them differs, depending the ChucK system.*

...generate a sine wave and send to the speaker...

...connecting audio processing modules ([unit generators](https://en.wikipedia.org/wiki/Unit_generator)) and having them work together to compute the sound.

      // connect sine oscillator to D/A convertor (sound card)
      SinOsc s => dac;

The above does several things:

    (1) it creates a new unit generator of type 'SinOsc' (sine oscillator(振荡器)), and store its reference(引用) in variable(变量的) 's'. 
    (2) 'dac' (D/A convertor(转炉)) is a special *unit generator* (created by the system) which is our abstraction(抽象) for the underlying(潜在的) audio interface(界面). 
    (3) we are using the ChucK operator (=>) to ChucK 's' to 'dac'. 

In ChucK, when one unit generator is ChucKed to another, we connect them. We can think of this line as setting up *a data flow from 's'*, a signal generator, to *'dac', the sound card/speaker*. Collectively(集体的), we will call this a 'patch'. 

...we simply have to "allow time to pass" for data to be computed...time and audio data are both inextricably(密不可分) related in ChucK (as in reality), and separated in the way they are manipulated(操纵).

let's generate our sine wave and hear it by adding one more line: 

    // connect sine oscillator to D/A convertor (sound card)
    SinOsc s => dac;

    // allow 2 seconds to pass
    2::second => now;

Let's now run this (assuming(假设) you saved the file as 'foo.ck'):

    %> chuck foo.ck

 For now, we can just take the second line of code to mean "let time pass for 2 seconds (and let audio compute during that time)". If you want to play it indefinitely(无限制地), we could write a loop:

    ////////////
    //example1//
    ////////////
    // connect sine oscillator to D/A convertor (sound card)
    SinOsc s => dac;

    // loop in time
    while( true ) {
        2::second => now;
        //(we used 2::second here, but we could have used any number of 
        //'ms', 'second', 'minute', 'hour', 'day', and even 'week')
        }

In ChucK, this is called a 'time-loop' (in fact this is an 'infinite time loop').
**To stop a ongoing ChucK program from the command line, hit (ctrl - c).**

Now, let's try changing the frequency randomly every 100ms:

    // make our patch
    SinOsc s => dac;

    // time-loop, in which the osc's frequency is changed every 100 ms
    while( true ) {
        100::ms => now;
        Std.rand2f(30.0, 1000.0) => s.freq;
        }

Two more things to note here. 

    (1) We are advancing time inside the loop by 100::ms durations. 
    (2) A random value between 30.0 and 1000.0 is generated and 'assigned' to the oscillator's frequency, every 100::ms. 

*目前运行的结果是有并不规律的声音产生，这就是那个时刻变形的声波*

---
Now let's write another (slightly longer) program: (these files can be found in the examples/ directory, so you don't have to type them in) 

    ////////////
    //example2//
    ////////////
    // impulse(脉冲信号) to filter to dac
    //Biquad filter（双二阶滤波器）
    Impulse i => BiQuad f => dac;
    // set the filter's pole radius( = prad)
    .99 => f.prad;
    // set equal gain zero's
    1 => f.eqzs;//不理解
    // initialize float variable
    0.0 => float v;

    // infinite time-loop
    while( true )
    {
        // set the current sample/impulse
        1.0 => i.next;
        // sweep the filter resonant frequency(共振频率)
        Std.fabs(Math.sin(v)) * 4000.0 => f.pfreq;
        // increment(增量) v
        v + .1 => v;
        // advance time
        100::ms => now;
    }

Name this moe.ck, and run it:

    chuck moe.ck

Now, make two copies of moe.ck - larry.ck and curly.ck. Make the following modifications(修改). 

    1) change larry.ck to advance time by 99::ms (instead of 100::ms). 
    2) change curly.ck to advance time by 101::ms (instead of 100::ms). 
    3) optionally, change the 4000.0 to something else (like 400.0 for curly).

Run all three in parallel:

    chuck moe.ck larry.ck curly.ck

What you hear (if all goes well) should be 'phasing' between moe, larry, and curly, with curly emitting(发出) the lower-frequency pulses(脉冲).

ChucK supports sample-synchronous concurrency(并发性), via the ChucK timing mechanism(机制). Given any number of source files that uses the timing mechanism above, the ChucK VM can use the timing information to automatically(自动地) synchronize(合拍) all of them... Note that each process do not need to know about each other - it only has to deal with time locally. The VM will make sure things happen correctly and globally. 

A large collection of pre-made examples have been arranged and provided with this distribution in the /doc/examples directory, and are mirrored [here](http://chuck.cs.princeton.edu/doc/examples/)
