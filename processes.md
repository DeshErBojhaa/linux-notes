## Process and Programs
A process is an instance of an executing program. 
A program is a file containing a rnage of information that describs how to construct a process at runtime. This information includes the following:
- *Binary format identification*: Each program file includes metainformation describing the format of the executable file. In modern Linux it is *Executable and Linking Format* (ELF)
- *Machine Language instructions*: This encodes the algorithm of the program
- *Program entry-point address*: This identifies the location of the instruction at which execution of the program should commence.
- *Data*: The program file contains values used to init variables and also literal constants used by the program.
- *Symble and relocation tables*: Describes the names and locations of functions and variables.
- *Standard-library and dynamic-linking information*: 3rd party libraries and dynamic links to locate those libraries.
- Other informations

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

## Virtual Memory Management
Linux uses *virtual memory management*. The aim of this technique is to use efficient use of both ram and CPU by exploiting a property that is typical of most programs: *locality of reference*. Most program demostrate 2 type of locality.
1. *Spatial locality*: is the tendency of a program to reference memory addresses that are near those that were recently accessed. Because of sequential processing of instructions and sometimes sequential processing of data structure.
2. *Temporal locality*: is the tendency of a program to access the same memory address in the near future that is accessed in the recent past (because of loops).
The advantage of locality of reference is that, it is possible to run a program while maintaining a part of it's address in RAM.

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
