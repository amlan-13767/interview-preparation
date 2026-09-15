# Operating Systems --- Interview Notes

## 1. Role of an OS

The OS manages hardware/software resources and provides an interface
between applications/users and hardware.

Major areas:

``` mermaid
flowchart TD
    A[Applications] --> B[System Calls]
    B --> C[Kernel]
    C --> D[Process Management]
    C --> E[Memory Management]
    C --> F[File System]
    C --> G[I/O and Devices]
    C --> H[Networking]
    C --> I[Security]
    D --> J[CPU]
    E --> K[RAM]
    F --> L[Storage]
```

## 2. Kernel

The kernel handles privileged operations such as:

-   process scheduling
-   memory management
-   device control
-   filesystem operations
-   system calls

------------------------------------------------------------------------

# 3. System calls

A system call is the controlled interface through which a user program
requests kernel services.

Examples conceptually:

-   process creation
-   file open/read/write
-   memory mapping
-   socket operations

------------------------------------------------------------------------

# 4. Process

A process is a program in execution.

Typical process information includes:

-   program counter
-   registers
-   address space
-   open files
-   scheduling information

PCB = Process Control Block.

------------------------------------------------------------------------

# 5. Process states

``` mermaid
stateDiagram-v2
    [*] --> New
    New --> Ready
    Ready --> Running
    Running --> Ready
    Running --> Waiting
    Waiting --> Ready
    Running --> Terminated
```

------------------------------------------------------------------------

# 6. Process vs Thread

  -----------------------------------------------------------------------
  Process                             Thread
  ----------------------------------- -----------------------------------
  Own address space                   Shares process address space

  More isolation                      Less isolation

  More expensive context switching    Usually cheaper

  IPC required for many forms of      Shared memory makes communication
  communication                       easier

  Failure is more isolated            Thread failure can affect process
  -----------------------------------------------------------------------

Threads share process resources such as code/data/heap but have their
own stack/register state.

------------------------------------------------------------------------

# 7. Context switching

CPU switches execution from one task to another.

OS saves current execution state and restores another.

Costs include:

-   state save/restore
-   cache/TLB effects
-   scheduler overhead

Too many context switches can hurt performance.

------------------------------------------------------------------------

# 8. CPU Scheduling

### FCFS

First Come First Serve.

Simple but can cause convoy effect.

### SJF

Shortest Job First.

Can minimize average waiting time under ideal knowledge but requires
burst prediction.

### SRTF

Preemptive version of SJF.

### Round Robin

Each process receives a time quantum.

Good for interactive systems.

### Priority scheduling

Highest-priority task first.

Can cause starvation.

### Aging

Gradually increases waiting task priority to reduce starvation.

------------------------------------------------------------------------

# 9. Synchronization

When multiple execution units access shared state, race conditions can
occur.

### Race condition

Outcome depends on timing/order.

Example:

``` text
counter = 10

Thread A reads 10
Thread B reads 10
A writes 11
B writes 11

Expected: 12
Actual: 11
```

------------------------------------------------------------------------

# 10. Critical section

A critical section is code accessing shared resources that must be
protected against unsafe concurrent access.

Requirements commonly discussed:

-   mutual exclusion
-   progress
-   bounded waiting

------------------------------------------------------------------------

# 11. Mutex vs Semaphore

### Mutex

Ownership-style lock for mutual exclusion.

### Semaphore

Integer synchronization primitive.

Binary semaphore can resemble a lock, while counting semaphores can
manage multiple identical resources.

### Monitor

Higher-level synchronization abstraction combining shared state and
synchronization operations.

------------------------------------------------------------------------

# 12. IPC

Inter-Process Communication methods:

-   pipes
-   message queues
-   shared memory
-   sockets
-   signals

Shared memory can be fast but requires synchronization.

------------------------------------------------------------------------

# 13. Deadlock

Four necessary Coffman conditions:

1.  mutual exclusion
2.  hold and wait
3.  no preemption
4.  circular wait

Break any condition to prevent classic deadlock.

### Handling

-   prevention
-   avoidance
-   detection and recovery

### Banker's algorithm

Deadlock avoidance based on safe-state reasoning.

------------------------------------------------------------------------

# 14. Starvation vs Deadlock vs Livelock

**Deadlock:** tasks wait indefinitely for one another.

**Starvation:** a task waits indefinitely because others repeatedly get
the resource.

**Livelock:** tasks keep changing state/responding but make no useful
progress.

------------------------------------------------------------------------

# 15. Memory management

A process uses virtual addresses. OS/MMU maps them to physical memory.

### Paging

Virtual memory is divided into pages; physical memory into frames.

Page table maps page → frame.

------------------------------------------------------------------------

# 16. Page fault

A page fault occurs when a required page is not currently available in
the needed physical-memory state.

High-level path:

``` text
CPU access
   ↓
Page table lookup
   ↓
Not present
   ↓
Page fault
   ↓
OS locates page
   ↓
Load into frame
   ↓
Update page table
   ↓
Restart instruction
```

Disk/SSD access is far slower than RAM.

------------------------------------------------------------------------

# 17. Virtual memory

Benefits:

-   process isolation
-   memory abstraction
-   ability to use storage as backing
-   controlled address spaces

------------------------------------------------------------------------

# 18. Page replacement

When memory is full, OS may evict a page.

Algorithms:

-   FIFO
-   Optimal
-   LRU
-   Clock/Second Chance

### Belady's anomaly

FIFO can sometimes have more page faults after increasing the number of
frames.

------------------------------------------------------------------------

# 19. Thrashing

System spends excessive time paging instead of executing useful work.

Causes:

-   insufficient memory
-   too many active processes
-   poor locality/working-set conditions

------------------------------------------------------------------------

# 20. Fragmentation

### Internal fragmentation

Allocated block contains unused space inside it.

### External fragmentation

Free memory exists but is split into small non-contiguous pieces.

------------------------------------------------------------------------

# 21. File systems

Know:

-   file
-   directory
-   inode concept
-   permissions
-   file allocation
-   buffering
-   caching

------------------------------------------------------------------------

# 22. Important interview Q&A

### Q: Process vs thread?

A process has an independent virtual address space; threads within a
process share most process resources while maintaining their own
execution state such as stack and registers.

### Q: Why are threads faster than processes?

They usually have lower creation/context-switch/communication overhead
because they share an address space, though exact costs depend on OS and
workload.

### Q: Mutex vs semaphore?

Mutex is primarily ownership-based mutual exclusion; semaphore is a
counting synchronization primitive and can coordinate access to multiple
instances.

### Q: What causes deadlock?

All four Coffman conditions must hold simultaneously.

### Q: What is a page fault?

An event where the requested virtual-memory page isn't currently
available in the required physical-memory mapping, causing OS handling
before execution can continue.

### Q: What is starvation?

A task is continually denied resources/CPU while others continue to
progress.


# 23. Additional OS topics

## User mode vs kernel mode

Applications normally run in restricted user mode. Privileged kernel operations require controlled transitions.

## System call vs function call

A normal function call stays within the process address space. A system call crosses into the OS kernel interface and may incur additional overhead and privilege transition.

## User-level vs kernel-level threads

User threads can be managed by a runtime/library. Kernel threads are known/scheduled by the OS.

## File allocation

High-level approaches include:

- contiguous
- linked
- indexed

Each trades off sequential access, fragmentation and random access.

## Disk scheduling

Know at least:

- FCFS
- SSTF
- SCAN
- C-SCAN

SSTF chooses nearest request but can cause starvation. SCAN behaves like an elevator.

## Buffering vs caching vs spooling

**Buffering:** temporary area to smooth producer/consumer speed mismatch.

**Caching:** keep likely-to-be-reused data for faster access.

**Spooling:** queue work/data for a device that handles it asynchronously, classically printing.

## Scheduler vs dispatcher

Scheduler selects which task should run.

Dispatcher performs the context switch and transfers CPU control.

## Priority inversion

A high-priority task waits for a resource held by a lower-priority task, potentially delayed by medium-priority work.

Priority inheritance is one mitigation.

