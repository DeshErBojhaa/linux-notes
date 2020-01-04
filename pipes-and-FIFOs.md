## Overview
Intendent pipe behaviour is to minic a **unidirectional** stream of bytes.
For pipes and FIFOs, boundaries between multiple message are not preserved. This means that when multiple message are being delivered to a single process, then the server and receiver must agree on some convention for seperating the messages. Various approachers are:
- Terminaring each message with a *delimiter character* (such as newline)
- Include a fixed sized header with a length field.
- Use fixed length meaasges.

![pipes](https://user-images.githubusercontent.com/46394832/71670393-e09f3d80-2d99-11ea-9c43-910538ea6e1d.gif)

### A pipe is a stream of byte
There is no concept of message or message boundaries when using a pipe. The  process reading from a pipe can read blocks of data of any size, regardless of the size of the block. Data in pipes are synchronized. It's **not possible** to randomly access the data in pipe using `lseek()`.

### Reading from a pipe
Reading on a empty pipe is blocking. Reading on a closed pipe will see and *end-of-file*. i.e. `read()` returns 0 once it has read all remainging data in the pipe.

### Pipes are unidirectional
One end of the pipe is used for writing and the other end is used for reading. If we need bidirectional communication we better use domain stream socket pairs (created using the `socketpair()` system call.)

### Pipes have limited capacity
A pipe is simply a buffer maintained in kernel memory. The buffer has a maximum capacity. Once a pipe is full, write operations blocks until reader removes some data from the pipe.
Since Linux 2.6.11 the pipe capacity is 65,536 bytes.
In theory, there is no reason why a pipe couldn’t operate with smaller capacities, even with a single-byte buffer. The reason for employing large buffer sizes is efficiency: each time a writer fills the pipe, the kernel must perform a context switch to allow the reader to be scheduled so that it can empty some data from the pipe. Employing a larger buffer size means that fewer context switches are required.

## Creating and Using Pipes
The `pipe()` system call creates a new pipe. A successful call to `pipe()` returns two open file descriptor in the array fileds: one for the read end of the pipe and `fields[0]` and one for write end `fields[1]`.
As with any file descriptor, we can use the `read()` and `write()` systemcall to perform I/O on the pipe.
The `read()` call returns `min(numberof bytes requested, number of bytes currently available)`.

Normally pipes are used to communicate between different processes. To connect two process with pipes, we first call the `fork()` followed by a `pipe()` call. During the `fork()` call, the child process inherits the parent process's pipes file descriptor. Then the parent process close the read descriptor of the pipe (`fields[0]`) and the child process closes the write file descriptor (`fields[1]`) of the pipe.

### Pipe allow communication between related processes
A related process are two process that share one common ancestor. For example, a pipe could be used for communication between a process and its grandchild. The first process creates the pipe, and then forks a child that in turn forks to yield the grandchild. A common scenario is that a pipe is used for communication between two siblings their parent creates the pipe, and then creates the two children. This is what the shell does when building a pipeline.

### Closign unused pipe file descriptors
Closing the proper file descriptor (read descriptor for write end and write descriptor for read end) is necessary. If we don't close those descriptors we may run into the limit of open descriptors. Also it is necessary for the pupe to work accurately.
The process reading from the pipe closes its write descriptor for the pipe, so that, when the other process completes its output and closes its write descriptor, the reader sees end-of-file (once it has read any outstanding data in the pipe).
If the reading process doesn’t close the write end of the pipe, then, after the other process closes its write descriptor, the reader won’t see end-of-file, even after it has read all data from the pipe. Instead, a read() would block waiting for data, because the kernel knows that there is still at least one write descriptor open for the pipe.
The writing process closes its read descriptor for the pipe for a different reason. When a process tries to write to a pipe for which no process has an open read descriptor, the kernel sends the SIGPIPE signal to the writing process. By default, this signal kills a process. A process can instead arrange to catch or ignore this signal, in which case the write() on the pipe fails with the error EPIPE (broken pipe). Receiving the SIGPIPE signal or getting the EPIPE error is a useful indication about the status of the pipe, and this is why unused read descriptors for the pipe should be closed.
If the writing process doesn’t close the read end of the pipe, then, even after the other process closes the read end of the pipe, the writing process will still be able to write to the pipe. Eventually, the writing process will fill the pipe, and a further attempt to write will block indefinitely.
One final reason for closing unused file descriptors is that it is only after all file descriptors in all processes that refer to a pipe are closed that the pipe is destroyed and its resources released for reuse by other processes. At this point, any unread data in the pipe is lost.

## FIFOs
Semantically a FIFO is similar to a pipe. The principal difference is that a FIFO has a name within the file system and is opened in the same way as a regular file. This allows a FIFO to be used for communication between unrelated processes.
