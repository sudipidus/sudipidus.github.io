---
date: "2018-08-02"
title: Learnings from Redis Meetup at Razorpay
layout: post
tags: [redis, caching, database, software-architecture, design-patterns]
---

Personally I haven't extensively used redis. My limited understanding of redis would be summarized as:

- redis is in-memory database, stored in RAM (making use of underutilized RAM space in servers)
- being stored in primary RAM it's very fast
- being stored in volatile storage (RAM) it vanished as system goes off, however there are ways to persist it in the file system
- redis uses key value storage and supports quite a few datatypes including list, hashmaps, geocodes, etc
- how companies like twitter have made an extensive use of redis in caching for faster retrieval (low latency when the throughput demand is high)
- it can be used as pub-sub model (message broker)


**Trivia: Redis is an acronym for "Remote Dictionary Server"**


I attended the monthly redis meetup organized at Razorpay Bangalore on 28th July, 2018 and it introduced me to the finer details and use cases of redis along with some examples of how companies like flipkart are making an extensive use of it. Flipkart uses [a tech stack of redis, kafka, Hbase, Solr](https://tech.flipkart.com/sherlock-near-real-time-search-indexing-95519783859d) in order to attain near real-time search indexing. 


## Installation
(Official redis documentation advises against using system package manager. Instead download the code and build ('make') it)
```bash
wget http://download.redis.io/redis-stable.tar.gz #download redis tarball
tar xvzf redis-stable.tar.gz #extract the tarball
cd redis-stable
make test #optional
make Install
```

## Starting and usage
```bash
redis-server #starts the server (redis instance) at default port 6379 with default config (/etc/redis.conf)
```

Explore more:
[Redis Tutorial](https://redis.io/documentation)


One of the biggest advantages redis brings to traditional memchaed is support of other data types than just Strings. It supports a wide range of data types like lists (linked), sets, hashmaps, etc. All the operations performed are atomic. This means there can never be a race condition amongst the client dealing with redis. One popular use case of redis is in maintaining leaderboard/scoreboard as the operations are fast and the latency is low even when throughput grows.


## Some tools

[rdb tools](https://github.com/sripathikrishnan/redis-rdb-tools) is a gui client for redis. It runs as a GUI (web based) server and provides a sneak peek into your redis clusters with options to view and manage memory resources, a CLI environment with autocompletion (quick documentation lookup), profiler among others.


![rdbtools](https://sudipbhandari126.github.io/resources/rdbtools.png "rdbtools")   


## Bonus
As part of the meetup we were divided in a group and were assigned a problem statement each in order to come up with a solution. (most problem sets required usage of message broker (kafka,redis), sql db, nosql db). Here are the snapshots of those problem sets for interested reader :)

Link:

[Redis Design Workshop Problem Sets](https://rdbtools.com/redis-design-workshop/)

![redis_ps1](https://sudipbhandari126.github.io/resources/redis_ps1.jpeg "redis_ps1")  



![redis_ps2](https://sudipbhandari126.github.io/resources/redis_ps2.jpeg "redis_ps2")



![redis_ps3](https://sudipbhandari126.github.io/resources/redis_ps3.jpeg "redis_ps3")





