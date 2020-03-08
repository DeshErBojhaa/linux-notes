## The Core Operation System: The Kernel
The term operating system commonly used with two different meanings:
1. The entire package consisting the central software managing a computer's resources and all of the other standard software tools, ex: command line interpreters, GUI, file utils and so on.
2. More narrowly, to refer to the central software that manages and allocates computer resources (i.e. the CPU, RAM, and devices).

The term kernel is ofter used as a synonym for the second meaning. It is possible to run programmes without the Kernel, but havind it greatly simplifies the writing and use of programs. The kernel does it by providing a software layer to manage the limited resources of a computer.

### Tasks performed by the Kernel
- **Process Scheduling**: Linux is a *preemptive multitasking* operating system. The kernel is resoinsible for determining which processes are allowed to use the CPU.
**Important Note: A process is a running program that is assigned resources. i.e. CPU, memory and file system**
**Important Note:** The act of one process giving up control of CPU to another process is called a *context switch.*

- **Memory Management**: Memory management is a complicated task. But modern CPUs inclide a *memory management unit (MMU)* that enables a memory access scheme called *virtual memory*. When the process accesses some of its memory, the MMU intercepts the access and uses a memory address map to translate the memory location from the process into an actual physical memory location on the machine. **The implementation if a memory address map is called a page table** Linux employs virtual memory management.
  - Process are isolated from one another and from the kernel. However, process can share memory.
  - Only part of a process need to kept in memory.
- Provision of a file system.
- Creation and termination of processes.
- Access to devices.
- **Networking**: The kernel transmit and receive network messages(packets) on behalf of user process. This task includes routing of network packets to the target system.
- **Provision of a system call application programming interface (API)**: Process can request the kernel to perform various tasks using kernel entry points known as *system calls*.

### Kernel mode and user mode
Modern CPUs have two modes. Kernel mode is also known as supervisor mode. Areas of virtual memory can be marked as kernel mode or user mode. When running on user mode, CPU can only access memory that is marked as user mode.
Certain operations can only be performed as kernel mode. Such as `halt`. 

## The Shell
A shell is a special-purposed program designed to read commands typed by a user and execute appropriate programs in response to those commands.
On UNIX system, the shell is a user process. Many different shell exists,
- **Bourne shell (sh)**: Oldest, written by **Steve Bourne**
- C shell (csh)
- *Korn Shell*
- **Bourne Again shell (bash)**: GNU's reimplementation of Bourne shell. 

## Users and Groups
In UNIX nominclature, a user is an entity that can run process and own files.
Users exists primarily to support permissions and boundaries. Each user in the system is uniquely identified and users may belong to groups.
### Users
Every user in the system has an unique user name and numeric id (UID). For each user, there are defined by a line in the system password file `/etc/passwd` which includes the following additional information
- Group ID
- Home directory
- Login shell

### Groups
In early UNIX a user can be member of only one group. BSD allowed a user to simultaniously belong to multiple groups. This idea also got into POSIX.1 standard. Each group is identified by a single line in the `etc/group`, which includes the following informations:
- Group name
- Group ID (GID)
- User list

### Super User
Super user id is 0 and normally has login name *root*.

## Single Directory Hierarchy, Directories, Links and Files
At the base of this hierarchr is the root directory `/`. All files and directories are children or further removed descendants of the root directory.

### File types
- Regular or plain file.
- pipes.
- sockets.
- directories.
- symbolic links.

### Directories and Links
A directory is a special file whose contents take the form of a table of filenames coupled with references to the corresponding files. This filename-plus-reference association is called a link, and files may have multiple links, and thus multiple names, in the same or in different directories.
Each directory contains atleast two entries. Link to self `.` and link to parent `..`. For the root directory `..` refers to itself. So `/..` is `/`

### Filenames
In most Linux file systems, filenames can be upto 255 characters long. File name can contain any character except `/` and null character `/0`.

### Current working directory
Each process has a current working directory. Inherited from it's parent process. Relative path starts from this directory.

### File ownership and permissions
Each file has an associated user ID and group ID that define the owner of the file and the group to which it belongs. 
For the purpose of accessing a file, the system divides users into three categories: the owner of the file (sometimes termed the user of the file), users who are members of the group matching the file’s group ID (group), and the rest of the world (other). Three permission bits may be set for each of these categories of user (making a total of nine permission bits): read permission allows the contents of the file to be read; write permission allows modification of the contents of the file; and execute permission allows execution of the file, which is either a program or a script to be processed by some interpreter (usually, but not always, one of the shells).
These permissions may also be set on directories, although their meanings are slightly different: read permission allows the contents of (i.e., the filenames in) the directory to be listed; write permission allows the contents of the directory to be changed (i.e., filenames can be added, removed, and changed); **and execute (sometimes called search)** permission allows access to files within the directory (subject to the permissions on the files themselves).
If `ls -l` is run on a directory, something like
`-rw-r--r-- 1 user group 1234 Mar 26 19:34 file.extencion` will be shown. Here, First dash of `-rw-r--r--` is the file type (here a dash means it's a regular file, in case of directories, it will be `d`, in case if symbilic link it will be `l`). Next 3 characters are `user permission`, next 3 characters are `group permission` and last 3 characters are `other permissions`. Each permission can be either,
- `r` Means the file is readable
- `w` Means the file is writable
- `x` Means the file is executable
- `-` Means nothing

Some executable files have an `s` in the user permissions listing instead of an `x`. This indicates that the executable is `setuid`, meaning that when you execute the program, it runs as though **the file owner is the user instead of you**. Many programs use this setuid bit to run as root in order to get the privileges they need to change system files. One example is the passwd program, which needs to change the `/etc/passwd` file.

| Mode | Meaning | Used For |
| :--- | :--- | :--- |
| 644 | user: (read/write); group+other: read | files |
| 600 | user: (read/write); group+other: none | files |
| 755 | user: (read/write/execute); group+other: (read/execute) | directories, programs |
| 700 | user: (read/write/execute); group+other: none | directories, programs |
| 711 | user: (read/write/execute); group+other: execute | directories |

### Some important file hierarchies
- `/bin` containg ready-to-run files AKA executables. Mostly bin files.
- `/dev` contains device files.
- `/etc` The core system configuration directory contains user password, boot, devices, networking and other setup files.
- `/home` Holds personal directories for regular user.
- `/lib` An abbreviation for library. Contains files that executables can use. There are two types of libraries, `static` and `shared`. Lib directory should contain only **shared libraries**.
- `/proc` Contains information abour currently running directories as well as some kernel parameters.
- `/sys` Similar to `/proc` in that it provides a device and system inteface.
- `/sbin` The place for system executables.
- `/tmp` meh
- `/usr` Although pronounced “user,” this subdirectory has **no user files**. Instead, it contains a large directory hierarchy, including the bulk of the Linux system. Many of the directory names in /usr are the same as those in the root directory (like /usr/bin and /usr/lib), and they hold the same type of files. (The reason that the root directory does not contain the complete system is primarily historic—in the past, it was to keep space requirements low for the root.)
- `/var` Where program records runtime information. **System logging**, user tracking, caches and other files system program creates and manages are here.

## File I/O Model
Same system callls (*open(), read(), write(), close() and so on*) are used to perform I/O on all types of files, including devices.
The kernel essentially provides one file type: a sequential stream of bytes, which in the case of disk files, disks, and tape devices can be randomly accessed and using the *lseek()* system call.
UNIX systems have no *end-of-file* character; the end of file is detected by a read that returns no data.

### File Descriptors
The I/O system calls refer to open files using a *file descriptor* a non negative integer. Normally a process inherits three open file descriptors when started by shell.
1. `0` is *standard input*
2. `1` is *standard output*
3. `2` is *standard error*: The file to which the process writes error messages and notification of exceptional or abnormal conditions.

## Programs
Programs normally exists in 2 forms 
1. Source code that human can understand. 
2. binary machine language instructions that computer can understand.

### Filters
A program that reads data from `stdin`, performs some operations on that and writes it to `stdout`. Ex: `cat, grep, tr, sort, wc sed` and `awk`.

### Command-line arguments
Provide to the program as string, first argument is the name of the program.

## Processes
Put most simply, a process os an instance of an executing program. When a program is executed, the kernel loads the code of the program in the virtual memory, allocates space for program variables, and set up kernel bookkeeping data structures to record various informations such as PID, termination status, UID and GID about the process.

From a kernel's point of view, process are entities among which the kernel must share the various resources of the computer. When the process terminates, all such resources are released for reuse by other process.

### Process momory layout
A process is logically divided into the following parts, known as segments:
- *Text*: the instructions of the program
- *Data*: the static variables used by the program
- *Heap*: an area from which programs can dynamically allocate extra memory.
- *Stack*: a piece of memory that grows and shrinks.

### Process creation and program execution
A process can create a new process using the `fork()` system call. The process that call `fork()`. The kernel creates the chile process by making a duplicate of the parent process. The child process inherites copies of parent's data, stack and heap segments, which it may modify independently.
The child process can either,
- go on to execute a different set of functions in the same source code as the parent.
- or, call the `exec()` system call to load and execute an entirely new program. 

An `exec()` call destroyes the existing text, data, stack and heap segments, replacing them with new segments based on the code of new program.

### Process termination and termination status
Process can terminate in 2 ways.
- It can kill itself by calling `_exit()` systemcall.
- By delivery of a signal.

In either case, the process yields a *termination status*. Status 0 means the process succeeded. Parent process can examin it's child process's termination status by calling `wait()` syscall.

### Process users and group identifiers
Each process has a number of associated UID and GID. Including
- *Real UID and real GID*: A new process inherits these from it's parent process. A login shell gets its real UID and GID from the corresponding fields in the system password file.
- *Effective UID and effective GID*: These two IDs are used in determining the permissions that the process has when accessing protected resources such as files and interprocess communication objects. Typically, the process’s effective IDs have the same values as the corresponding real IDs. Changing the effective IDs is a mechanism that allows a process to assume the privileges of another user or group, as described in privileged process section.
- *Supplementary GID*: These ids identifies additional groups to which a process belongs. A new process inherits these ids from it's parents. A login shell gets it's supplementary GIDs from the group file.

### Privileged processes
A process may be privilaged because it was created by another previleged process. For example by a login shell started by root. Another way a process becomes privilaged is via the *set-user-ID* mechanism.

### Capabilities
Privilages of a super user is divided into a set of distinct units called capabilities. Capability names usually starts with the prefix `CAP_` as in `CAP_KILL`.

### The init process
When booting the system the kernel creates a special process callled *init* the "parent of all processes". Which is derived from the program file `/sbin/init`. **The init process always has process id `1`** and runs with supervised privilages. The init process can not be killed even by the super user. It trminates only when the system shutdowns.

### The deamon process
- It is long lived. A deamon process is often started at system booot and remains in existance until the system is shut down.
- It runs in background, and has not controlling terminal from which it can read input or to which it can write ourput.

### Environment list
Each process has an *environment list*, which is a set of *environment variables* which is a name, value pair that are maintained within the user-space memory of the process. When a new process is created via `fork()` it inherits it's copy of env variables from it's parent process. Thus it is a way for parent process to communicate with the child process. When a child process replaces the running program using `exec()` the new program inherits the environment from the parent program or receives a new environment specified as part of the `exec()` call.
Environment variables are created using `export` command in most shells.

### Resource limits
Using the `setrlimit()` system call, a process can establish upper limit on it's consumption of various resources such as open file, memory and CPU. Each such resource limit has 2 associated values,
- soft limit : which limits the ammoutn of the resources the process may consume. An unprivilaged process may change its soft limit for a particular resource to any value in the range from zero upto the corresponding hard limit. And can only lower it's hard limit.
- hard limit : which is a ceiling on the value to which the soft limit may be adjusted.

A new process is create with `fork()` it inherits copies of its parent's resources limit settings.

## Static and Shared Libraries
**Will write later**

## Interprocess Communication and Synchronization
Most naive one is reading and wirting from a shared file. But it is slow for most *IPCs* (Inter Process Communication). Linux provides,
- *signals*, which are used to indicte that an event occurred
- *pipes* and *FIFOS* (came from system V branch of UNIX) which can used to transfer data between processes.
- *sockets* (came from BSD branch of UNIX)
- *file locking*, which allowes a process to lock a region of a file in order to prevent other processed from reading or updating the file content.
- *message queues*
- *semaphores*, which are used to synchronize the actions of process
- *shared memory*

## Signals
> One important distinction between signals and interupts is, signamls are generated within process and passed to the same or another process, where interupts are generated by outside world (maybe by users) and passed to the **CPU** directly.

Often described as "software interrupts". Each signal type is identified by a different integer, defined with symbolic names of the form `SIGxxxx`. Signals are sent by kernel directly or by other process via kernel.
A kernel may send a signal to a process when one of the following occurres,
- *interrupt character* pressed (Control-C).
- one of the process's children has terminated.
- a timer set by the peocess has expired
- the process tries to access invalid memory address.

When a process receives a signal, it takes one of the following actions,
- ignores the signal.
- process is killed by the signal.
- it is suspended until later being resumed by receipt of a special-purpose signal.

In the interval between the time it is generated and the time it is delivered, a signal is said to be *pending* for a process. Normally, a pending signal is delivered as soon as the receiving process is next scheduled to run, or immediately if the pro- cess is already running. However, it is also possible to *block* a signal by adding it to the process’s signal *mask*. If a signal is generated while it is blocked, it remains pending until it is later unblocked 
