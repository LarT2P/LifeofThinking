---
title: chuck13
date: 2018-02-15 21:40:10
categories: 
    - 编程与生活
tags: 
    - ChucK
---

# Events

In addition to the built-in(嵌入的) timing mechanisms(机制) for internal(内部的) control, ChucK has an **event** class to allow exact synchronization(精确同步) across an arbitrary(任意的) number of shreds.
事件类允许在任意的进程间实现精确同步

[View sample code for events](http://chuck.cs.princeton.edu/doc/examples/index.html# event)

# what they are

ChucK events are a native class(原始类) within the ChucK language. We can create an event objects, and then chuck (`=>`) that event to **now**. 
The event places the current shred on the event's waiting list, suspends(暂停) the current shred (letting time advance from that shred's point of view). 
时间放置在当前进程的事件的等待列表里，暂停当前进程(从时间的观测点推进时间)
When the event is triggered(引发), one or more of the shreds on its waiting list is shreduled to run immediately.
时间被引发时，在进程的等待列表里调用运行
This trigger(触发器) may originate(引起) **from another ChucK shred, or from activities taking place outside the Virtual Machine** ( MIDI, OSC, or IPC ).

```c
// declare event
Event e;

// function for shred
fun void eventshred( Event event, string msg )
{
    // infinite loop
    while ( true )
    {
        // wait on event
        event => now;
        // print
        <<<msg>>>;
    }
}

// create shreds
spork ~ eventshred ( e, "fee" );
spork ~ eventshred ( e, "fi" );
spork ~ eventshred ( e, "fo" );
spork ~ eventshred ( e, "fum" );

// infinite time loop
while ( true )
{
    // either signal or broadcast(信号或广播)
    if( maybe )
    { 
        <<<"signaling...">>>;
        e.signal();
    }
    else
    { 
        <<<"broadcasting...">>>;
        e.broadcast();
    }

    // advance time
    0.5::second => now;
}
```

# use

Chucking an event to **now** *suspends the current shred*, letting time advance:

```c
// declare Event
Event e;

// ...

// wait on the event
e => now;

// after the event is trigger
<<< "I just woke up" >>>;
```

as shown above, events can be triggered in two ways, *depending on the desired behavior(行为).*

```c
// signal one shred waiting on the event e
e.signal();
```

`signal()` releases the *first* shred in that event's queue, and shredule it to run at the current time, respecting(考虑) the order in which shreds were added to the queue.

```c
// wake up all shreds waiting on the event e
e.broadcast();
```

`broadcast()` releases *all* shreds queued by that event, in the order they were added, and at the same instant in time(在同一瞬间).

The released shreds are shreduled to run immediately. But of course they will respect(涉及) other shreds also shreduled to run at the same time. Furthermore(此外), the shred that called `signal()` or `broadcast()` will continue to run until it advances time itself, or yield the virtual(虚拟的) machine without advancing time. (see `me.yield()` under [concurrency](http://chuck.cs.princeton.edu/doc/language/spork.html# me))

# MIDI events

ChucK contains built-in(嵌入的) MIDI classes to allow for interaction(相互作用) with MIDI based software or devices(装置).

```c
MidiIn min;
MidiMsg msg;

// open midi receiver, exit on fail
if ( !min.open(0) ) me.exit(); 

while( true )
{
    // wait on midi event
    min => now;

    // receive midimsg(s)
    while( min.recv( msg ) )
    {
        // print content
        <<< msg.data1, msg.data2, msg.data3 >>>;
    }
}

...
```

**MidiIn** is a subclass(子类) of **Event**, and as such can be ChucKed to **now**. MidiIn then takes a MidiMsg object to its **.recv()** method to access the MIDI data.
*As a default, MidiIn events trigger(引发) the **broadcast()** event behavior.*

# OSC events

In addition to MIDI, ChucK has OSC communication classes as well:

```c
...

// create our OSC receiver
OscRecv orec;
// port 6449
6449 => orec.port;
// start listening (launch thread)
orec.listen();

function void rate_control_shred()
{ 
    // create an address in the receiver 
    // and store it in a new variable.
    orec.event("/sndbuf/buf/rate,f") @=> OscEvent rate_event; 

    while ( true )
    { 
        rate_event => now; // wait for events to arrive.

        // grab the next message from the queue. 
        while( rate_event.nextMsg() != 0 )
        { 
            // getFloat fetches the expected float
            // as indicated in the type string ",f"
            buf.play( rate_event.getFloat() );
            0 => buf.pos;
        }
    }       
}

...
```

The **OscRecv** class listens for incoming OSC packets on the specified(特定的) port. Each instance(实例) of **OscRecv** can create OscEvent objects using its `event()` method to listen for packets at any valid OSC Address pattern.(在任何合法的OSC地址模式，使用事件方法来监听数据包)

An OscEvent object can then be ChucKed to **now** 
*to wait for messages to arrive*, 
after which the **nextMsg()** and **get{Float|String|Int}()** methods can be used 
*to fetch message data*.

# creating custom events

Events, like any other class, can be subclassed to add functionality(功能) and transmit(传输) data:

```c
// extended event扩充事件
class TheEvent extends Event
{
    int value;
}

// the event
TheEvent e;

// handler
fun int hi( TheEvent event )
{
    while( true )
    {
        // wait on event
        event => now;
        // get the data
        <<<e.value>>>;
    }
}

// spork
spork ~ hi( e );
spork ~ hi( e );
spork ~ hi( e );
spork ~ hi( e );

// infinite time loop
while( true )
{
    // advance time
    1::second => now;

    // set data
    Math.rand2( 0, 5 ) => e.value;

    // signal one waiting shred
    e.signal();
}
```
