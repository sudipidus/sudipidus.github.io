---
layout: post
title: Experimenting With Local Kafka cluster
tags: [kafak,apache,zookeeper,cap, distributed-systems]
---

Kafka is the platform for stream processing. The real power of Kafka comes in the scenario of distributed computing where Kafka logs can be considered as a single source of ordered-truths. Those logs can be consumed by all the relevant consumers in their own pace and own time. (within the retention period, of course). Here is a great LinkedIn blog on log, which is the core of Kafka platform.

In summary, Kafka has a number of clustered broker nodes (which actually store message inside various topics which can be partitioned (default is 3)). These topics can be further replicated across the brokers. (replication factor is set per topic).

In this blog, however, I am going to set up a simple 3broker cluster in my local machine and try to illustrate some interesting properties and consequences.

1. Starting zookeeper
```bash
sh zookeeper-server-start.sh ../config/zookeeper.properties #from kafka/bin location (Starts up in localhost:2181 by default)
```
2. Configuring multiple Kafka brokers

Kafka server properties are stored inside config/server.properties file. In order to create 3 broker cluster, I am going to modify some properties by creating two more copies of server.properties namely server-1.properties and server-2.properties. The properties which should be changed in this context would be:

```bash
# A comma separated list of directories under which to store log files
log.dirs=/tmp/kafka-logs-1 (In production scenario this be better stored inside different machines and different disk mount points to avoid a single point of failure)

# The address the socket server listens on. It will get the value returned from 
# java.net.InetAddress.getCanonicalHostName() if not configured.
# FORMAT:
# listeners = listener_name://host_name:port
# EXAMPLE:
# listeners = PLAINTEXT://your.host.name:9092
listeners=PLAINTEXT://:9093

Kafka broker listens on port 9092 by default. Since I am setting up all 3 in a single machine (localhost) I am changing this to 9093.

# The id of the broker. This must be set to a unique integer for each broker.
broker.id=1. The first broker has a default id of 0.
```

Now, let's start all 3 brokers:

```bash
sh kafka-server-start.sh ../config/server.properties #broker 0
sh kafka-server-start.sh ../config/server-1.properties #broker 1
sh kafka-server-start.sh ../config/server-2.properties #broker 2 (properties changed as shown above)
```

3. Creating a sample topic
```bash
sh kafka-topics.sh — create — zookeeper localhost:2181 — replication-factor 3 — partitions 1 — topic local_topic
```

We have set replication factor as 3 so that the logs are replicated across all 3 brokers.

4. Topic Stat
```bash
sh kafka-topics.sh — describe — zookeeper localhost:2181 — topic my-replicated-topic
Topic:my-replicated-topic PartitionCount:1 ReplicationFactor:3 Configs:
 Topic: my-replicated-topic Partition: 0 Leader: 1 Replicas: 1,0,2 Isr: 0,2,1
```

As the stat shows, we can see that the topic has replicas across brokers: 1,0 and 2. (These are the broker_ids we set up earlier). Also, broker 1 has been set as a leader (like the primary node).

ISR is the set of in sync replicas. Replicas which are in sync with the leader. The leader will always be a member of the LSR set.

What happens when one of the nodes goes down? Let’s try and see.

Console consumers and producers:
```bash
sh kafka-console-producer.sh — broker-list localhost:9092,localhost:9093,localhost:9094 — topic local_topic
sh kafka-console-consumer.sh — bootstrap-server localhost:9094,localhost:9092,localhost:9093 — topic local_topic — from-beginning
```

I can see all the messages published via producer in the consumer. Let me stop broker 1.

Now, this is what I can see in the console consumer:
```bash
[2019–03–10 14:54:12,357] WARN [Consumer clientId=consumer-1, groupId=console-consumer-53777] Connection to node 1 could not be established. Broker may not be available. (org.apache.kafka.clients.NetworkClient)
hi
```

As we can see it’s not able to connect to broker 1 but still it's able to relay the logs from rest 2 brokers. (Also remember that broker 1 was our leader/primary). Let’s stat the topic again and see:
```bash
sh kafka-topics.sh — describe — zookeeper localhost:2181 — topic my-replicated-topic 
Topic:my-replicated-topic PartitionCount:1 ReplicationFactor:3 Configs:
 Topic: my-replicated-topic Partition: 0 Leader: 0 Replicas: 1,0,2 Isr: 0,2
```
The leader has been changed to broker 0. Also, broker 1 has been removed from in sync replica set.

Finally some note on Kafka regarding CAP theorem as it’s a distributed system.
All distributed systems must make trade-offs between guaranteeing consistency, availability, and partition tolerance (CAP Theorem). Our goal was to support replication in a Kafka cluster within a single datacenter, where network partitioning is rare, so our design focuses on maintaining highly available and strongly consistent replicas. Strong consistency means that all replicas are byte-to-byte identical, which simplifies the job of an application developer. (Source: LinkedIn blog)