# CSE316: Deadlocks and File Management (Expanded)

## Unit IV: Deadlocks

### 1. Deadlock Characterization

A deadlock is a situation in which two or more processes are unable to proceed because each is waiting for the other to release a resource. Deadlocks occur when the following four conditions hold simultaneously:

1. **Mutual Exclusion**: At least one resource must be held in a non-sharable mode. Only one process at a time can use the resource.
   - Example: A printer can only be used by one process at a time.

2. **Hold and Wait**: A process must be holding at least one resource while waiting to acquire additional resources held by other processes.
   - Example: Process A holds Resource 1 and is waiting for Resource 2, which is held by Process B.

3. **No Preemption**: Resources cannot be forcibly taken away from a process; they must be released voluntarily by the process holding them.
   - Example: The operating system cannot forcibly take away a printer from a process that's using it.

4. **Circular Wait**: A circular chain of two or more processes, each waiting for a resource held by the next process in the chain.
   - Example: Process A is waiting for a resource held by Process B, which is waiting for a resource held by Process C, which is waiting for a resource held by Process A.

Visual representation:
```
Process A ---> Resource 1 ---> Process B
    ^                              |
    |                              |
    |                              v
Process C <--- Resource 3 <--- Resource 2
```

### 2. Deadlock Handling

There are four main strategies for handling deadlocks:

1. **Deadlock Prevention**: Ensure that at least one of the four necessary conditions for deadlock cannot occur.
   - Pros: Guarantees no deadlocks
   - Cons: Low resource utilization, reduced concurrency

2. **Deadlock Avoidance**: Make careful resource allocation decisions to ensure the system never enters a deadlocked state.
   - Pros: Higher resource utilization than prevention
   - Cons: Requires advance knowledge of resource usage, may still lead to low utilization

3. **Deadlock Detection and Recovery**: Allow deadlocks to occur, detect them, and then take action to recover.
   - Pros: Allows maximum resource utilization
   - Cons: Overhead of detection, potential loss of work during recovery

4. **Ignoring the Problem**: Pretend deadlocks never occur and let the system deal with them if they do.
   - Pros: No overhead
   - Cons: May lead to system instability or data loss

### 3. Deadlock Prevention

Deadlock prevention involves eliminating one of the four necessary conditions:

1. **Eliminating Mutual Exclusion**:
   - Not always possible, as some resources are inherently non-sharable.
   - Example: Implement spooling for printers, where print jobs are queued and processed one at a time.

2. **Eliminating Hold and Wait**:
   - Require processes to request all resources at once.
   - Alternatively, require processes to release all resources before requesting new ones.
   - Example: A process must acquire all needed database locks at once, or none at all.

3. **Eliminating No Preemption**:
   - Allow resources to be forcibly taken away from processes.
   - Example: If a process holding some resources requests another resource that cannot be immediately allocated, all resources currently being held are preempted.

4. **Eliminating Circular Wait**:
   - Impose a total ordering of resource types and require processes to request resources in that order.
   - Example: Assign unique numbers to each resource type. Processes can only request resources in ascending order of these numbers.

### 4. Deadlock Avoidance

Deadlock avoidance requires:

1. Information about future resource requests
2. Careful allocation decisions

Key concepts:
- **Safe State**: A state from which the system can allocate resources to each process in some order and still avoid deadlock.
- **Unsafe State**: A state that may lead to deadlock.

Algorithms for deadlock avoidance:

1. **Resource Allocation Graph Algorithm**: For single instances of resources.
   - Maintain a resource allocation graph
   - Only grant a request if it doesn't form a cycle in the graph
   
   Example:
   ```
   P1 ---> R1 ---> P2
           ^        |
           |        |
           +---- R2 <-
   ```
   P1 requesting R2 would be denied as it would create a cycle.

2. **Banker's Algorithm**: For multiple instances of resources.
   - Maintain state information: available resources, maximum resource needs of processes, current allocation
   - Only grant a request if it results in a safe state
   
   Example:
   ```
   Available resources: A=3, B=3, C=2
   
   Process   Max Needs    Current Allocation
   P0        (7,5,3)      (0,1,0)
   P1        (3,2,2)      (2,0,0)
   P2        (9,0,2)      (3,0,2)
   P3        (2,2,2)      (2,1,1)
   P4        (4,3,3)      (0,0,2)
   ```
   The system checks if granting a request would lead to a safe state before allocating resources.

### 5. Deadlock Detection

Deadlock detection involves:

1. Maintaining a system resource allocation graph or state.
2. Periodically invoking an algorithm to search for cycles in the graph or an unsafe state in the system.

Detection algorithms:

1. **Wait-for Graph**: For single instance of each resource type.
   - Construct a graph where nodes are processes
   - Edge from Pi to Pj if Pi is waiting for a resource held by Pj
   - Periodically check for cycles in the graph
   
   Example:
   ```
   P1 ---> P2 ---> P3
   ^               |
   |               |
   +--- P4 <-------+
   ```
   This graph indicates a deadlock involving P1, P2, P3, and P4.

2. **Banker's Algorithm (Detection Version)**: For multiple instances of resource types.
   - Similar to avoidance version, but considers only current allocation and requests
   - Attempts to find a sequence of process completions
   - If no such sequence exists, system is in deadlock state

### 6. Deadlock Recovery

Once a deadlock is detected, the system must recover. Methods include:

1. **Process Termination**:
   - Abort all deadlocked processes
     - Pros: Simple, guaranteed to break deadlock
     - Cons: May lose significant computation
   - Abort one process at a time until the deadlock is eliminated
     - Pros: Minimizes lost work
     - Cons: Overhead of repeated deadlock detection

   Criteria for selecting processes to terminate:
   - Priority of the process
   - Computation time already consumed
   - Resources the process has used
   - Resources the process needs to complete
   - Number of processes that will need to be terminated

2. **Resource Preemption**:
   - Select a victim
   - Rollback the victim process
   - Preempt resources and give them to other processes

   Considerations for choosing a victim:
   - Cost of resource preemption
   - Number of resources the victim has
   - How long the victim has computed

   Rollback strategies:
   - Total rollback: Abort and restart the process
   - Partial rollback: Roll back to a safe state, using checkpoints

### 7. Starvation

Starvation is a problem related to deadlock where a process is indefinitely denied necessary resources. It can occur when:

- Resource allocation policies favor certain processes
- There's an issue with priority scheduling

Examples of starvation:
1. In a priority-based CPU scheduling, low-priority processes may never get CPU time if there's a constant stream of high-priority processes.
2. In a page replacement algorithm like Least Recently Used (LRU), some pages may never get loaded if they're always the least recently used.

To prevent starvation:
- Use a fair resource allocation policy (e.g., First-Come-First-Served)
- Implement aging, where a process's priority increases the longer it waits
  Example: A process starts with priority 0. Every N seconds it waits, its priority increases by 1.

### 8. Critical Regions

A critical region (or critical section) is a part of a program where shared resources are accessed. Proper management of critical regions is crucial for preventing race conditions and ensuring correct program behavior in concurrent systems.

Key properties of a good critical section solution:
1. Mutual Exclusion: Only one process can be in the critical section at a time.
2. Progress: If no process is in the critical section and some processes want to enter, only processes not in their remainder section can participate in the decision, and this selection can't be postponed indefinitely.
3. Bounded Waiting: There exists a bound on the number of times other processes are allowed to enter their critical sections after a process has made a request to enter its critical section and before that request is granted.

Example implementation using semaphores:
```c
semaphore mutex = 1;  // Binary semaphore

void process() {
    while (true) {
        wait(mutex);  // Enter critical section
        // Critical section
        signal(mutex);  // Exit critical section
        // Remainder section
    }
}
```

## Information Management: Files and Directories

### 1. Files and Directories

- **File**: A named collection of related information stored on secondary storage.
  - Attributes: Name, Identifier, Type, Location, Size, Protection, Time, date, and user identification

- **Directory**: A system catalog that provides information about all files in the system.
  - Functions: 
    1. Translate file names to their metadata locations
    2. Group files for organization
    3. Allow file sharing among users

### 2. Directory Structure

Common directory structures include:

1. **Single-Level Directory**: 
   - All files in one directory
   - Simple, but limited organization
   ```
   Root Directory
   |-- File1
   |-- File2
   |-- File3
   ```

2. **Two-Level Directory**: 
   - Separate directory for each user
   - Allows users to have same file names
   ```
   Root Directory
   |-- UserA
   |   |-- File1
   |   |-- File2
   |-- UserB
   |   |-- File1
   |   |-- File3
   ```

3. **Tree-Structured Directory**: 
   - Allows users to create subdirectories
   - Efficient organization of files
   ```
   Root Directory
   |-- UserA
   |   |-- Projects
   |   |   |-- Project1
   |   |   |-- Project2
   |   |-- Documents
   |       |-- Doc1
   |       |-- Doc2
   |-- UserB
       |-- Downloads
       |-- Pictures
   ```

4. **Acyclic-Graph Directory**: 
   - Allows sharing of subdirectories and files
   - Prevents cycles
   ```
   Root Directory
   |-- UserA
   |   |-- Project1
   |   |-- SharedFolder (link)
   |-- UserB
       |-- SharedFolder
           |-- File1
           |-- File2
   ```

5. **General Graph Directory**: 
   - Allows cycles in the directory structure
   - Difficult to manage (e.g., garbage collection)
   ```
   Root Directory
   |-- Folder1
   |   |-- Subfolder1
   |       |-- Link to Folder1
   |-- Folder2
       |-- Link to Folder1
   ```

### 3. Directory Implementation

Two common methods for implementing directories:

1. **Linear List**:
   - Simple to implement
   - Time-consuming to execute (O(n) search time)
   - Appropriate for small directories
   
   Implementation:
   ```c
   struct directory_entry {
       char file_name[256];
       int inode_number;
   };

   struct directory_entry directory[MAX_FILES];
   ```

2. **Hash Table**:
   - Faster search time (O(1) average case)
   - More complex implementation
   - Appropriate for larger directories
   
   Implementation:
   ```c
   #define TABLE_SIZE 1024

   struct hash_entry {
       char file_name[256];
       int inode_number;
       struct hash_entry *next;
   };

   struct hash_entry *hash_table[TABLE_SIZE];

   int hash(char *file_name) {
       // Hash function implementation
   }
   ```

## File Management

### 1. Allocation Methods

Three main strategies for allocating file space:

1. **Contiguous Allocation**:
   - Each file occupies contiguous blocks on disk
   - Simple, but can lead to external fragmentation
   
   Advantages:
   - Supports both sequential and direct access
   - Simple to implement
   
   Disadvantages:
   - External fragmentation
   - Difficult to grow files
   
   Example:
   ```
   Disk blocks: [File A][File A][File A][Free][File B][File B][Free][Free][File C]
   ```

2. **Linked Allocation**:
   - Each file is a linked list of disk blocks
   - No external fragmentation, but no efficient direct access
   
   Advantages:
   - No external fragmentation
   - Files can grow easily
   
   Disadvantages:
   - Only efficient for sequential access
   - Reliability issues (losing a link loses rest of file)
   
   Example:
   ```
   File A: Block 1 -> Block 4 -> Block 7
   File B: Block 2 -> Block 5 -> Block 8
   File C: Block 3 -> Block 6 -> Block 9
   ```

3. **Indexed Allocation**:
   - Each file has an index block containing pointers to its data blocks
   - Supports direct access, but requires extra space for the index
   
   Advantages:
   - Supports direct access
   - No external fragmentation
   
   Disadvantages:
   - Extra space needed for index block
   - Small files waste space in index block
   
   Example:
   ```
   File A Index: [Blk 1][Blk 4][Blk 7]
   File B Index: [Blk 2][Blk 5][Blk 8]
   File C Index: [Blk 3][Blk 6][Blk 9]
   ```

### 2. Free-Space Management

Techniques for managing free disk space:

1. **Bit Vector**: 
   - Each block represented by a bit (1 = free, 0 = allocated)
   - Efficient for finding first free block or contiguous blocks
   - Can be memory-intensive for large disks
   
   Example:
   ```
   Bit vector: 10011100110000
   (1 = free, 0 = allocated)
   ```

2. **Linked List**: 
   - Free blocks linked together
   - Only need to store the head of the list
   - Inefficient for finding contiguous free blocks
   
   Example:
   ```
   Free block list: Block 1 -> Block 4 -> Block 5 -> Block 8
   ```

3. **Grouping**: 
   - First free block contains addresses of n free blocks
   - Last of these points to another block of free addresses
   - More efficient than simple linked list for finding contiguous blocks
   
   Example:
   ```
   Group 1: [Blk 2][Blk 3][Blk 7][Ptr to Group 2]
   Group 2: [Blk 9][Blk 10][Blk 11][Null]
   ```

4. **Counting**: 
   - Keep address of first free block and count of contiguous free blocks
   - Good for systems with contiguous allocation
   
   Example:
   ```
   (Starting block, count of free blocks)
   (3, 2), (7, 1), (12, 3)
   ```

Each method has trade-offs in terms of efficiency and space overhead. The choice depends on the specific requirements of the file system and the characteristics of the storage device.

For example:
-
