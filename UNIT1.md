# CSE316 Operating Systems

## Table of Contents

1. [Introduction to Operating Systems](#1-introduction-to-operating-systems)
2. [Operating System Structure](#2-operating-system-structure)
3. [Process Management](#3-process-management)
4. [Introduction to OS Concepts](#4-introduction-to-os-concepts)

## 1. Introduction to Operating Systems

### 1.1 Operating System Operations and Functions

#### 1.1.1 Key Functions
#### Key Functions:
- Process management
- Memory management
- File system management
- I/O management
- Network management
- Security management

1. **Process Management**
   - Process creation and termination
   - Scheduling
   - Inter-process communication (IPC)
   - Synchronization mechanisms (e.g., semaphores, mutexes)
   - Deadlock handling strategies

2. **Memory Management**
   - Physical and virtual memory allocation
   - Memory protection
   - Swapping and paging mechanisms
   - Memory compression techniques

3. **File System Management**
   - File and directory operations (create, read, write, delete)
   - Access control and file permissions
   - File system types (e.g., FAT, NTFS, ext4, ZFS)
   - Journaling and recovery mechanisms

4. **I/O Management**
   - Device driver management
   - I/O scheduling algorithms
   - Buffering and caching strategies
   - Spooling for shared devices

5. **Network Management**
   - Protocol stack implementation
   - Network interface configuration
   - Firewall and security features
   - Quality of Service (QoS) management

6. **Security Management**
   - User authentication methods
   - Access control models (e.g., DAC, MAC, RBAC)
   - Encryption and secure communication
   - Intrusion detection and prevention

#### 1.1.2 Operations
- Booting
- Memory allocation
- Task scheduling
- Resource allocation
- User interface provision
1. **Booting Process**
   - BIOS/UEFI initialization
   - Bootloader execution (e.g., GRUB)
   - Kernel loading and initialization
   - User space initialization (init system)

2. **Memory Allocation**
   - Segmentation and paging techniques
   - Memory protection rings
   - Virtual memory management
   - Memory mapping (mmap) implementation

3. **Task Scheduling**
   - Process and thread scheduling algorithms
   - Real-time scheduling considerations
   - Load balancing in multiprocessor systems
   - Priority inversion prevention

4. **Resource Allocation**
   - CPU time allocation strategies
   - Memory allocation algorithms (e.g., buddy system, slab allocation)
   - I/O resource management
   - Deadlock prevention, avoidance, and recovery techniques

5. **User Interface Provision**
   - Command-line interface (CLI) implementation
   - Graphical user interface (GUI) support
   - System call interface design
   - Shell scripting capabilities

### 1.2 Multiprogramming and Multiprocessing Systems
- **Multiprogramming**: Allows multiple programs to run concurrently by organizing them so that the CPU always has one to execute.
  - Improves CPU utilization
  - Increases system throughput

#### 1.2.1 Multiprogramming

- **Concept**: Multiple programs residing in memory simultaneously
- **Benefits**:
  - Improved CPU utilization
  - Increased system throughput
  - Better response time for interactive users
- **Challenges**:
  - Memory management complexity
  - CPU scheduling overhead
  - Resource contention and synchronization issues
- **Implementation Techniques**:
  - Job scheduling algorithms
  - Memory partitioning methods
  - I/O scheduling to maximize concurrency

#### 1.2.2 Multiprocessing
- **Multiprocessing**: Uses two or more CPUs within a single computer system.
  - Types: Symmetric Multiprocessing (SMP) and Asymmetric Multiprocessing (AMP)
  - Advantages: Increased throughput, improved reliability
- **Types**:
  1. **Symmetric Multiprocessing (SMP)**
     - All processors are equal
     - Shared memory and I/O buses
     - Dynamic load balancing
  2. **Asymmetric Multiprocessing (AMP)**
     - Master-slave processor configuration
     - Specialized task allocation
     - Used in some embedded systems
- **Advantages**:
  - Increased processing power and throughput
  - Improved reliability through redundancy
  - Scalability for large-scale computing
- **Challenges**:
  - Increased software complexity
  - Synchronization and coherence issues
  - Potential for race conditions
- **Implementation Considerations**:
  - Cache coherence protocols
  - NUMA (Non-Uniform Memory Access) architectures
  - Process and thread affinity

## 2. Operating System Structure

### 2.1 System Calls
- **Definition**: Programming interface to the services provided by the OS.
- **Purpose**: Provide an interface between a process and the operating system.
  
#### 2.1.1 Types of System Calls
1. Process Control (e.g., fork(), exit())
2. File Management (e.g., open(), read(), write(), close())
3. Device Management
4. Information Maintenance
5. Communications
6. Protection
1. **Process Control**
   - `fork()`: Create a new process
   - `exec()`: Execute a new program
   - `exit()`: Terminate process execution
   - `wait()`: Wait for child process to terminate
   
   Example (C code):
   ```c
   pid_t pid = fork();
   if (pid == 0) {
       // Child process
       execl("/bin/ls", "ls", "-l", NULL);
       exit(0);
   } else if (pid > 0) {
       // Parent process
       wait(NULL);
   }
   ```

2. **File Management**
   - `open()`: Open a file
   - `read()`: Read from a file
   - `write()`: Write to a file
   - `close()`: Close a file descriptor
   - `lseek()`: Reposition read/write file offset
   
   Example (C code):
   ```c
   int fd = open("example.txt", O_RDWR | O_CREAT, 0644);
   char buffer[100];
   ssize_t bytes_read = read(fd, buffer, sizeof(buffer));
   ssize_t bytes_written = write(fd, "Hello, World!", 13);
   close(fd);
   ```

3. **Device Management**
   - `ioctl()`: Control device
   - `read()`, `write()`: Device I/O operations
   - `mmap()`: Map device memory to process memory

4. **Information Maintenance**
   - `getpid()`: Get process ID
   - `alarm()`: Set an alarm clock for process
   - `sleep()`: Suspend process for specified time

5. **Communications**
   - `pipe()`: Create a communication pipe
   - `shmget()`: Create shared memory segment
   - `mmap()`: Map files or devices into memory
   
   Example (C code for pipe):
   ```c
   int pipefd[2];
   pipe(pipefd);
   if (fork() == 0) {
       close(pipefd[1]);
       // Child process reads from pipe
       char buf[100];
       read(pipefd[0], buf, 100);
   } else {
       close(pipefd[0]);
       // Parent process writes to pipe
       write(pipefd[1], "Hello, Child!", 13);
   }
   ```

6. **Protection**
   - `chmod()`: Change file permissions
   - `umask()`: Set file creation mode mask
   - `chown()`: Change file ownership

#### 2.1.2 System Call Interface

- Typically implemented in C or assembly
- Often accessed via API rather than direct system calls
- Examples: POSIX API, Win32 API
- System call dispatch methods:
  1. By number (Linux)
  2. By table (Windows)

#### 2.1.3 System Call Handling

1. User program traps to the kernel
2. Kernel verifies system call number
3. Kernel dispatches to appropriate system call handler
4. System call executes and returns to user program

## 3. Process Management

### 3.1 Process States
1. New
2. Ready
3. Running
4. Waiting (Blocked)
5. Terminated

1. **New**
   - Process is being created
   - Memory is allocated
   - PCB (Process Control Block) is initialized

2. **Ready**
   - Process is loaded into main memory
   - Awaiting CPU allocation
   - Managed in the ready queue

3. **Running**
   - Process is currently executing on a CPU
   - Only one process per CPU can be in this state at a time

4. **Waiting (Blocked)**
   - Process is waiting for an event (e.g., I/O completion)
   - Cannot execute until event occurs
   - Moved to ready state when event completes

5. **Terminated**
   - Process has finished execution
   - PCB is deleted
   - Resources are deallocated

### 3.2 Process Scheduling
- **Definition**: Allocating CPU time to different processes.
#### 3.2.1 Scheduling Algorithms
- First-Come, First-Served (FCFS)
- Shortest Job First (SJF)
- Priority Scheduling
- Round Robin (RR)
  
1. **First-Come, First-Served (FCFS)**
   - Simplest scheduling algorithm
   - Non-preemptive
   - Can lead to convoy effect
   
   Example:
   ```
   Process   Burst Time
   P1        24
   P2        3
   P3        3
   
   Average Waiting Time: (0 + 24 + 27) / 3 = 17
   ```

2. **Shortest Job First (SJF)**
   - Can be preemptive or non-preemptive
   - Optimal for minimizing average waiting time
   - Difficult to implement (predicting burst time)
   
   Example (non-preemptive):
   ```
   Process   Burst Time
   P1        6
   P2        8
   P3        7
   P4        3
   
   Execution Order: P4, P1, P3, P2
   Average Waiting Time: (3 + 16 + 9 + 0) / 4 = 7
   ```

3. **Priority Scheduling**
   - Assigns priority to each process
   - Can lead to starvation of low-priority processes
   - Often implemented with aging to prevent starvation
   
   Example:
   ```
   Process   Burst Time   Priority
   P1        10           3
   P2        1            1
   P3        2            4
   P4        1            5
   P5        5            2
   
   Execution Order: P2, P5, P1, P3, P4
   ```

4. **Round Robin (RR)**
   - Preemptive scheduling
   - Uses time quantum
   - Fair allocation of CPU, but can lead to high context switch overhead
   
   Example (Time Quantum = 4):
   ```
   Process   Burst Time
   P1        24
   P2        3
   P3        3
   
   Execution Order: P1(4), P2(3), P3(3), P1(4), P1(4), P1(4), P1(4), P1(4)
   ```

5. **Multilevel Queue Scheduling**
   - Processes are classified into different groups
   - Each group has its own scheduling algorithm
   - Example queues: System processes, Interactive processes, Batch processes

6. **Multilevel Feedback Queue Scheduling**
   - Similar to multilevel queue, but allows processes to move between queues
   - Adapts to changing behavior of processes
   - Often used in modern operating systems

### 3.3 Operations on Processes
- Creation
- Termination
- Suspension
- Resumption
- Inter-Process Communication (IPC)
  
1. **Creation**
   - Allocate and initialize PCB
   - Allocate memory for the process
   - Initialize process context

2. **Termination**
   - Deallocate PCB
   - Release allocated resources
   - Remove process from scheduling queues

3. **Suspension**
   - Save process state
   - Move process to suspended state
   - Useful for swapping processes to disk

4. **Resumption**
   - Restore process state
   - Move process from suspended to ready state

5. **Inter-Process Communication (IPC)**
   - Mechanisms: Pipes, Message Queues, Shared Memory, Sockets
   - Synchronization methods: Semaphores, Mutexes, Condition Variables

### 3.4 Process Concept

- **Definition**: A program in execution
- **Components**:
  - Program counter
  - Stack
  - Data section
- **Address Space**: Memory allocated to the process
- **Process vs. Program**: A program becomes a process when loaded into memory

  ### Life Cycle

1. Creation
2. Execution
3. Termination

### 3.5 Process Control Block (PCB)

- **Definition**: Data structure containing information associated with each process
- **Contents**:
  1. Process ID (PID)
  2. Process State
  3. Program Counter
  4. CPU Registers
  5. CPU Scheduling Information
  6. Memory Management Information
  7. I/O Status Information
  8. List of Open Files
  9. Pointer to Parent Process
  10. Pointers to Child Processes

## 4. Introduction to OS Concepts

### 4.1 Evolution of OS
1. Serial Processing
2. Simple Batch Systems
3. Multiprogrammed Batch Systems
4. Time-Sharing Systems
5. Personal Computer Systems
6. Parallel Systems
7. Distributed Systems
8. Real-Time Systems
9. Mobile Systems

1. **Serial Processing** (1940s-1950s)
   - No OS, direct machine language programming
   - Manual operation, one program at a time

2. **Simple Batch Systems** (1950s-1960s)
   - Introduction of card readers and line printers
   - Resident monitor for job sequencing

3. **Multiprogrammed Batch Systems** (1960s)
   - Multiple programs in memory
   - CPU scheduling introduced

4. **Time-Sharing Systems** (1960s-1970s)
   - Interactive computing
   - Multiple users sharing computer resources

5. **Personal Computer Systems** (1980s)
   - Single-user systems
   - Focus on user-friendly interfaces

6. **Parallel Systems** (1980s-present)
   - Multiple processors working together
   - Increased processing power

7. **Distributed Systems** (1990s-present)
   - Networked computers working together
   - Distributed computing and storage

8. **Real-Time Systems**
   - Guaranteed response times
   - Used in industrial control, aviation, etc.

9. **Mobile Systems** (2000s-present)
   - Optimized for mobile devices
   - Power management and touch interfaces

### 4.2 Operating System Modes

1. **User Mode**: Limited privileges, restricted access to hardware and memory.
2. **Kernel Mode**: Full access to hardware and memory, execution of privileged instructions.

  
1. **User Mode**
   - Limited privileges
   - Cannot directly access hardware
   - Cannot execute privileged instructions
   - Used for running user applications

2. **Kernel Mode**
   - Full system privileges
   - Direct hardware access
   - Can execute any CPU instruction
   - Used for OS kernel operations

#### Mode Switching
- Occurs via system calls, interrupts, or exceptions
- Involves saving user mode state and loading kernel mode state

### 4.3 OS Services and Functions

1. Program execution
2. I/O operations
3. File system manipulation
4. Communications
5. Error detection
6. Resource allocation
7. Accounting
8. Protection and security

### 4.4 OS Structure

#### 4.4.1 Kernel Types

1. **Monolithic Kernel**
   - All OS services in kernel space
   - Fast performance due to direct function calls
   - Large size and potentially less stable
   - Examples: Traditional Unix, Linux

2. **Microkernel**
   - Minimal kernel, most services in user space
   - More stable and easier to extend
   - Potential performance overhead due to message passing
   - Examples: QNX, MINIX

3. **Hybrid Kernel**
   - Combines monolithic and microkernel approaches
   - Some services in kernel space, others in user space
   - Balances performance and modularity
   - Examples: Windows NT, macOS (XNU kernel)

4. **Exokernel**
   - Provides minimal hardware abstraction
   - Allows more direct management by applications
   - Highly efficient but complex to program

### 4.5 Shell

- **Definition**: User interface to interact with the kernel.
- **Types**: 
  - Command-Line Interface (CLI): e.g., Bash, PowerShell
  - Graphical User Interface (GUI): e.g., Windows Explorer, GNOME
    
1. **Command-Line Interface (CLI)**
   - Text-based interaction
   - Powerful for scripting and automation
   - Examples: Bash (Unix/Linux), PowerShell (Windows)

2. **Graphical User Interface (GUI)**
   - Visual interaction using windows, icons, and pointers
   - More intuitive for novice users
   - Examples: Windows Explorer, GNOME, KDE

#### Shell Functions
- Command interpretation
- Program execution
- I/O redirection
- Pipeline creation
- Environment variable management
- Scripting capabilities
