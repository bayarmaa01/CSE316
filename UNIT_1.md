# CSE316 Operating Systems - Unit 1 Overview

## 1. Introduction to Operating Systems

### Operating System Operations and Functions

- **Definition**: An operating system (OS) is software that manages computer hardware and provides services for computer programs.

#### Key Functions:
- Process management
- Memory management
- File system management
- I/O management
- Network management
- Security management

#### Operations:
- Booting
- Memory allocation
- Task scheduling
- Resource allocation
- User interface provision

### Multiprogramming and Multiprocessing Systems

- **Multiprogramming**: Allows multiple programs to run concurrently by organizing them so that the CPU always has one to execute.
  - Improves CPU utilization
  - Increases system throughput

- **Multiprocessing**: Uses two or more CPUs within a single computer system.
  - Types: Symmetric Multiprocessing (SMP) and Asymmetric Multiprocessing (AMP)
  - Advantages: Increased throughput, improved reliability

## 2. Operating System Structure

### System Calls

- **Definition**: Programming interface to the services provided by the OS.
- **Purpose**: Provide an interface between a process and the operating system.

#### Types of System Calls:
1. Process Control (e.g., fork(), exit())
2. File Management (e.g., open(), read(), write(), close())
3. Device Management
4. Information Maintenance
5. Communications
6. Protection

## 3. Process Management

### Process States

1. New
2. Ready
3. Running
4. Waiting (Blocked)
5. Terminated

### Process Scheduling

- **Definition**: Allocating CPU time to different processes.

#### Scheduling Algorithms:
- First-Come, First-Served (FCFS)
- Shortest Job First (SJF)
- Priority Scheduling
- Round Robin (RR)

### Operations on Processes

- Creation
- Termination
- Suspension
- Resumption
- Inter-Process Communication (IPC)

### Process Concept

- **Definition**: A program in execution.
- **Components**: 
  - Program counter
  - Stack
  - Data section

### Life Cycle

1. Creation
2. Execution
3. Termination

### Process Control Block (PCB)

- **Definition**: Data structure containing information associated with each process.

#### Contents of PCB:
- Process ID
- Process State
- Program Counter
- CPU Registers
- CPU Scheduling Information
- Memory Management Information
- I/O Status Information

## 4. Introduction to OS Concepts

### Evolution of OS

1. Serial Processing
2. Simple Batch Systems
3. Multiprogrammed Batch Systems
4. Time-Sharing Systems
5. Personal Computer Systems
6. Parallel Systems
7. Distributed Systems
8. Real-Time Systems
9. Mobile Systems

### Operating System Modes

1. **User Mode**: Limited privileges, restricted access to hardware and memory.
2. **Kernel Mode**: Full access to hardware and memory, execution of privileged instructions.

### OS Services and Functions

- Program execution
- I/O operations
- File system manipulation
- Communications
- Error detection
- Resource allocation
- Accounting
- Protection and security

### OS Structure

#### Kernel and its Types

1. **Monolithic Kernel**: 
   - All OS services operate in kernel space
   - Examples: Unix, Linux

2. **Microkernel**:
   - Minimal functionalities in kernel space
   - Most services run in user space
   - Example: QNX

3. **Hybrid Kernel**:
   - Combines aspects of monolithic and microkernel
   - Example: Windows NT

### Shell

- **Definition**: User interface to interact with the kernel.
- **Types**: 
  - Command-Line Interface (CLI): e.g., Bash, PowerShell
  - Graphical User Interface (GUI): e.g., Windows Explorer, GNOME
