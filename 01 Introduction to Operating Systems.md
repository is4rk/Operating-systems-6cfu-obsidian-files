# Components
An elaboration system is composed of the four following components:
1. Hardware:
	- Provides basic computing resources (CPU, memory, I/O devices)
2. OS:
	- controls and coordinates the use of the hardware among the various application programs for the various users
3. System and application programs
	- User services
4. Users:
	- People, machine, other computers
![[Pasted image 20250922110308.png]]

Its divided in two parts, kernal space and user space. The kernal space is made up of OS, kernel, drivers, hardware. User space is made up of users, applications and system programs. 
These two spaces are connected by system calls.
## Operating System
It's a software interface between user or application and hardware. 
The goal is to execute command and programs, make system friendly to users, and use and share hardware efficiently.
It can be considered a VM that manages and allocates available resources or a "program" that controls the execution of user programs and operations of I/O devices.

## Two views
It can be viewed top-down:
- Its an abstract manager of resources and information:
	- it abstracts information and resources
	- it allows to manage them in a simplifyed way
- Its also a software interface between system users and the hardware

From a bottom-up view:
- A program that controls:
	- Devices and operations on devices
	- The execution of users programs
- It can be considered a set of modules, each of which provides services to the user.

## Modules and Services
- **Command interpreter**
- **Process management**
- Main Memory Management
- Secondary Memory Management
- Management of I/O devices
- **File, and file system management**
- Implementation of protection mechanisms
- Network management, and distributed systems

(bold ones are anylized in the course)
![[Pasted image 20250922111101.png]]

### Command interpreter
- The user and OS communicate through an textual or graphical interface
- The user performs its tasks through a command interpreter (shell)
- The OS allows the user to
	- Manage processes
	- Manage main and secondary memory
	- Establish protection policies
	- Manage the network and external connections

### Process management
- A process (active unit) is a program (passive unit) in execution
- To run it requires resources
	- CPU, memory, devices, etc.
- The OS offers support for
	- Creating, suspending and deleting processes
	- Establishing communication mechanisms and synchronization among processes

### Main memory management 
- The data and instructions of a program must be in a region of main memory to allows a process to be executed![[Pasted image 20250928222244.png]]
- Logically, main memory is a vector of elements (words)
- The OS must
	- Manage the use of memory (which regions are used and which are free)
	- Decide which processes to allocate in memory, and which can be  eallocated
	- Optimize CPU access to memory

### I/O devices management
 - I/O devices cannot be managed directly by the users (complexity, driver, sharing, etc.)
- The OS must
	- Hide the details of a device to users by providing a uniform interface to the user
	- Providing read, write, control operations on devices

### File, and file system management
- Data on secondary memory are organized into one or more file systems, which contain directories and files
- The OS must
	- Create, read, write, remove files and directories
	- Establish appropriate access protection mechanisms for data privacy and sharing
	- Optimize R/W operations

### Implementation of protection mechanisms
- Protection indicates access control for users and processes to system resources
- The OS must
	- Define the access rights associated to users and resources
	- Distinguish between authorized and unauthorized use
	- Keep track of which users is using system resources

### Network and distributed systems management
- A network is a collection of processors that do not share memory and clock
- The nodes of the network are interconnected by communication paths
- The OS must
	- Grant access to system resources
	- Increase the performance and reliability of the computing system, and the amount of data that can be processed

## Terminology and basic concepts of OS
- Kernel, bootstrap, kernel protection, system call
- Login, shell
- Filesystem, filename, pathname, working directory,  home directory, root directory
- Program (sequential and concurrent), process, thread
- Pipe
- Deadlock, livelock, starvation, polling (busy waiting)

### Kernel
- Is the core of an OS
- It manages all system resources
	- In particular, it manages memory and processors
	- A program (or better module) always in execution
	- All other programs are system programs or applications
- There are different types of kernel
	- Modular kernel that is subdivided into levels
	- Microkernel that provides only basic functionalities
	- Monolithic kernel that provides functionalities through the device drivers (most common)
![[Pasted image 20250922113016.png]]
#### Monolithic kernels
The services are realized through a set of system calls made by separate modules but whose integration is very tight
- Drawbacks
	- A problem on a module can block the entire system
	- Adding a new hardware device involves adding its module to the kernel and recompile all the kernel (in modern kernel modules can be loaded at runtime)
- Advantages
	- The tight internal integration of the components is extremely efficient
- Common solution
	- Unix, Linux, Open VMS, XTS-400
![[Pasted image 20250922113128.png]]

#### Microkernel
It defines very small and simple software modules on top of the hardware that implement minimal services. It separates core service implementations from operating system operational structures.
- Drawbacks
	- Slow due to the high number of core services and context switching
-  Advantages
	-  Very stable
- Not common solution
	- March, L4, AmigaOS, Minix
![[Pasted image 20250922113252.png]]

#### Hybrid aproach
Intermediate approach between the previous two.  Microkernel with additional and "non- ssential" kernel- level code, which can be executed very quickly. It's a compromise adopted by many developers.
- Drawbacks
	- Performance (slightly worse) but comparable with monolithic kernels
-  Advantages
	- It integrates advantages of monolithic and microkernels
- Common solution
	- Windows NT, Netware, XNU Kernel di Mac OS X, Dragonfly BSD
![[Pasted image 20250922113438.png]]

#### Esokernel
Known as "vertical operating systems", it has Radically different approach to operating system design and a more direct access to hardware. They separate "protection from management".
Extremely small and compact, as their functionality is arbitrarily limited to resource protection and  multiplexing.
- Drawbacks
	- They involve more work in application development (libraries decrease this effort)
- Advantages
	- Less hardware abstraction
- Examples
	- Nemesis, ExOS
![[Pasted image 20250922113605.png]]

### Bootstrap
Bootstrap or booting program. 
Initialization program. Executes at power-on performing a proper check and initialization of the computer hardware, then it loads the kernel into main memory.
The bootstrap program is usually:
- Stored in ROM and EEPROM (firmware)
- Loaded at power-up or reboot

### Kernel protection
A mode bit is added to computer hardware to indicate the cur- Operating Systems classification, Linux installationrent mode: kernel (0) or user (1). When an interrupt, exception, or fault occurs, hardware switches to kernel mode. Privileged instructions, that can be issued only in kernel mode. 
![[Pasted image 20250922114110.png]]
Changing the content of a system register can only be done in kernel mode.
- Dual mode ensures that a user program cannot gain control of the computer in kernel mode
- Memory protection does not allow a user to write in kernel memory, e.g., store a new address in the interrupt vector. Load the memory protection registers is a privileged instruction
- Timer commonly used to implement time sharing
	- Load the timer is a privileged instruction

### System call
It is the interface with the services provided by the OS, i.e., it is the entry point of the OS. It's often implemented in assembler.
Often accessible with high level Application
Program Interface (API)
- Win32/64 API (for Windows)
- POSIX API (for UNIX, Linux, MAC OS X)
- Java API (for Java Virtual Machine)

How many system calls exist in an OS?
- UNIX 4.4 BSD: about 110
- Linux: between 240 and 260
- UNIX FreeBSD: about 320

The difference between system calls and functions are:
- Both provide services to users
-  Any system call is typically coupled with one or more functions with the same name of the system call, and defined with a high-level programming language (e.g., C)
- Functions can be substituted and modified, system calls are kept stable over the years
- System calls provide basic functionalities (and they have a non-complex prototype), functions inside libraries provide more elaborate functionalities

Most common calls:
- Process management
	- `fork`, `wait`, `exec`, `exit`, `kill`
- File management
	- `open`, `close`, `read`, `write`, `lseek`, `stat`
- Directory management
	- `mkdir`, `rmdir`, `link`, `unlink`, `mount`, `umount`, `chdir`, `chmod`

Example:
difference between windows and linux 
```POSIX
int read (int fd, void *buffer, size_t nbytes);
```

```Win32/64API
BOOL ReadFile (
	HANDLE fileHandle,
	LPVOID dataBuffer,
	DWORD numberOfByteToRead,
	LPDWORD numberOfByteRead,
	LPOVERLAPPED overlappedDataStructure
);
```

Example:
What happens when we make a system call
A system call causes an exception, and CPU switches to kernel mode (mode bit = 0)
- The exception, a software interrupts (or trap), activates the corresponding service routine
	- It verifies that the parameters are correct and legal, executes the request, and returns control to the instruction following the system call
![[Pasted image 20250922115300.png]]

Example:
`read(fd, buffer, nbytes)`
![[Pasted image 20250922115458.png]]
Example:
System calls versus library functions
- `printf` function uses system call `write`
- The allocation function `malloc` plausibly call the system call `sbrk`
- Data/time management
	- Only one system call `time`
	- The system call `time` provides the number of seconds since `01.01.1970`
	- Date and time are provided by different functions that produce different result formats

### Login
To login you must provide
- Username
- Password
	- Passwords were usually stored in /etc/passwd

### Shell
Its the command line interpreter, and its not directly part of the OS. It is a set of user space commands included in the OS distribution. 
It Reads user commands and executes them. The commands are typed on the terminal or read from a "script file"
There are several shells:
- Bourne shell (sh)
- Bourne againg shell (bash)
- tcsh, ksh, zsh, etc.

### File system
Hierarchical structure of directories and files.
![[Pasted image 20250922130006.png]]

### Filename
There are few composition rules and length limitations (i.e., typical 255 bytes). 
- In UNIX the only characters that cannot be used for a filename are: 
	- slash `/`
	- character `null`

### Pathname
A sequence of names separated by slashes ’/’
- `.` indicates the current directory
- `..` indicates the parent directory

### Home directory
- Directory that is accessed after login operation
- It contains files and directories of the user that performed the login
- Identified by tilde ~ in UNIX-like systems
	- The home directory of user foo is usually `/home/foo`, which corresponds to `~` for that user
- Origin point to interpret absolute pathnames

### Working directory
Origin point for interpreting relative pathnames
- Initially equal to the Home directory (i.e., ~)
- It can be changed by following the structure of the file system
- Owned by each process
	- i.e., each process has its own Working directory
- The reference to the Working directory is implicit, if any pathname is specified
	- directory/file1 is equal to ./directory/file1

### Program
executable file that resides on disk:
- Passive entity
- Specifies a set of operations to execute a defined task

### Sequential program
- Its operations are performed in sequence
- A new instruction starts at the end of the previous one (fetch - decode - execute)

### Concurrent or parallel program
- Several statements can be executed in parallel
- An operation can be performed without waiting for the completion of the previous one

### Atomic
An operation is atomic if it cannot be interrupted.
Why not always guarantee atomicity?
Atomicity is expensive to guarantee and slows down the entire computer
Example:
```c
x++;
```
which is translated to:
```
add BYTE PTR [0x20], 1
```
If, in the meanwhile, another processor executes the same operation, one of the two increments can be lost. Atomicity guarantees it wont be lost.

### Process
A running program (which includes a program counter, registers, variables, etc.). Is active entity. On UNIX systems, each process is characterized by a unique integer (positive) identifier
Process tree:
![[Pasted image 20250922131418.png]]

### Threads
They are also called light process. 
A process uses a set of resources. it can have one or more control streams running, each of these streames is a thread. Each thread, belong to a process, and shares its resources, but it has its own identifier, and "life".

### Pipe
A pipe allows a communication data flow to be established between two processes
Typically, the channel is half-duplex (mono-directional)
	● Communication in one direction from P1 to P2 or from P2 to P1
![[Pasted image 20250922131622.png]]

### Deadlock
A deadlock is a situation in which entities (processes) sharing the same resource wait indefinitely an event, which is caused by other entities, resulting that one or more entities are blocked forever. 
![[Pasted image 20250922131652.png]]

### Livelock 
Called also active deadlock. 
Situation similar to the deadlock in which the entities are not actually blocked but do not make any progress because they are too busy responding to each other to resume work.

Examples:
- Two people meeting in a corridor and trying to pass, repeatedly move from one side to the other of the corridor
- Two units perform polling (busy waiting) to check the status of the other and do not show progress (mutual livelock), but they are not in deadlock because each is doing the poll operation

### Starvation
Access to a resource needed for its progress is repeatedly refused to an entity. 
Starvation does not imply deadlock because while an entity may starve other can progress
Instead, deadlock imply starvation. No entity can progress, consequently all are in starvation

# 