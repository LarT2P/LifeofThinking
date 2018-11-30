---
title: chuck11
date: 2018-02-15 21:40:10
categories: 
    - 编程与生活
tags: 
    - ChucK
---

unit generators(单元发生器)

    declaring
    connecting
    controlling (timing mechanism)
    mono + stereo
    creating (coming soon) 

#Unit Generators

**Unit Generators are function generators that output(输出) signals that can be used as audio or control signals**. However, in ChucK, there is *no fixed control rate*. **Any unit generator may be controlled at any rate.** Using the timing mechanism(机制), you can program your own control rate, and can dynamically(动态地) vary(变化) the control over time. *Using concurrency(并发性), it is possible to have many different parallel(平行的) controls rates, each at any granularity(间隔尺寸).*

Some more quick facts about ChucK unit generators

* All ChucK unit generators are objects (not primitive(原始的) types).是对象，非原始类型
* All ChucK unit generators inherit(继承) from the `UGen` class.继承自`UGen`类
* The operation `foo => bar`, where `foo` and `bar` are UGen's, connects `foo` to `bar`.
* Unit generators are controlled by calling/chucking to member functions over time.通过随时间唤起成员函数而控制单元发生器
* All unit generators have the functions `gain`, `op`, `channels`, `chan`, and `last`. (see below)
* Three default, global unit generators are provided. They are `adc`, `dac`, and `blackhole`. (see below)
* *Unit generators are specially integrated(集合) into the virtual machine* such that audio is computed for every sample(取样) `as time is advanced(随时间被推进)`. Via the timing mechanism(机制), we have the ability to **assert(维护) control over the audio generate process *at any point in time* and *at any desired control rate*. **

>[View a list](http://chuck.cs.princeton.edu/doc/program/ugen.html) of ChucK's built-in(嵌入的) unit generator classes 
>[View sample code](http://chuck.cs.princeton.edu/doc/examples/index.html#ugen) for unit generators 

#declaring

Unit generators (UGens) are objects, and *need to be instantiated before they can be used(在使用前需要被实例化)*. We declare unit generators the same way we declare objects.

    // instantiate a SinOsc, assign reference to variable s
    SinOsc s;

#connecting

The ChucK operator (`=>`) *is specially overloaded for unit generators*: **ChucKing one UGen to another** connects their respective(分别的) output(s) and input(s).

    // instantiate a SinOsc, connect its output to dac's input
    SinOsc s => dac;

It is also possible to linearly(线性地) chain many UGens together in a single statement.在一个单独声明中，线性连接许多单元发生器

    // connect SinOsc to Gain to reverb(混响) to dac
    SinOsc s => Gain g => JCRev r => dac;

Furthermore(此外), it is possible to introduce feedback(反馈) in the network.网络中可以引入反馈

    // connect adc to Gain(增益) to delayline(延迟线) to dac; (feedforward(正反馈))
    adc => Gain g1 => DelayL d => dac;

    // adc to Gain to dac (feedforward)
    adc => Gain g2 => dac;

    // our delayline to Gain back to itself (feedback)
    d => Gain g3 => d;

UGens may be dynamically(动态地) connected in this fashion(以这种方式) into an audio synthesis network(音频合成网络). It is essential to note that the above only connects the unit generators, but does not actually generate(形成) audio - unless time is advanced. (see manipulating time and using events)要注意上面只是链接了单元发生器，并未形成音频，除非时间被推进

    // connect SinOsc to dac
    SinOsc s => dac;
    // set initial frequency (see next section)
    440 => s.freq;

    // advance time; allow audio to compute
    1::second => now;

It is also possible to dynamically disconnect(断开) unit generators, using the UnChucK operator (`=<` or `!=>`):

```c
    // connect SinOsc to dac
    SinOsc s => dac;

    // let time pass for 1 second letting audio be computed for that amount of time
    1::second => now;

    // disconnect s from the dac
    s =< dac;

    // let time pass for another second - should hear silence
    1::second => now;

    // duh, connect them again
    s => dac;

    // let time pass...
    1::second => now;
```

#controlling (over time)

In ChucK, parameters(参数) of unit generators may be *controlled and altered at any point in time and at any control rate.* We only have to move through time and assert(维护) the control at *appropriate*(适当的) points in time, by setting various parameters on the unit generator.
通过在单元发生器设定各种各样的参数
To set the a value for a parameter of a unit generator, a value of the proper type should be ChucKed to the corresponding control fucntion.
为了设定一个单元发生器的参数值，一个合适的类型的值应该被ChucK到相应的控制函数上。

    // connect SinOsc to dac
    SinOsc s => dac;
    // set initial frequency to 440 hz
    440 => s.freq;

    // let time pass for 1 second
    1::second => now;

    // change the frequency to 880 hz
    880 => s.freq;

Since *the control functions* are *member functions of the unit generator*,
控制函数就是单元发生器的成员函数
the above syntax(语法) is equivalent to calling functions(等价于调用函数).

    // connect SinOsc to dac
    SinOsc s => dac;

    // set frequency to 440
    s.freq( 440 );

    // let time pass
    1::second => now;

For a list of unit generators and their control methods, consult(查阅) [UGen reference](http://chuck.cs.princeton.edu/doc/program/ugen.html).

To read the current value of certain parameters(参数) (*not all parameters can be read*), we may call an overloaded function of the same name.
为了读取某个参数的当前值，需要调用一个同名的重载函数

    // connect SinOsc to dac
    SinOsc s => dac;

    // store the current value of the freq
    s.freq() => float the_freq;

You can chain assignments(分配) together when you want to assign(分配) one value to multiple targets. **Note** that the parentheses(括号) are only needed when the read function is on the very left.
只有当读函数位于最左边时，才需要括号

    // SinOsc to dac
    SinOsc foo => dac;
    // triosc to dac
    triosc bar => dac;

    // set frequency of foo and then bar
    500 => foo.freq => bar.freq;

    // set one freq to the other
    foo.freq() => bar.freq;

    // the above is same as:
    bar.freq( foo.freq() );

>Of course, varying(不同的) parameters over time is often more interesting.不同参数随时间推移是很有趣的。

    // SinOsc to dac
    SinOsc s => dac;

    // infinite time loop
    while( true )
    {
        // set the frequency
        ( s.freq() + 200 ) % 5000 => s.freq;

        // advance time
        100::ms => now;
    }

All ugen's have at least the following three control parameters:
以下参数至少有三个

* *gain(float)* (of type `float`): set/get the gain of the UGen's output(输出).设置或获取单元发生器输出的增益
* *last()* (of type `float`): get the last sample computed by the UGen. if UGen has more than one channel, the average of all components(成分) channels are returned. 得到被单元发生器计算的最后的样本，如果单元发生器有不止一个通道，则返回所有通道的平均值。
* *channels()* (of type `int`): get the number of channels in the UGen.获取单元发生器的通道数
* *chan(int)* (of type `UGen`): return reference to nth channel (or *null* if no such channel).返回第几个通道引用，没有的话就返回`null`
* *op(int)* (of type `int`): set/get operation at the UGen. 设定或获取单元发生器的操作 Values:
    * 0 : stop - always output 0
    * 1 : normal operation, add all inputs (default)
    * 2 : normal operation, subtract(减去) *inputs starting from the earliest* connected减去从最早的链接开始的输入(不甚理解？？？)
    * 3 : normal operation, multiply(乘以) all inputs
    * 4 : normal operation, divide *inputs starting from the earlist connected*
    * -1 : passthru - all inputs to the ugen are summed and passed directly to output所有单元生成器的输入被加在一起并且直接传递到输出

#mono + stereo(单声道立体声)

ChucK supports *stereo*(立体声) (default) and *multi-channel audio* (see `command line options` to select interfaces(界面) and number of channels). **The `dac` and the `adc` are now multi-channel UGens**. By default, ChucKing two UGens containing the same number of channels (**e.g. both stereo(立体声) or both mono(单声道放音)**) automatically(自动地) matches the output(输出) channels with the input(输入) channels (**e.g. left to left, right to right for stereo**). 

*Stereo UGens mix their output channels when connecting to mono UGens*. 
*Mono UGens split their output channels when connecting to stereo UGens.* 

Stereo UGens contain the parameters(参数) `.left` and `.right`, which allow access(访问) to the individual(个人的) channels.

    // adding separate reverb(混响) to left and right channels
    adc.left => JCRev rl => dac.left;
    adc.right => JCRev rr => dac.right;

The pan2 stereo object takes a mono signal and split it to a stereo signal, with control over the panning. The pan position may be changed with the `.pan` parameter (-1 (hard left) <= p <= 1 (hard right)范围是固定的)
pan2立体声对象将单声道分离成立体声，通过控制panning(平移？)，平移位置可能被用`.pan`参数改变。

    // white noise to pan to dac
    noise n => pan2 p => dac;

    // infinite time loop
    while( true )
    {
        // modulate(调整) the pan
        Math.sin( now / 1::second * 2 * pi ) => p.pan;
        // advance time
        10::ms => now;
    }

#creating

>( coming soon! )

#built-in unit generators

ChucK has a number of built-in(嵌入的) UGen classes, included most of the `Synthesis ToolKit (STK)`. A list of built-in ChucK unit generators can be found here(url:.../ugen.html).
