# CSE316 Operating Systems - Unit II: CPU Scheduling

## Table of Contents

1. [Introduction to CPU Scheduling](#1-introduction-to-cpu-scheduling)
2. [CPU Scheduler and Dispatcher](#2-cpu-scheduler-and-dispatcher)
3. [Scheduling Criteria](#3-scheduling-criteria)
4. [Preemptive and Non-preemptive Scheduling](#4-preemptive-and-non-preemptive-scheduling)
5. [Scheduling Algorithms](#5-scheduling-algorithms)
6. [Process Management in UNIX](#6-process-management-in-unix)
7. [Multiprocessor Scheduling](#7-multiprocessor-scheduling)
8. [Real-time Scheduling](#8-real-time-scheduling)

## 1. Introduction to CPU Scheduling

CPU scheduling is a fundamental operating system function that determines which process runs on the CPU at any given time. It aims to maximize CPU utilization, increase system throughput, and minimize response time for users.

Key concepts:
- **Process**: A program in execution
- **CPU burst**: Period of time during which a process is executing on the CPU
- **I/O burst**: Period of time during which a process is waiting for I/O operations

## 2. CPU Scheduler and Dispatcher

### 2.1 CPU Scheduler

The CPU scheduler selects a process from the ready queue and allocates the CPU to it. It makes scheduling decisions at four key times:

1. When a process switches from running to waiting state (e.g., I/O request)
2. When a process switches from running to ready state (e.g., interrupt occurs)
3. When a process switches from waiting to ready state (e.g., I/O completion)
4. When a process terminates

### 2.2 Dispatcher

The dispatcher is the module that gives control of the CPU to the process selected by the CPU scheduler. Its functions include:

- Context switching
- Switching to user mode
- Jumping to the proper location in the user program to resume execution

**Dispatch latency**: Time taken by the dispatcher to stop one process and start another

## 3. Scheduling Criteria

Criteria used to evaluate CPU scheduling algorithms:

1. **CPU Utilization**: Keep the CPU as busy as possible
2. **Throughput**: Number of processes completed per unit time
3. **Turnaround Time**: Time from process submission to completion
4. **Waiting Time**: Total time spent in the ready queue
5. **Response Time**: Time from submission to first response

## 4. Preemptive and Non-preemptive Scheduling

### 4.1 Non-preemptive Scheduling

- Once the CPU is allocated to a process, it keeps the CPU until it releases it voluntarily or terminates
- No interruption of running processes
- Examples: First-Come, First-Served (FCFS), Shortest Job First (SJF) without preemption

### 4.2 Preemptive Scheduling

- Allows the operating system to reclaim the CPU from a running process
- Useful for real-time systems and time-sharing environments
- May lead to race conditions when multiple processes share data
- Examples: Round Robin, Shortest Remaining Time First (SRTF)

## 5. Scheduling Algorithms

### 5.1 First-Come, First-Served (FCFS)

- Simplest scheduling algorithm
- Non-preemptive
- Processes are executed in the order they arrive

Example:
```
Process   Burst Time
P1        24
P2        3
P3        3

Execution Order: P1 -> P2 -> P3
Average Waiting Time: (0 + 24 + 27) / 3 = 17
```

Pros:
- Simple to implement
- Fair in terms of arrival order

Cons:
- Can lead to "convoy effect" where short processes wait behind long ones

### 5.2 Shortest Job First (SJF)

- Associates each process with its next CPU burst length
- Allocates CPU to the process with the smallest next CPU burst
- Can be preemptive (Shortest Remaining Time First) or non-preemptive

Example (non-preemptive):
```
Process   Arrival Time   Burst Time
P1        0              6
P2        1              8
P3        2              7
P4        3              3

Execution Order: P1 -> P4 -> P3 -> P2
Average Waiting Time: (0 + 16 + 3 + 7) / 4 = 6.5
```

Pros:
- Optimal for minimizing average waiting time

Cons:
- Difficult to know the length of the next CPU burst
- May lead to starvation of longer processes

### 5.3 Round Robin (RR)

- Preemptive scheduling algorithm
- Each process gets a small unit of CPU time (time quantum)
- After time quantum expires, the process is preempted and added to the end of the ready queue

Example (Time Quantum = 4):
```
Process   Burst Time
P1        24
P2        3
P3        3

Execution Order: P1(4) -> P2(3) -> P3(3) -> P1(4) -> P1(4) -> P1(4) -> P1(4) -> P1(4)
Average Waiting Time: (6 + 4 + 7) / 3 = 5.67
```

Pros:
- Fair allocation of CPU
- Good for time-sharing systems

Cons:
- Performance heavily depends on the size of the time quantum

### 5.4 Priority Scheduling

- Associates a priority with each process
- CPU is allocated to the process with the highest priority
- Can be preemptive or non-preemptive

Example:
```
Process   Burst Time   Priority
P1        10           3
P2        1            1
P3        2            4
P4        1            5
P5        5            2

Execution Order: P2 -> P5 -> P1 -> P3 -> P4
```

Pros:
- Allows important processes to run first

Cons:
- May lead to starvation of low-priority processes
- Solution: Aging (gradually increase the priority of processes that wait for a long time)

### 5.5 Multilevel Queue Scheduling

- Partitions the ready queue into separate queues
- Processes are permanently assigned to one queue
- Each queue has its own scheduling algorithm

Example:
1. System processes (highest priority)
2. Interactive processes
3. Batch processes (lowest priority)

Pros:
- Allows different scheduling strategies for different types of processes

Cons:
- Inflexible (processes can't move between queues)

### 5.6 Multilevel Feedback Queue Scheduling

- Similar to multilevel queue scheduling, but allows processes to move between queues
- Adapts to changing behavior of processes

Example:
- Multiple queues with different time quantum
- New process enters the highest priority queue
- If a process uses its full time quantum, it moves to a lower priority queue
- If a process gives up the CPU before its time quantum expires, it stays in the same queue

Pros:
- Flexible and adaptive
- Favors short, I/O bound processes

Cons:
- Complex to implement

## 6. Process Management in UNIX

UNIX uses a priority-based, preemptive scheduling algorithm with multilevel feedback queues.

Key features:
- Priorities range from 0 to 127 (lower number means higher priority)
- Real-time processes: priorities 0-49
- User processes: priorities 50-127
- `nice` value can adjust process priority (-20 to +19)

Basic algorithm:
1. Choose the highest priority ready process
2. If multiple processes have the same priority, use round-robin scheduling
3. Periodically adjust priorities based on CPU usage and `nice` value

System calls for process management:
- `fork()`: Create a new process
- `exec()`: Execute a new program
- `wait()`: Wait for a child process to terminate
- `exit()`: Terminate the current process

## 7. Multiprocessor Scheduling

Scheduling in systems with more than one CPU introduces new complexities:

### 7.1 Approaches

1. **Asymmetric Multiprocessing**: One processor (master) handles all scheduling decisions
2. **Symmetric Multiprocessing (SMP)**: Each processor is self-scheduling

### 7.2 Processor Affinity

- Tendency of a scheduler to try to keep a process running on the same processor
- Helps maintain cache contents

Types:
- Soft affinity: OS attempts to keep a process on one processor, but not guaranteed
- Hard affinity: Process specifies a subset of processors it may run on

### 7.3 Load Balancing

- Attempts to keep workload evenly distributed across all processors
- Push migration: Periodic task checks load on each processor and balances
- Pull migration: Idle processor pulls waiting task from busy processor

### 7.4 Multicore Processors

- Multiple processor cores on a single chip
- May share caches, making affinity even more important

## 8. Real-time Scheduling

Real-time systems require that results be produced within specified deadlines.

### 8.1 Types of Real-time Systems

1. **Hard real-time systems**: Must guarantee that critical tasks complete on time
2. **Soft real-time systems**: Critical tasks should be given priority, but no absolute guarantee

### 8.2 Real-time Scheduling Algorithms

1. **Rate Monotonic Scheduling**
   - Static priority algorithm
   - Assigns priorities based on the frequency of tasks (higher frequency = higher priority)

2. **Earliest Deadline First (EDF)**
   - Dynamic priority algorithm
   - Assigns priorities based on deadlines (earlier deadline = higher priority)

3. **Proportional Share Scheduling**
   - Allocates T shares among all processes
   - Each process is allocated a number of shares
   - Process runs for time proportional to its allocated shares

Example (EDF):
```
Task   Period   Execution Time
T1     50       25
T2     80       35

At t=0: Both T1 and T2 arrive. T1 has earlier deadline, so it runs.
At t=25: T1 completes, T2 runs.
At t=50: T1 arrives again, preempts T2 (earlier deadline).
At t=75: T1 completes, T2 resumes.
At t=80: T2 completes.
```

### 8.3 Priority Inversion

Problem: Lower priority process holds a resource needed by a higher priority process

Solution: Priority Inheritance Protocol
- Temporarily boost the priority of the lower priority process holding the resource

This comprehensive guide covers the main topics of CPU Scheduling in CSE316. Each section provides detailed explanations and examples to help understand the concepts clearly. You can use this Markdown file directly in your GitHub repository for easy reference and studying.
