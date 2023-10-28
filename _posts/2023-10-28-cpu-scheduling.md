---
layout: post
title:  "CPU Scheduling in OS"
summary: "CPU Scheduling in OS"
author: thanhtinhpas1
category: operating system
keywords: Operating, Scheduling
thumbnail: https://media.geeksforgeeks.org/wp-content/uploads/20220525174157/UntitledDiagram12.jpg
permalink: /blog/cpu-scheduling-in-ops
usemathjax: true
---

# CPU Scheduling in Operating Systems

# Introduction

Scheduling of process/work is done to finish the work on time. CPU Scheduling is a process that allows one process to use the CPU while another process is delayed (in standby) due to unavailability of any resources such as I/O etc, thus making full use of the CPU. The purpose of CPU Scheduling is to make the system more efficient, faster and fairer

Whenever the CPU becomes idle, the operating system must select one of the processes in the line ready for launch. The select process is done by a temporary (CPU) scheduler. The Scheduler selects between memory processes ready to launch and assigns the CPU to one of them.

### What is a process?

In computing, a process is the instance of a Program which will be executed by one or many threads. It contains program code and its activity. 

*How is process memory used for efficient operation?*

A typical memory representation of a C program consists of the following sections:

1. Text segment (i.e instructions)
2. Initialized data segment: is a portion of the virtual address space of a program, which contains the global variables and static variables that are initialized by the programmer.
3. Uninitialized data segment (bss)
4. Heap
5. Stack

### What is process scheduling?

Process scheduling is the process manager handling the removal of an active process from the CPU and selecting another process based on a specific strategy.

There are three type of process scheduling:

- Long term or Job scheduler
- Short term or Job scheduler
- Medium term job scheduler

*Why do we need to scheduler process?*

Keep the CPU always busy, this allows us to get less response time for programs.

⇒ Considering that there are hundred programs that need to work, the OS must launch the program, stop it, switch to another program, etc. The way OS keeps context-switching programs in and out, it’s place the Scheduler Process will come.

*Objective*:

- Utilization of CPU at maximum level: keep CPU as busy as possible.
- Allocation of CPU should be fair
- Throughput should be maximum
- Minimum turnaround time (Turn around time = Completion Time - Arrival Time)
Note: Waiting time = Turn around time - Burst time (time require CPU for execution)
- Minimum response time.

What are different types of CPU Scheduling Algorithms?

1. Preemptive scheduling: is used when a process switches from running  ⇒  ready state
2. Non-preemptive scheduling: is used when a process switches from running ⇒ waiting state

![Untitled](/assets/img/cpu-scheduling/Untitled.png)

### First come first serve

FCFS considered to be the simplest of all operating system scheduling algorithms.

*Characteristics of FCFS*:

- Supports both preemptive and non-preemptive scheduling algorithm
- Always execute on a First-come, First-serve concept
- Easy to implement
- Not much efficient, wait time is quite hight

### Shortest job first

Is a scheduling that selects the waiting process with the smallest execution time to execute next.

![Untitled](/assets/img/cpu-scheduling/Untitled%201.png)

### Longest job first

Opposite of shorted job first, as the name suggests this algorithm is based upon the fact that the process with the largest burst time is process first. Longest job first is non-preemptive in nature.

*Characteristics of LJF*:

- Among all the processes waiting in a waiting queue, CPU is always assigned to the process having largest burst time.
- If two processes have the same burst time then the tie is broken using FCFS i.e the process that arrived first is process first
- LJF CPU Scheduling can be of both preemptive and non-preemptive types

*Advantages*:

- No other task can schedule until longest job or process executes completely
- All the job or processes finish at the same time approximately

*Disadvantage*

- High average waiting time and average turn around-time for a given set of processes

### Priority Scheduling

Each process has a priority number, in this algorithm that the most important process must be done first. In the case of any conflict, that is, where there are more than one processor with equal value, then the most important CPU planing algorithm works on the basic of the FCFS algorithm.

*Characteristics*:

- Schedule tasks based on priority
- When higher priority task arrives while a task with less priority is executed, the higher priority work takes the place of the less priority one
- The latter is suspended until the execution is complete
- Lower is the number assigned, higher is the priority level of a process

*Advantage*

- Less complex
- Average waiting time is less than FCFS

*Disadvantage*

- Facing to Starvation Problem when a process waiting indefinitely when having very low priority. The solution is increase priority after a certain time as 15 minutes until the priority is high enough which suitable for execution.

### Shortest remaining time first:

In SRTF the process with the smallest amount of time remaining until completion is selected to execute.

*Characteristics*:

- SRTF make the processing of the jobs faster than SJF algorithm, given it’s overhead charges are not counted.

*Advantage*

- Short processes are handled very fast
- Very little overhead since it only makes a decision when a process completes or a new process is added.

*Disadvantages*

- Potential for starvation like Shorted job first
- Long processes maybe held off indefinitely

### Longest remaining time first

Longest job will be selected to execute first.

*Characteristics*

- CPU is always assigned to the process having the largest burst time
- If two processes have the same burst time then the tie is broken using FCFS
- LJF CPU scheduling can be of both preemptive and non-preemptive types

*Advantage*

- No other process can execute until longest task executes completely
- All the job or processes finish at the same time approximately

*Disadvantage*

- High waiting time and average turn-around time
- This may lead to a convoy effect

### Highest response ratio next

Need to calculate the response ratio of process then select the highest.

`Response Ratio = (W + S) / S`

W: waiting time

S: Burst time

*Advantage*

- Give better performance than the SJF
- Reduction of waiting time

*Disadvantage*

- Not possible to know the burst time for all job
- Might occur an overload on the CPU

### Multiple queue scheduling

Processes in the ready queue can be divided into different classes where each class has its own scheduling needs.

![Untitled](/assets/img/cpu-scheduling/Untitled%202.png)

*Advantage*

- The main merit of the multilevel queue is that it has a low scheduling overhead

*Disadvantage*

- Starvation problem
- It is inflexible in nature

### Multilevel feedback queue

Like multiple queue but the process can move between the queues.

*Characteristics*

- Permanently assigned to a queue on entry the system
- Flexible

*Advandtage*:

- More flexible
- Allows different processes to move between different queues

*Disadvantage*

- Produces CPU overhead
- More complex

## Comparition table

| Algorithm | Allocation is | Complexity | Average waiting time (AWT) | Preemption | Starvation | Performance |
| --- | --- | --- | --- | --- | --- | --- |
| FCFS | According to the arrival time of the processes, the CPU is allocated. | Simple and easy to implement | Large. | No | No | Slow performance |
| SJF | Based on the lowest CPU burst time  (BT). | More complex than FCFS | Smaller than FCFS | No | Yes | Minimum Average Waiting Time |
| LJFS | Based on the highest CPU burst time (BT) | More complex than FCFS | Depending on some measures e.g., arrival time, process size, etc. | No | Yes | Big turn-around time |
| LRTF | Same as LJFS the allocation of the CPU is based on the highest CPU  burst time (BT). But it is preemptive | More complex than FCFS | Depending on some measures e.g., arrival time, process size, etc. | Yes | Yes | The preference is given to the longer jobs |
| SRTF | Same as SJF the allocation of the CPU is based on the lowest CPU burst time (BT). But it is preemptive. | More complex than FCFS | Depending on some measures e.g., arrival time, process size, etc | Yes | Yes | The preference is given to the short jobs |
| RR | According to the order of the process arrives with fixed time quantum (TQ) | The complexity depends on Time Quantum size | Large as compared to SJF and Priority scheduling. | Yes | No | Each process has given a fairly fixed time |
| Priority Pre-emptive | According to the priority. The bigger priority task executes first | This type is less complex | Smaller than FCFS | Yes | Yes | Well performance but contain a starvation problem |
| Priority non-preemptive | According to the priority with monitoring the new incoming higher priority jobs | This type is less complex than Priority preemptive | Preemptive Smaller than FCFS | No | Yes | Most beneficial with batch systems |
| MLQ | According to the process that resides in the bigger queue priority | More complex than the priority scheduling algorithms | Smaller than FCFS | No | Yes | Good performance but contain a starvation problem |
| MFLQ | According to the process of a bigger priority queue. | It is the most Complex but its complexity rate depends on the TQ size | Smaller than all scheduling types in many cases | No | No | Good performance |