## Process and Programs
A process is an instance of an executing program. 
A program is a file containing a range of information that describs how to construct a process at runtime. This information includes the following:
- *Binary format identification*: Each program file includes metainformation describing the format of the executable file. In modern Linux it is *Executable and Linking Format* (ELF)
- *Machine Language instructions*: This encodes the algorithm of the program
- *Program entry-point address*: This identifies the location of the instruction at which execution of the program should commence.
- *Data*: The program file contains values used to init variables and also literal constants used by the program.
- *Symble and relocation tables*: Describes the names and locations of functions and variables.
- *Standard-library and dynamic-linking information*: 3rd party libraries and dynamic links to locate those libraries.
- Other informations

### Task struct and task vector
To manage the process in system, Linux represents each process with a `task_struct` in **Linux the term task and process are oftern used interchangably**. The `task vector` is an array of pointers that holds ref of all tasks in a system. The size of `task vector` is by default 512, thus the max number of process in a system is 512. The task struct is a large and complex data structure. It's main components are,
- State
  - Running: either currently running or it is waitng to be assigned to one of a systems CPU.
  - Waiting: The process is waiting for an event or resource
    - Interreptable : waiting on signal or interrupts
    - Uninterruptable: waiting on hardware
  - Stopped
  - **Zombie**: It is a halted process, which, for some reason still has a refrence in the `task_list` vector. It is a dead process.
- Scheduling information
- Identifiers: PID and GID of a process
- Inter process communication: signals, pipes and semaphores from the classic Unix™. Shared memory, FIFO and ans semaphores from system V.
- Links: Reference to parent process and all the child processes in `task_vector`.
- Timers and time
- File system  
- Virtual memory: (Kernel thread and deamon process do not have virtual memory).
- Process specific context.

## Process and Process ID
With the exceptionof a few system processes such as  `init` process has id 1, other process ids are not fixed. Linux kernel limit process id is <= 32767.
When the limit is reached, kernel set the counter to 300 not 1. Because a lot of system and deamon process occupies the lower numbers.

## Memory Layout of a Process
The momory (virtual memory) allocated to a process is composed of a number of parts, usually called segments.
- **Text segment**: Contains the machine-language instructions of the program run by the process. It is sharabel because multiple process may run the same program. It's read only.
- **The initialized data segment**: Global and static variables that are explicitly initialized.
- **The uninitialized data segment**: Global and static variables that are not explicitly initialized. These variables are saved in different mymory because when program is saved in the disk, it is not necessary to allocate space for uninitialized data.
- **Stack**: Dynamically growing and shrinking segment conraining stack frames. One stack frame is allocated for each currently called function. A frame holds a functions local variables, arguments and return value.
- **Heap**: It is an area from which memory for variables can be dynamically allocated at run time. The top end of a heap is called *program break*.

## Files
![files](https://user-images.githubusercontent.com/46394832/71638947-700ff800-2c97-11ea-919e-25d1b0fec8ae.gif)

## Virtual Memory Management
Linux uses *virtual memory management*. Most process has vertual memory, except kernel thread and deamon process. The aim of this technique is to use efficient use of both ram and CPU by exploiting a property that is typical of most programs: *locality of reference*. Most program demostrate 2 type of locality.
1. *Spatial locality*: is the tendency of a program to reference memory addresses that are near those that were recently accessed. Because of sequential processing of instructions and sometimes sequential processing of data structure.
2. *Temporal locality*: is the tendency of a program to access the same memory address in the near future that is accessed in the recent past (because of loops).
The advantage of locality of reference is that, it is possible to run a program while maintaining a part of it's address in RAM.

![process-vm](https://user-images.githubusercontent.com/46394832/71638962-bebd9200-2c97-11ea-8670-0f2cbcb5d3cb.gif) </br>
virtual memory


| Kernel(mapped into process virtual memory, but not accessible to program) |
-----
| argv, environ |
| Stack (grows downwards) |
| unallocated memory |
| Heap (grows upward) |
| Uninitialized data AKA `bss` |
| Initialized data |
| Text (program code) |

A virtual memory scheme splits the memory used by each program into small, fixed-size units called pages. Correspondingly, RAM is divided into a series of page frames of the same size. At any one time, only some of the pages of a program need to be resident in physical memory page frames; these pages form the so-called resident set. Copies of the unused pages of a program are maintained in the swap area—a reserved area of disk space used to supplement the computer’s RAM—and loaded into physical memory only as required. When a process references a page that is not currently resident in physical memory, a page fault occurs, at which point the kernel suspends execution of the process while the page is loaded from disk into memory.

## The Stack and Stack Frames
A special purpose register, the *stack pointer* tracks the current top of the stack. Each time a function is called additional frame is allocated on the stack and removed when function returns.
The kernel stack is a per-process memory region maintained in kernel memory that is used as the stack for execution of the functions called internally during the execution of a system call. (The kernel can’t employ the user stack for this purpose since it resides in unprotected user memory.)

Stack frame contains the following informations:
- *Function arguments and local variables*: In C those are called automatic variables.
- *Call linkage information*: Each function used certain CPU registers, such as the program counter, which points to the next machine-language instruction to be executed. Each time a function calls another, a copy of the values of these registers are saved in the called function's stack, so that when it returns the register values can be restored for the calling function.

## Context Switching
The kernel is responsible for the context switching between processes. Whenever a process is running it is using the processor's registers, stacks and so on. This is the processes context and, when a process is suspended, all of that CPU specific context must be saved in the `task_struct` for the process. When a process is restarted by the scheduler its context is restored from here. 
To understand how it works, let's assume a process is running in the user mode and it's time slice is up. Then:
1. The CPU (the actual hardware) interrupts the current process based on an internal timer, switches into kernel mode, and hands control back to the kernel.
2. The kernel records the current state of the CPU and memotry in `task_struct`, which will be essential to resume the process that was just interrupted.
3. The kernel performs any task that might have come up during the preceding time slick (such as collecting data from input and output, I/O operations).
4. The kernel is now ready to ler another process run. The kernel analyzes the list of peocess and ready to choose and run one.
5. The kernel prepares the memory for the new process and then prepare the CPU.
6. The kernel tells the CPU how long the time slice for the new process will last.
7. The kernel switches the CPU into user mode and hands control of the CPU to the process.



