# CSE316 Operating Systems - Unit III: Threads and Process Synchronization (Expanded)

## Table of Contents

1. [Threads](#1-threads)
   - [1.1 Overview](#11-overview)
   - [1.2 Multithreading Models](#12-multithreading-models)
2. [Process Synchronization](#2-process-synchronization)
   - [2.1 Critical Section Problem](#21-critical-section-problem)
   - [2.2 Synchronization Hardware](#22-synchronization-hardware)
   - [2.3 Peterson's Solution](#23-petersons-solution)
   - [2.4 Semaphores](#24-semaphores)
   - [2.5 Monitors](#25-monitors)
   - [2.6 Classic Synchronization Problems](#26-classic-synchronization-problems)
     - [2.6.1 Dining Philosophers Problem](#261-dining-philosophers-problem)
     - [2.6.2 Readers-Writers Problem](#262-readers-writers-problem)

## 1. Threads

### 1.1 Overview

A thread is a fundamental unit of CPU utilization, comprised of:
- Thread ID: Unique identifier for the thread
- Program Counter: Address of the next instruction to be executed
- Register Set: Working registers
- Stack: Temporary data storage (e.g., function parameters, return addresses, and local variables)

Threads within the same process share:
- Code section
- Data section
- Other operating system resources (e.g., open files and signals)

Benefits of using threads:

1. **Responsiveness**: In interactive applications, threads allow the program to continue running even if part of it is blocked or performing a lengthy operation. For example, in a web browser, one thread can handle user input while another thread loads images.

2. **Resource Sharing**: Threads share the resources of the process, which is more economical than inter-process communication. For instance, in a word processor, one thread can format text while another performs spell-checking, both accessing the same document data.

3. **Economy**: Creating and managing threads is much more efficient than creating and managing processes. Thread creation can be 10-100 times faster than process creation on most systems.

4. **Scalability**: Threads can take advantage of multiprocessor architectures. On a multi-core system, multiple threads can run in parallel, genuinely improving performance. For example, a video encoding application can use multiple threads to process different frames simultaneously on different CPU cores.

Thread Operations:
- Thread creation
- Thread termination
- Thread joining (waiting for another thread to finish)
- Thread yielding (voluntarily giving up the CPU)

Example of thread creation in C using POSIX threads:

```c
#include <pthread.h>
#include <stdio.h>

void *print_message_function(void *ptr);

int main() {
    pthread_t thread1, thread2;
    char *message1 = "Thread 1";
    char *message2 = "Thread 2";
    int  iret1, iret2;

    iret1 = pthread_create(&thread1, NULL, print_message_function, (void*) message1);
    iret2 = pthread_create(&thread2, NULL, print_message_function, (void*) message2);

    pthread_join(thread1, NULL);
    pthread_join(thread2, NULL);

    printf("Thread 1 returns: %d\n", iret1);
    printf("Thread 2 returns: %d\n", iret2);
    exit(0);
}

void *print_message_function(void *ptr) {
    char *message;
    message = (char *) ptr;
    printf("%s \n", message);
}
```

### 1.2 Multithreading Models

Multithreading models define the relationship between user-level threads and kernel-level threads.

#### 1.2.1 Many-to-One Model

In this model, many user-level threads are mapped to a single kernel thread.

Characteristics:
- Thread management is done by the thread library in user space, which is efficient.
- The entire process will block if a thread makes a blocking system call.
- Multiple threads cannot run in parallel on multiprocessors.

Advantages:
- Thread management is efficient because it's done in user space.
- It can run on any operating system.
- Scheduling can be application-specific.

Disadvantages:
- Lack of concurrency: A blocking system call blocks the entire process.
- Cannot take advantage of multiprocessing.

Example: GNU Portable Threads

#### 1.2.2 One-to-One Model

This model maps each user thread to a kernel thread.

Characteristics:
- Provides more concurrency than the many-to-one model.
- Allows another thread to run when a thread makes a blocking system call.
- Allows multiple threads to run in parallel on multiprocessors.

Advantages:
- Provides good concurrency.
- Allows parallel execution on multiprocessors.

Disadvantages:
- Creating a user thread requires creating a corresponding kernel thread, which can be expensive.
- The number of threads per process may be restricted due to the overhead on the system.

Example implementation (Windows CreateThread API):

```c
HANDLE CreateThread(
  LPSECURITY_ATTRIBUTES   lpThreadAttributes,
  SIZE_T                  dwStackSize,
  LPTHREAD_START_ROUTINE  lpStartAddress,
  LPVOID                  lpParameter,
  DWORD                   dwCreationFlags,
  LPDWORD                 lpThreadId
);
```

#### 1.2.3 Many-to-Many Model

This model multiplexes many user-level threads to a smaller or equal number of kernel threads.

Characteristics:
- Allows the developer to create as many user threads as necessary.
- The corresponding kernel threads can run in parallel on a multiprocessor.
- When a thread makes a blocking system call, the kernel can schedule another thread for execution.

Advantages:
- Combines the best of both worlds: developer can create as many threads as needed, and the corresponding kernel threads can run in parallel.
- Efficient use of system resources.

Disadvantages:
- Complex to implement.
- Balancing between kernel-level and user-level threads can be challenging.

Example: Solaris prior to version 9

## 2. Process Synchronization

### 2.1 Critical Section Problem

The critical section is a part of a program where shared resources are accessed. The critical section problem involves designing a protocol that processes can use to cooperate.

A solution to the critical section problem must satisfy three requirements:

1. **Mutual Exclusion**: If process Pi is executing in its critical section, no other processes can be executing in their critical sections.

2. **Progress**: If no process is executing in its critical section and some processes wish to enter their critical sections, then the selection of the processes that will enter the critical section next cannot be postponed indefinitely.

3. **Bounded Waiting**: A bound must exist on the number of times that other processes are allowed to enter their critical sections after a process has made a request to enter its critical section and before that request is granted.

Example structure of a process with a critical section:

```c
do {
    entry section
        critical section
    exit section
        remainder section
} while (true);
```

### 2.2 Synchronization Hardware

Modern computer systems provide special hardware instructions that allow us to solve the critical section problem efficiently. 

#### 2.2.1 Memory Barriers

Memory barriers, or memory fences, are low-level synchronization mechanisms provided by hardware architectures. They ensure that memory operations are completed in a specific order.

Example (pseudo-code):
```
Store A
Memory Barrier
Load B
```
This ensures that the store to A is completed before the load from B is executed.

#### 2.2.2 Test-and-Set Instruction

The test-and-set instruction is an atomic instruction that tests and modifies the content of a word.

```c
boolean TestAndSet(boolean *target) {
    boolean rv = *target;
    *target = true;
    return rv;
}
```

Usage example:
```c
do {
    while (TestAndSet(&lock))
        ; // do nothing
    
    // critical section
    
    lock = false;
    
    // remainder section
} while (true);
```

#### 2.2.3 Compare-and-Swap Instruction

The compare-and-swap instruction atomically compares the contents of a memory word to a given value and, if they are the same, modifies the contents of that word.

```c
int CompareAndSwap(int *value, int expected, int new_value) {
    int temp = *value;
    if (*value == expected)
        *value = new_value;
    return temp;
}
```

Usage example:
```c
do {
    while (CompareAndSwap(&lock, 0, 1) != 0)
        ; // do nothing
    
    // critical section
    
    lock = 0;
    
    // remainder section
} while (true);
```

### 2.3 Peterson's Solution

Peterson's solution is a classic software-based solution to the critical section problem for two processes. It uses two shared variables:

```c
int turn;           // Indicates whose turn it is to enter the critical section
boolean flag[2];    // Indicates if a process is ready to enter the critical section
```

Complete implementation for process i (where i is 0 or 1):

```c
do {
    flag[i] = true;                 // Indicate interest in entering critical section
    turn = j;                       // Give away the turn
    while (flag[j] && turn == j);   // Wait if the other process is interested and it's its turn
    
    // Critical Section
    
    flag[i] = false;                // Indicate process has left critical section
    
    // Remainder Section
} while (true);
```

Proof of correctness:

1. Mutual Exclusion: If Pi is in its critical section, then flag[i] == true and either flag[j] == false or turn == i. Thus, Pj cannot enter its critical section.

2. Progress: If both processes are trying to enter their critical sections, eventually one will succeed (when turn changes).

3. Bounded Waiting: A process can be prevented from entering its critical section at most once by the other process.

### 2.4 Semaphores

A semaphore S is an integer variable that, apart from initialization, is accessed only through two standard atomic operations: wait() and signal().

Detailed semaphore operations:

```c
typedef struct {
    int value;
    struct process *list;
} semaphore;

void wait(semaphore *S) {
    S->value--;
    if (S->value < 0) {
        add this process to S->list;
        block();
    }
}

void signal(semaphore *S) {
    S->value++;
    if (S->value <= 0) {
        remove a process P from S->list;
        wakeup(P);
    }
}
```

Types of Semaphores:

1. **Binary Semaphore**: Can only take values 0 or 1. Used for mutual exclusion.

2. **Counting Semaphore**: Can take any non-negative integer value. Used for resource management.

Example: Using semaphores for mutual exclusion

```c
semaphore mutex = 1;  // Binary semaphore for mutual exclusion

do {
    wait(&mutex);
        // Critical Section
    signal(&mutex);
        // Remainder Section
} while (true);
```

Example: Using semaphores for synchronization (producer-consumer problem)

```c
semaphore full = 0;    // Counts the number of items in buffer
semaphore empty = n;   // Counts the number of empty slots in buffer
semaphore mutex = 1;   // For mutual exclusion

void producer() {
    do {
        // Produce item
        wait(&empty);
        wait(&mutex);
            // Add item to buffer
        signal(&mutex);
        signal(&full);
    } while (true);
}

void consumer() {
    do {
        wait(&full);
        wait(&mutex);
            // Remove item from buffer
        signal(&mutex);
        signal(&empty);
        // Consume item
    } while (true);
}
```

### 2.5 Monitors

A monitor is a high-level synchronization construct that encapsulates both data and procedures within it. It guarantees mutual exclusion among processes that want to execute the procedures of the monitor.

Key characteristics:
- Only one process can be active within the monitor at a time.
- The monitor implementation is responsible for enforcing mutual exclusion.
- A process enters the monitor by invoking one of its procedures.

Structure of a monitor:

```
monitor monitor_name
{
    // shared variable declarations
    condition x, y;
    
    procedure P1 (...) {
        ...
    }
    
    procedure P2 (...) {
        ...
    }
    
    initialization code (...) {
        ...
    }
}
```

Monitor operations:
- `wait(c)`: The calling process is suspended until another process calls `signal(c)`.
- `signal(c)`: Resumes exactly one suspended process. If no process is suspended, then the signal operation has no effect.

Example: Bounded-Buffer Problem using a monitor

```
monitor bounded_buffer
{
    int buffer[N];
    int count = 0, in = 0, out = 0;
    condition full, empty;
    
    procedure deposit(int item) {
        if (count == N) 
            wait(full);
        buffer[in] = item;
        in = (in + 1) % N;
        count++;
        if (count == 1) 
            signal(empty);
    }
    
    procedure remove(int *item) {
        if (count == 0) 
            wait(empty);
        *item = buffer[out];
        out = (out + 1) % N;
        count--;
        if (count == N-1) 
            signal(full);
    }
}
```

### 2.6 Classic Synchronization Problems

#### 2.6.1 Dining Philosophers Problem

Problem Statement: Five philosophers sit around a circular table with five forks. Each philosopher needs two forks to eat. Ensure that no philosopher starves and the system doesn't deadlock.

Detailed solution using semaphores:

```c
#define N 5
#define LEFT (i + N - 1) % N
#define RIGHT (i + 1) % N
#define THINKING 0
#define HUNGRY 1
#define EATING 2

int state[N];
semaphore mutex = 1;
semaphore s[N];

void philosopher(int i) {
    while (true) {
        think();
        take_forks(i);
        eat();
        put_forks(i);
    }
}

void take_forks(int i) {
    wait(&mutex);
    state[i] = HUNGRY;
    test(i);
    signal(&mutex);
    wait(&s[i]);
}

void put_forks(int i) {
    wait(&mutex);
    state[i] = THINKING;
    test(LEFT);
    test(RIGHT);
    signal(&mutex);
}

void test(int i) {
    if (state[i] == HUNGRY && state[LEFT] != EATING && state[RIGHT] != EATING) {
        state[i] = EATING;
        signal(&s[i]);
    }
}
```

This solution prevents deadlock by ensuring that a philosopher picks up both forks or none. The `test()` function is called not only when a philosopher is hungry but also when a philosopher finishes eating, potentially allowing waiting neighbors to eat.

#### 2.6.2 Readers-Writers Problem

Problem Statement: Multiple processes want to read and write to a shared database. Allow multiple readers to read simultaneously, but only one writer can write at a time.

Detailed solution using semaphores (favoring readers):

```c
semaphore mutex = 1;      // Controls access to reader_count
semaphore wrt = 1;        // Controls write access
int reader_count = 0;     // Number of readers accessing the database

void reader() {
    while (true) {
        wait(&mutex);
        reader_count++;
        if (reader_
