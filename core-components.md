## The Core Operation System: The Kernel
The term operating system commonly used with two different meanings:
1. The entire package consisting the central software managing a computer's resources and all of the other standard software tools, ex: command line interpreters, GUI, file utils and so on.
2. More narrowly, to refer to the central software that manages and allocates computer resources (i.e. the CPU, RAM, and devices).

The term kernel is ofter used as a synonym for the second meaning. It is possible to run programmes without the Kernel, but havind it greatly simplifies the writing and use of programs. The kernel does it by providing a software layer to manage the limited resources of a computer.

#### Tasks performed by the Kernel
- **Process Scheduling**: Linux is a *preemptive multitasking* operating system.
- **Memory Management**: Linux employs virtual memory management.
  - Process are isolated from one another and from the kernel.
  - Only part of a process need to kept in memory.
- Provision of a file system.
- Creation and termination of processes.
- Access to devices.
- **Networking**: The kernel transmit and receive network messages(packets) on behalf of user process. This task includes routing of network packets to the target system.
- **Provision of a system call application programming interface (API)**: Process can request the kernel to perform various tasks using kernel entry points known as *system calls*.

#### Kernel mode and user mode
