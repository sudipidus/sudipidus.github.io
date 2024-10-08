---
layout: post
title: Notion of time; Distributed Systems
tags: [distributed-systems, time, concurrency, clock, NTP, wall-clock, monotonic-clock]
date:  "2024-08-12"
---
 

#### Time is not just an abstraction
Of course time as we know in a day to day sense is an abstract thing. It's a concept we defined to measure something. (like we defined length to measure how long something is or weight to define how heavy something is). But behind that abstraction is a physical reality. Events are a manifestation of that reality which really give us a sense of time. (we percieve time because of the events).

Here is Leslie Lamport quote:

> I look at mutual exclusion not as a programming problem, not as a mathematical problem, but a physics problem.



#### Time and concurrency

Notion of time can also help us understand what concurrency is. Concurrency is one of the misunderstood term in computer science. (specially in relation to parallelism). If we use the notion of time we can define events in terms of a timeline.

If there are events A and B, is there a way for us to decide which occurs first (in a deterministic way)?. If the answer is no then A and B are concurrent events. A could happen first, or at the same time or after B we never know, it would depend on other non deterministic factors. (like when is the scheduled by the OS)
Event A doesn't know about Event B and the vice verca. Now it can be lead to the myriads of problems that can occur if there is a race condition ....

 
#### How to measure time?

Since we have 2 notions of time, we need different approaches to measure them. One is the conventional/agreed upon value of time (which is an abstraction really to give meaning to the real world mostly based on earth's rotation around the sun). This measure of time is the agreed upon standard which makes sense to everyone. Since it has to be consistent to everyone we have NTP servers and satelites and atomic clocks with high precision to keep it in check (by correcting when it goes off). (even the best ones do have some margin of error).


The other approach to measure time is to measure it within a constrain of a system, call it a logical clock. This notion of time works within the confine of a system. Eg: You can start counting from 1 and go on from there which can work as a reference point. (If we think in terms of distributed system this is really like timestamping the events so we can look at the events see which happened before and which happened later i.e ordering)


#### Wall clock vs Monotonic Clock

*Wall clock* - tells us what the current time is. This is the standard time which is maintained and tracked by the NTP servers (Network time protocols) and is calibrated and kept in sync using satelites and atomic clocks. Periodically our computers connect to those NTP servers and do adjustments when there is some mismatch. (adjustments till a threshold are handled by the system and large discrepancies are left to the users to handle - some of us have probably faced this complaint from our computers when the time set is very much off/deviant from the NTP time). Example of NTP server: `ntp.pool.org`

*Monotonic clock* - tells us relative time (i.e the difference). Point of reference could be anything that the system handles.


Now some examples:

#### Measuring elapsed time

One of the common ways to instrument code is to measure elapsed time. We enclose the function body with methods to fetch current time and subtract. This elapsed time could be logged or sent to any metrics aggregator and could be utilized to get a better insight about what is taking time and what could be optimized in the code flow.

```java
long start = System.currentTimeMillis();
// method body
long end = System.currentTimeMillis();
long elapsed = end - start;
```
However, this approach has a problem. If the system clock is adjusted (either manually or automatically), it could give us wrong value. It could even give negative value. (in case of leap seconds adjustment)



Different languages have ways to fetch both wall-clock and monotonic clock.
The correct form of our earlier java example would be something like:

```java
long start = System.nanoTime();
// method body
long end = System.nanoTime();
long elapsed = end - start;
```




Further reading:
[Leslie Lamport Paper on Time Clocks](https://lamport.azurewebsites.net/pubs/time-clocks.pdf)

[Martin Klepmann lecture series on distributed systems](https://www.youtube.com/watch?v=UEAMfLPZZhE&list=PLeKd45zvjcDFUEv_ohr_HdUFe97RItdiB)

[Leslie Lamport on Writing Time Clocks](https://www.youtube.com/watch?v=nfRouGH0oMg)