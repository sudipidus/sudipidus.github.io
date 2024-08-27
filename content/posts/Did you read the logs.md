---
layout: post
title: Did you read the logs?
tags: [logs,computer-science,programming]
date:  "2024-08-16"
---

### Logs

Logs (in a very degenerative sense) are a series of events/statements that act like the breadcrumb for the things that happened. Degenerative as I am referring mostly as application or software logs. (More pure meaning would be let's say database logs which are used for replication process or a series of events for active state machine replication). I want to talk about the logs (application logs, system logs).

For interested reader, I highly recommend [I heart logs](https://www.oreilly.com/library/view/i-heart-logs/9781491909379/) which talks about the power of logs in a distributed systems - how a series of immutable logs help us chronologically capture a series of events and computing/coming to consensus/committing transaction etc on top of it. (core problem in distributed computing)



- Have you ever looked at browser console when some action fails on the browser but you are not sure what failed? You are essentially looking at logs (response really but a form of logging nonetheless)

- Has your computer suddenly crashed and have you gone through syslogs after it recovered to see what really failed? (I often do this even when there is no issue because it gives a very clear understanding of what your system is really doing - series of events)

- Have you ever faced an annoying issue with the library that you are using and have gone ahead and set the maximum verbosity of log level output for that library (given your langauge/framework supports it) in order to know what's happening internally? (I once had a springboot app that would just stop immediately without any further info, what I did next was run the app with trace level log output, narrowed down the library, suppressed the noise(tracing only that library) and fixed the esoteric issue)

- You'd have surely worked with kafka. But have you ever run kafka - zookeeper setup in verbose log level and marvelled at how all the distributed transaction stuff is happening (heartbeats, quorums, ISR updates, etc)


Of course, logs can be misleading too:

- when code and the log statements are not in sync (manual programming error)
- when essential informations like errors are swallowed due to incorrect error handling 
```
catch (Error e){
log("operation failed"); // e is swallowed
}
```
- incorrect translation of essential information like errors (sometimes you can't afford to spit out the exact details or even the error message: popular example being webapps - (you wouldn't want to spit a response/log like publishing to kafka failed on the browser console))

Logs are our friends really.

I recall reading/watching an interview where [Shubash Choudhary](https://www.linkedin.com/in/subhashch) - Dukan's CTO (as of 2024 August) really got an appreciation and knack for writing software after looking at logs. (as part of troubleshooting job)
