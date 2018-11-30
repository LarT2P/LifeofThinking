---
title: chuck7
date: 2018-02-15 21:40:10
categories: 
    - 编程与生活
tags: 
    - ChucK
---

manipulating time

* time and duration
* operations (arithmetic)
* the keyword 'now'
* advancing time
* properties

#Manipulating Time

ChucK is a **strongly-timed** language, meaning that time is fundamentally(根本地) embedded(嵌入) in the language. ChucK allows the programmer to explicitly(明确的) reason about time from the code. This gives extremely flexible(灵活的) and precise(精确的) control over time and (therefore) sound synthesis(综合).

In ChucK:

* time and duration are native types in the language
* keyword `now` holds the current logical time
* time is advanced (and can only be advanced) by explicitly(显式地) manipulating(操纵) `now`
* you have flexible(灵活的) and precise(精确的) control 

#time and duration

`Time` and `duration` are native types in ChucK. 

    `time` represents an absolute(绝对的) point in time (from the beginning of ChucK time). 时间点
    `dur` represents a duration (with the same logical units as time). 时间段

```c
// a duration of one second
1::second => dur foo;

// a point in time (duration of foo from now)
now + foo => time later;
```

Later in this section, we outline(概述) the various arithmetic operations(各种数学运算) to perform on time and duration.

Durations can be used to construct new durations, which then be used to inductively(归纳地) construct yet other durations. For example:

```c
// .5 second is a quarter
.5::second => dur quarter;

// 4 quarters is whole
4::quarter => dur whole;
```

By default, ChucK provides these preset(预先装置的) duration values:

* samp : duration(持续) of 1 sample(样本 in ChucK time
* ms : duration of 1 millisecond(毫秒)
* second : duration of 1 second
* minute : 1 minute
* hour : 1 hour
* day : 1 day
* week : 1 week 

Use these to represent any duration.

```c
// the duration of half a sample
.5::samp => dur foo;

// 20 weeks
20::week => dur waithere;

// use in combination
2::minute + 30::second => dur bar;

// same value as above
2.5::minute => dur bar;
```

#operations on time and duration (arithmetic)

In ChucK, there are well-defined(定义明确的) arithmetic operations on values of type time and dur.

---
example 1 (time offset(偏移)):

    // time + dur yields time
    now + 10::second => time later;

example 2 (time subtraction):

    // time - time yields dur
    later - now => dur D;

example 3 (addition):

    // dur + dur yields dur
    10::second + 100::samp => dur foo;

example 4 (subtraction):

    // dur - dur yields dur
    10::second - 100::samp => dur bar;

example 5 (division):

    // dur / dur yields number
    10::second / 20::ms => float n;

example 6 (time mod):

     // time mod dur yields dur
     now % 1::second => dur remainder;

example 7 (synchronize to period):

    // synchronize to period of .5 second
    .5::second => dur T;
    T - (now % T) => now;

example 8 (comparison(比较) on time):

    // compare time and time
    if( t1 < t2 )
        // do something...

example 9 (comparison on duration:

    // compare dur and dur
    if( 900::ms < 1::second )
        <<< "yay!" >>>;
---

#the keyword 'now'

The keyword `now` is the key to reasoning about and controlling time in ChucK.思考控制时间的关键

Some properties(属性) of `now` include:

* `now` is a special variable(变量) of type time.
* `now` holds the current ChucK time (when read).
* modifying `now` has the side effects(副作用) of:
    - advancing time (see below);
    - suspending(暂停) the current process (called *shred*) until the desired time is reached - allowing other shreds and audio synthesis to compute; 
* the value of `now` only changes when it is explicitly(明确地) modified(改动). 

(also see next section on advancing time).

Example:

```c
// compute value that represents "5 seconds from now"
now + 5::second => time later;

// while we are not at later yet...
while( now < later )
{
    // print out value of now
    <<< now >>>;

    // advance time by 1 second
    1::second => now;
}
```

#advancing time(推进时间)

Advancing time allows other shreds (processes进程) to run and *allows audio to be computed* in a controlled manner. There are three ways of advancing time in ChucK:

* chucking (=>) a duration to `now`: this will advance time by that duration.
* chucking (=>) a time to `now`: this will advance time to that point. (note that the desired time must be later than the current time, or at least be equal to it.)
* chucking (=>) an `Event` to `now`: time will advance until the event is triggered(引发). (also see `event`) 

**advancing time by duration**

```c
// advance time by 1 second
1::second => now;

// advance time by 100 millisecond
100::ms => now;

// advance time by 1 samp (every sample)
1::samp => now;

// advance time by less than 1 samp
.024::samp => now;
```

**advancing time by absolute(绝对的) time**

```c
// figure out when
now + 4::hour => time later;

// advance time to later
later => now;
```

A time chucked to now will have ChucK wait until the appointed(约定的) time. *ChucK never misses an appointment (unless it crashes)!* Again, the time chucked to now must be greater than or equal to now, otherwise an exception(异常) is thrown.

#advancing time by event

```c
// wait on event
e => now;
```

The advancement(推进) of time can occur(发生) at any point in the code.

```c
// our patch: sine oscillator -> dac
SinOsc s => dac;

// infinite time loop
while( true )
{
    // randomly choose frequency from 30 to 1000
    Std.rand2f( 30, 1000 ) => s.freq;

    // advance time by 100 millisecond
    100::ms => now;
}
```

Furthermore(此外), there are no restrictions(限制) (other than underlying floating point precision除了基本的浮点精度) on how much time is advanced. So it is possible to advance time by a microsecond(微秒), a samp, 2 hours, or 10 years. The system will behave accordingly(相应地) and deterministically(确切地).

This mechanism(机制) *allows time to be controlled at any desired rate*, according to any programmable(可编程的) pattern. With respect to(关于) sound synthesis(合成), it is possible to control any unit generator at literally(字面地) any rate, even sub-sample rate.

The power of the timing mechanism is extended(延伸) by the ability to write parallel(并行) code, which is discussed in concurrency(并发性) and shreds.

#properties

(see above)
