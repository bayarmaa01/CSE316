# CSE316: Memory Management

## 1. Objectives and Functions of Memory Management

Memory management is a crucial aspect of operating systems, responsible for efficiently allocating and deallocating memory resources to processes.

### Objectives:
1. **Relocation**: Ability to move processes in memory without affecting their execution.
2. **Protection**: Ensure that processes can only access their own memory space.
3. **Sharing**: Allow multiple processes to access the same memory (e.g., shared libraries).
4. **Logical organization**: Provide a logical view of memory to processes, abstracting physical details.
5. **Physical organization**: Manage physical memory efficiently, considering hardware constraints.

### Functions:
1. **Allocation**: Assign memory to processes as they are created or grow.
2. **Deallocation**: Free memory when processes terminate or shrink.
3. **Tracking**: Keep track of which parts of memory are in use and by whom.
4. **Protection**: Implement access controls to prevent unauthorized memory access.
5. **Optimization**: Minimize fragmentation and maximize memory utilization.

## 2. Simple Resident Monitor Program

A resident monitor is a basic form of memory management used in early computer systems. It divides memory into two parts: one for the operating system (resident monitor) and one for user programs.

### Characteristics:
- Fixed partition of memory
- Single user program in memory at a time
- Limited multitasking capabilities

### Example:
```
+------------------+
|  Resident Monitor|
+------------------+
|                  |
|   User Program   |
|                  |
+------------------+
```

## 3. Overlays and Swapping

### Overlays:
Overlays allow a program larger than physical memory to run by keeping only the currently needed parts in memory.

#### Process:
1. Divide program into modules
2. Load only necessary modules into memory
3. Swap modules in and out as needed

#### Example:
```
+------------------+
|  Resident Monitor|
+------------------+
|   Main Module    |
+------------------+
|  Overlay Area    |
| (Module A/B/C)   |
+------------------+
```

### Swapping:
Swapping involves moving entire processes between main memory and secondary storage (usually disk).

#### Process:
1. Move inactive process from memory to disk
2. Move active process from disk to memory

#### Advantages:
- Allows more processes than physical memory can hold
- Improves CPU utilization

#### Disadvantages:
- Overhead of moving large amounts of data
- Requires fast storage systems for efficiency

## 4. Paging Schemes

Paging is a memory management scheme that eliminates the need for contiguous allocation of physical memory.

### Simple Paging:

- Divides physical memory into fixed-size blocks called frames
- Divides logical memory into blocks of the same size called pages
- Uses a page table to map logical pages to physical frames

#### Example:
```
Logical Address Space    Physical Address Space
+--------+               +--------+
| Page 0 |  ---->        | Frame 3|
+--------+               +--------+
| Page 1 |  ---->        | Frame 1|
+--------+               +--------+
| Page 2 |  ---->        | Frame 4|
+--------+               +--------+
                         | Frame 2|
                         +--------+
                         | Frame 0|
                         +--------+
```

### Multi-level Paging:

Used when the page table itself becomes too large to fit in memory.

- Divides the page table into multiple levels
- Only the top-level page table needs to be in memory at all times

#### Example (Two-level paging):
```
Logical Address
+--------+--------+--------+
| P1 | P2 |    Offset     |
+--------+--------+--------+
   |       |
   |       v
   |   +--------+
   |   | Page 2 |  ---->  Frame Number
   v   +--------+
+--------+
| Page 1 |
+--------+
```

## 5. Fragmentation

Fragmentation refers to wasted space in memory that becomes unusable for allocation.

### Internal Fragmentation:
- Occurs when allocated memory is slightly larger than requested memory
- Common in fixed-size allocation schemes like paging

#### Example:
```
+-------------------+
| Process (4.5 KB)  |
+-------------------+
| Unused (0.5 KB)   |
+-------------------+
```

### External Fragmentation:
- Occurs when free memory is split into small blocks
- Common in variable-size allocation schemes

#### Example:
```
+-------------------+
| Process A (2 KB)  |
+-------------------+
| Free (1 KB)       |
+-------------------+
| Process B (3 KB)  |
+-------------------+
| Free (1 KB)       |
+-------------------+
```

## 6. Virtual Memory Concept

Virtual memory is a memory management technique that provides an idealized abstraction of the storage resources actually available on a given machine.

### Key Concepts:
1. Allows execution of processes not completely in memory
2. Logical address space can be much larger than physical address space
3. Separates user logical memory from physical memory

### Benefits:
- Allows running larger programs than physical memory
- Increases degree of multiprogramming
- Less I/O needed to load or swap processes

## 7. Demand Paging

Demand paging is a method of virtual memory management where pages are loaded into memory only when they are needed.

### Process:
1. Pages are initially marked as invalid in page table
2. When a page is accessed, a page fault occurs
3. OS loads the required page into memory
4. Page table is updated, and process continues

### Advantages:
- Less I/O and memory needed
- Faster process startup
- More efficient use of memory

## 8. Page Fault Interrupt

A page fault occurs when a program accesses a page that is mapped in the virtual address space, but not loaded in physical memory.

### Page Fault Handling Process:
1. Trap to the operating system
2. Save the state of the process
3. Determine that the interrupt was a page fault
4. Check if the page reference was valid
5. Find a free frame (or free a frame if necessary)
6. Read the desired page into the free frame
7. Update the page table
8. Restart the instruction that caused the page fault

## 9. Page Replacement Algorithms

Page replacement algorithms decide which memory pages to page out when a page of memory needs to be allocated.

### Common Algorithms:

1. **First-In-First-Out (FIFO)**:
   - Replaces the oldest page in memory
   - Simple but can suffer from Belady's anomaly

2. **Optimal Page Replacement**:
   - Replaces the page that will not be used for the longest period
   - Theoretical algorithm, not implementable in practice

3. **Least Recently Used (LRU)**:
   - Replaces the page that has not been used for the longest period
   - Good performance but can be expensive to implement

4. **Clock Algorithm**:
   - Approximation of LRU
   - Uses a circular list of pages with a "use" bit

### Example (LRU):
```
Page Reference String: 7, 0, 1, 2, 0, 3, 0, 4, 2, 3
3 Frames:

Time    Page    Frames          Page Fault?
1       7       7 - -           Yes
2       0       7 0 -           Yes
3       1       7 0 1           Yes
4       2       2 0 1           Yes
5       0       2 0 1           No
6       3       2 3 1           Yes
7       0       2 3 0           Yes
8       4       4 3 0           Yes
9       2       4 2 0           Yes
10      3       3 2 0           Yes
```

## 10. Segmentation

Segmentation is a memory management scheme that supports the user's view of memory.

### Simple Segmentation:
- Memory is divided into segments of varying sizes
- Each segment represents a logical unit (e.g., function, array)
- Uses a segment table to map logical addresses to physical addresses

#### Example:
```
Logical Address    Segment Table     Physical Memory
+-----------+      +-----------+     +-----------+
| Segment 0 |  --> | Base  Limit|    | Segment 2 |
+-----------+      +-----------+     +-----------+
| Segment 1 |  --> | Base  Limit|    | Segment 0 |
+-----------+      +-----------+     +-----------+
| Segment 2 |  --> | Base  Limit|    | Segment 1 |
+-----------+      +-----------+     +-----------+
```

### Multi-level Segmentation:
Similar to multi-level paging, used when the segment table becomes too large.

### Segmentation with Paging:
Combines benefits of both segmentation and paging.

- Logical address space is divided into segments
- Each segment is further divided into pages
- Provides both logical separation and efficient memory management

#### Example:
```
Logical Address
+--------+--------+--------+
|Segment | Page # | Offset |
+--------+--------+--------+
    |        |
    |        v
    |    +--------+
    |    | Page # |  ---->  Frame #
    v    +--------+
+--------+
|Segment |
+--------+
```

This combined approach allows for flexible memory allocation while maintaining the logical structure of programs.
