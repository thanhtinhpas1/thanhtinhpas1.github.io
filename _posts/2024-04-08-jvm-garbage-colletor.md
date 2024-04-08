---
layout: post
title:  "JVM Garbage collectors"
summary: "JVM Garbage collectors"
author: thanhtinhpas1
category: java, jvm
keywords: java, jvm
thumbnail: https://www.oracle.com/webfolder/technetwork/tutorials/obe/java/gc01/images/gcslides/Slide1.png
permalink: /blog/jvm-garbage-collector
usemathjax: true
---


# JVM Garbage Collectors

# Overview

In this quick tutorial, we’ll demonstrate the basic of different JVM Garbage collector (GC) implementations. Then we’ll learn how to enable a particular type of Garbage Collector in our applications.

![Untitled](/assets/img/jvm-garbage-collector/Untitled.png)

# Brief introduction of GC

Given the name, it seems like Garbage Collection would deal with finding and deleting the garbage from the memory. However, in reality, Garbage collection tracks each and every object available in the JVM heap space, and removes the unused one.

Basically, GC works in two simple steps, known as Mark and Sweep:

- **Mark** - this is where the garbage collector identifies which pieces of memory are in use and which aren’t
- **Sweep** - this step removes objects identified during the “***mark***” step.

**Advantages:**

- No manual memory allocation/deallocation handling because unused memory space is automatically handled by GC.
- No overhead of handling ***[Dangling pointer](https://en.wikipedia.org/wiki/Dangling_pointer)***
- Automatic ***Memory Leak*** management (GC on its own can’t guarantee the full proof solution to memory leaking, it takes care of a good portion of it).

**Disadvantages:**

- Since JVM has to keep track of object reference creation/deletion, this activity requires more CPU power than the original application. It may effect the performance of requests which require large memory.
- Programmers have no control over the scheduling of CPU time dedicated to freeing objects that are no longer needed.
- Using some GC implementations might result in the application stopping unpredictable.
- Automatized memory management won’t be as efficient as the proper manual memory allocation/deallocation.

 

# GC Implementations

JVM has four types of GC implementations:

- Serial Garbage Collector
- Parallel Garbage Collector
- G1 Garbage Collector
- Z Garbage Collection

## 1. Serial Garbage Collector

This is simplest garbage implementation, as it basically works with a single thread. As a result, ***this GC implementation freezes all application threads when it runs.*** Therefore, it’s not a good idea to use it in multi-threaded applications, like server environments.

However, there was an excellent talk given by *Twitter* engineers at QCon 2012 about the performance of Serial Garbage Collector, which is a good way to understand this collector better.

The Serial GC is the garbage collector of choice for most applications that don’t have small pause time requirements and run on client-style machines. To enable *Serial Garbage Collector,* we can use the following argument:

```java
java -XX:+UseSerialGC -jar Application.java
```

### 2. Parallel Garbage Collector

It’s the default GC of JVM from 5 until Java 8 and it sometimes called Throughput collectors. Unlike Serial GC, it’s using multi-threaded garbage collector for managing heap space, but it also freezes other threads while performing GC.

If you using this GC, you can specify maximum threads, throughput, pause time, and footprint (heap size).

The number of GC threads can be controlled with the command-line option `XX:ParallelGCThreads=<N>` 

The maximum pause time goal (a hint for GC that pause time of <N> milliseconds or less than), specifying with the command line: `XX:MaxGCPauseMillis=<N>` 

The time spent doing GC versus the time outside of GC was called throughput, and specifying by command line `XX:GCTimeRatio=<N>` 

To enable Parallel GC we can using command line: `java -XX:UseParallelGC -jar Application.java` 

### 3. G1 Garbage Collector

G1 (Garbage first) Garbage Collector is designed for application which running on multiple processor machines with large memory. It’s available from the JDK7 Update 4 and in later releases.

Unlike other collectors, the G1 collector partitions the heap into a set of equal-sized heap regions, each a contiguous rang of virtual memory. When performing GC, G1 shows a concurrent global marking phase, (i.e phase 1, known as marking) to the determine the liveness of objects through the heap.

After the mark phase 1 complete, G1 knows which regions are mostly empty. It collects in these area first, which usually yields a significant amount of free space (i.e phase 2, known as Sweeping). That’s this method of garbage collections is called Garbage-first.

To enable G1 GC, use the command line: `java -XX:UseG1GC -jar Application.java`

### 4. Z Garbage Collector

ZGC is scalable, low-latency GC that debuted in Java 11 as an experimental option for Linux. JDK14 introduce ZGC under the windows and macos operation systems. ZGC has obtained the production status from Java 15.

ZGC performs all expensive work concurrently, without stopping the execution of application threads for more than 10ms, which makes it suitable for application that require low latency. It uses load barriers with colored pointers to perform, and they’re used to keep track of heap usage.

Reference coloring (colored pointers) is the core concept of ZGC when using some bits (metadata bits) of reference to mark the state of the object. It also handles heap ranging from 8MB to 16Tb in size.

Similar to Z1, Z GC partitions the heap. To Enable Z GC, we can use the following argument in JDK versions lower than 15.

`java -XX:+UnlockExperimentalVMOptions -XX:+UseZGC Application.java`

From version 15 on, we don’t need to use flag UnlockExperimentalVMOptions

`java -XX:+UseGC Application.java`

## More about

### Tuning the Garbage Collector

Garbage Collector (GC) reclaims the heap space previously allocated to objects no longer needed. The process of locating and removing the dead objects can stall any application and consume as much as 25 percent throughput.

![Untitled](/assets/img/jvm-garbage-collector/Untitled%201.png)

The heap space is divided into the old generation and new generation. The new generation includes the new object space (eden) and two survivor spaces. The JVM allocates new objects in the eden space, and moves long-live objects from the new generation to old generation.

The young generation uses a fast copying garbage collector which employs two-semi spaces (survivor spaces) in the eden, copying surviving objects from one survivor space to the second. Objects that survive multiple young space collections are **tenured,** meaning they are copied to the tenured generation. The tenured collection is large and fill up less quickly. So, it is garbage collected less frequently; and each collections take longer than a young space only collection;

The frequent young space collections are quick (a few milliseconds), while the full generation collections take a longer (tens of millisecond to few seconds, depending upon the heap size).

Other GC algorithms, such as the Concurrent Mark Sweep (CMS) algorithm, are incremental. They divide the full GC into several incremental pieces. This provides a high probability of small pauses. This process comes with an overhead and is not required for enterprise web applications.

### Choosing the Garbage Collection Algorithm

Pauses during a full GC of more than four seconds can cause intermittent failures in persisting session data into HADB.

While GC is going on, the Application Server isn’t running. If the pause is long enough, the HADB times out the existing connections. Then, when the application server resumes its activities, the HADB generates errors when the application server attempts to use those connections to persist session data. It generates errors like, “Failed to store session data,” “Transaction Aborted,” or “Failed to connect to HADB server.”

To prevent that problem, use the CMS collector as the GC algorithm. This collector can cause a drop in throughput for heavily utilized systems, because it is running more or less constantly. But it prevents the long pauses that can occur when the garbage collector runs infrequently.

### To use the CMS collector

1. Make sure that the system is not using 100 percent of its CPU.
2. Configure HADB timeouts, as described in the Administration Guide.
3. Configure the CMS collector in the server instance.
    
    To do this, add the following JVM options:
    
    - XX:+UseConcMarkSweepGC
    - XX:SoftRefLRUPolicyMSPerMB=1

![https://docs.oracle.com/docs/dcommon/dsc/images/sol/procedure.gif](https://docs.oracle.com/docs/dcommon/dsc/images/sol/procedure.gif)

### Young generation garbage collector algorithms

- The (original) copying collector (***enable by default***). When this collector kicks in, all application threads are stopped, and the copying collection proceeds using one thread (which means only one CPU use event on multi-CPU machine).
- The parallel copying collector (Enable using `-XX:UseParNewGC`) is like the original copying collector, this is stop the word collector. How ever this collector is using multiple threads, which is more efficient than previous one but the draw back is reduce the throughput by using more CPU.
- The parallel scavenge collector (Enable using `-XX:UseParallelGC`): like the previous one but it is tuned for gigabyte heaps (over 10GB) on multiple-CPU machines. This collector algorithm is designed to maximize throughput while minimizing pauses.

### Old generation garbage collector algorithms

- The (original) *mark-sweep* collector (***enable by default***). This uses a stop-the-world mark-and-sweep collection algorithm. The collector is single-threaded, the entire JVM is paused and  the collector is use only one CPU until completed.
- The *concurrent collector* (Enable by using `-XX:+UseConcMarkSweepGC`). This collector tries to allow application process to continue as much as possible during the collection. Splitting the collection into six phases described shortly, four are concurrent while two are stop-the-world.
1. The initial-mark-phase (stop-the-world, snapshot the old generation so that we can run most of the rest of the application)
2. The mark phase (concurrent, mark the live objects traversing the object graph from the root)
3. The pre-cleaning phase (concurrent)
4. The re-mark phase (stop-the-world)
5. The sweep phase (concurrent)
6. The reset phase (concurrent)