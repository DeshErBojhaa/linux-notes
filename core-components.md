## The Core Operation System: The Kernel
The term operating system commonly used with two different meanings:
1. The entire package consisting the central software managing a computer's resources and all of the other standard software tools, ex: command line interpreters, GUI, file utils and so on.
2. More narrowly, to refer to the central software that manages and allocates computer resources (i.e. the CPU, RAM, and devices).

The term kernel is ofter used as a synonym for the second meaning. It is possible to run programmes without the Kernel, but havind it greatly simplifies the writing and use of programs. The kernel does it by providing a software layer to manage the limited resources of a computer.

#### Tasks performed by the Kernel
- **Process Scheduling**: Linux is a *preemptive multitasking* operating system.
**Important Note: A process is a running program that is assigned resources. i.e. CPU, memory and file system**

- **Memory Management**: Linux employs virtual memory management.
  - Process are isolated from one another and from the kernel.
  - Only part of a process need to kept in memory.
- Provision of a file system.
- Creation and termination of processes.
- Access to devices.
- **Networking**: The kernel transmit and receive network messages(packets) on behalf of user process. This task includes routing of network packets to the target system.
- **Provision of a system call application programming interface (API)**: Process can request the kernel to perform various tasks using kernel entry points known as *system calls*.

#### Kernel mode and user mode
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
Each user in the system is uniquely identified and users may belong to groups.
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
For the purpose of accessing a file, the system divides users into three catego- ries: the owner of the file (sometimes termed the user of the file), users who are members of the group matching the file’s group ID ( group), and the rest of the world (other). Three permission bits may be set for each of these categories of user (making a total of nine permission bits): read permission allows the contents of the file to be read; write permission allows modification of the contents of the file; and execute permission allows execution of the file, which is either a program or a script to be processed by some interpreter (usually, but not always, one of the shells).
These permissions may also be set on directories, although their meanings are slightly different: read permission allows the contents of (i.e., the filenames in) the directory to be listed; write permission allows the contents of the directory to be changed (i.e., filenames can be added, removed, and changed); **and execute (some- times called search)** permission allows access to files within the directory (subject to the permissions on the files themselves).

## File I/O Model