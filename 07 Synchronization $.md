## Concurrency and Synchronization
Development environment
- concurrent programming (using Processes or Threads)
- cooperating processes or threads

Issues:
- Need to manipulate shared data
- Race conditions may arise
- There may be sections of not-reentrant code (non interruptible)

Solution strategy
- appropriately synchronize P or T, to make their results not dependent on their relative speed

Example:
![[Pasted image 20260211165155.png|400]]

## LIFO - Stack
![[Pasted image 20260211165836.png|170]]
```c
void push (int val) {
	if(top>=SIZE) return;
	stack[top] = val;
	top++;
	return;
}

int pop (int *val) {
	if(top<=0) return;
	top--;
	*val=stack[top];
	return;
}
```
push and pop
- Operate on the same end of the stack
- Variable `top` is shared

The problem is that we can overwrite a value or make a pop of  a nonexistent value.
## Circular buffer
![[Pasted image 20251124102003.png|200]]
```c
void enqueue (int val) {
	if (n>SIZE)
		return;
	queue[tail] = val;
	tail=(tail+1)%SIZE;
	n++;
	return;
}
```
```c
int dequeue (int *val) {
	if (n<=0) 
		return;
	*val=queue[head];
	head=(head+1)%SIZE;
	n--;
	return;
}
```
enqueue and dequeue
- Operate on the different ends of the queue, using two variables tail and head
- Variable `n` is still shared

The problem is that increments and decrements of `n` can be lost
## Critical section
The above examples are examples of critical sections in programs.
A Critical Section (CS) or Critical Region (CR) section of code, common to multiple processes (or Threads), in which they can access (read and write) shared objects. 
In other words it is a section of code in which multiple processes (or threads) are competing for the use of shared resources.

If the conditions for a race condition (Sharing, Writing, Concurrency) are not present, then there is no Critical Section

Race conditions could be prevented if:
- No P (or T) executes in the same CS simultaneously
- No other P (or T) can execute, when a P (or T) executes in the CS
- The code in the CS is executed by a single P ((or T)) at a time
- The code in the CS is executed in mutual exclusion

Solution:
Establish an access protocol that enforces mutual exclusion for each CS.

Entering a CS, a thread executes a "resertvation" code:
- The reservation code must block the P (or T) if another P (or T) is using its CS.

Leaviging its CS, a P (or T) executes a code to release the CS
- The release possibly unlocks another P (or T) which was waiting in the "reservation" code of its CS

### Access protocol
```c
while (TRUE) {
	...
	reservation code
	Critical Section
	release code
	...
	non critical section
}
```

Every CS is protected by an
- enter code (reservation, or prologue)
- exit code (release, or epilogue)

Remember that non-critical sections should not be protected

### Conditions
Each solution to the CS probkem must match the followiing requirements.:
- Mutual exclusion
	- Only one P (or T) at a time must gain access to the CS
- Progress
	- If no P (or T) is in the CS, and a P (or T) wants to enter, it must be able to do it in a defined time
		- Only the P (or T) in the reservation phase can participate to the selection
		- No P (or T) outside the CS can block other P (or T)
	- That is, deadlock between P (or T) must be avoided
- Defined wait
	- Tehre must be a maximum number of times in which other P (or T) can access the CS, before a specific P (or T) can access
	- That is we must avoid stavation of P (or T)
- Each solution should be symmetrical 
	- The selection of the P (or T) that must access the CS should not depende on
		- Relative priority between P (or T)
		- Relative speed between P (or T)

### Solutions
- Software functions
	- Solutions without special CPU instructions, which depend on the logic of an algorithm
- Hardware
	- Solutions based on special hardware characteristics, or special (atomic) CPU instructions
- System calls
	- The kernel provides the data structures, and the related system calls, that the programmer can properly use for solving the mutual exclusion problem

# Software solutions
The software solutions to the CS problem are base on the use of shard (global) variables. This is available on systems with shared memory
We will analyze the solution with only two P (or T)
- They are named threads P$_i$ (T$_i$) and P$_j$ (T$_j$), $j=i-1$

The proposed solution is not easily extended to more than two threads.

## Mutual exclusion
### Sol1
```c
//Shared variables:
int flag[2]={FALSE, FALSE}

//P_i
while (TRUE) {
	while (flag[j]);
	flag[i] = TRUE;
	CS
	flag[i] = FALSE;
	non critical section
}

//P_j
while (TRUE) {
	while (flag[i]);
	flag[j] = TRUE;
	CS
	flag[j] = FALSE;
	non critical section
}
```
The problem is that both tasks can get passed the while at the same time (since `TRUE` isn't set yet), so both operate on the critical.
So the problems is that the lock variable is controlled and changed by two separate statements. So there is **no mutual exclusion**
### Sol2
```c
//Shared variables:
int flag[2]={FALSE, FALSE}

//P_i
while (TRUE) {
	flag[i] = TRUE;
	while (flag[j]);
	CS
	flag[i] = FALSE;
	non critical section
}

//P_j
while (TRUE) {
	flag[j] = TRUE;
	while (flag[i]);
	CS
	flag[j] = FALSE;
	non critical section
}
```

There is the chance of getting a **livelock** (deadlock), since both threads can set their flag to `TRUE` and wait forever

### Sol3
```c
# Shared variables
int turn =i //or int turn =j

//P_i
while (TRUE) {
	while (turn!=i);
	CS
	turn = j;
	non critical section
}

// P_j
while (TRUE) {
	while (turn!=j);
	CS
	turn = i;
	non critical section
}
```
The problem here is undefined wait:
- P$_i$ and P$_j$ acces their CS only alternatively, so if P$_i$ has no interest in using its CS, P$_j$  cannot enter its CS (**starvation**)

### Sol4
```c
// Shared variables
int turn = i;
int flag[2] = {FALSE, FALSE};

//P_i 
while (TRUE) {
	flag[i] = TRUE;
	turn = j;
	while (flag[j] && turn==j);
	CS
	flag[i] = FALSE;
	non critical section
}

//P_j
while (TRUE) {
	flag[j] = TRUE;
	turn = i;
	while (flag[i] && turn==i);
	CS
	flag[j] = FALSE;
	non critical section
}

```
All conditions met

# Hardware solutions
Hardware solutions to the CS problem can be classifired as follows:
- Soluions for systems that do not allow preemption
- SOlutions for systems that allow preemption
	- Solutions based on interrupts management
	- Solutions based on an "extension" of software solutions, or based on
		- Some kind of lock
		- Some kind of atomic instruction

## Systems without preemption
In mono-processor systems without  preemption
- The CS problem does not exist, because only a P (or T) can use the only CPU at a certain time, and this P (or T) cannot be interrupted

However this situation rarely occurs because: 
- Systems are often multi-processor or multicore, and even without preemption the parallelism is effective: i.e., distinct processors or cores can concurrently execute more than one P (or T)
- Kernels without preemption are not secure, have excessive response times, and are not suitable for "real-time"

## Systems with preemption
In a system with preemption
- A running process can be interrupted
- As a matter of fact, the operating system or the arrive of an interrupt changes/preempts the control flow to another process
- The original process will be terminated later

In mono-processor system with preemption:
It is possible to solve CS problem with interrupts
- Disable interrupts in the reservation section
- Enable interrupts in the release section
	- Used only inside the kernel, and for short sections
	- In multi-processor (multi-core) the interrupts must be disabled on all processors

```c
while (TRUE) {
	disable interrupt
	CS
	enable interrupt
	non critical section
}
```

### Using the interrupt mechanism
In general, disabling interrupts has several disadvantages:
- The procedure is inherently insecure
	- What happens if to a user process is given the right to disable interrupts, and that process has an incorrect behavior?
	- This opportunity can be provided only to kernel level processes (super-user)
- In multi-processor (multi-core) systems it is necessary to disable the interrupt on all processors
	- The interrupt disabling request must be sent
	- Long processing times are needed
	- System management becomes non real-time

### Using lock-unlock mechanisms
An alternative strategy is to simplify the software solutions, using locking mechanisms supported by the hardware.
A lock can be uses to protect a CS.
The lock value allows or prohibits access to the CS.
It must be an "atomic" indivisible instruction executed in a single "memory cycle", which
- Cannot be interrupted
- Allows testing and simultaneous setting of a shared variable

Two main atomic lock instructions exist
- `char TestAndSet(char *lock);`
	- Sets to one and returns the previous value of a shared lock variable
	- Executed in a single indivisible cycle
- `void swap (char *v1, char *v2);`
	- Swaps the content of two variables, one of which is a shared lock
	- Executed in a single indivisible cycle
```c
char TestAndSet (char *lock) {
	char val;
	val = *lock;
	*lock = TRUE;//Sets the lock to TRUE
	return val; //Returns the previous value of the lock
}
```

```c
void swap (char *v1, char *v2) {
	char *tmp;
	*tmp = *v1;
	*v1 = *v2;
	*v2 = *tmp;
	return;
}
```

### Use
```c
char lock=FALSE;
while (TRUE) {
	while (TestAndSet (&lock)); // lock, if lock==TURE the CS is busy thus wait, if lock=FALSE, set lock=TRUE and enter CS
	CS
	lock = FALSE; // unlock
	Non critical section
}
```
The disadvantage is that the while consumes CPU cycles while it waits on the permissoin to enter CS

```c
char lock=FALSE;
while (TRUE) {
	key = TRUE; //reserves the CS
	while (key==TRUE)
		swap (&lock, &key); // Lock, If lock==FALSE the CS is free, set key=FALSE, lock=TRUE, and enter the CS
	CS
	lock = FALSE; // Unlock
	non critical section
}
```
The disadvantage is still the spin-lock as above

## Mutual exclusion without starvation
The previous techiniques:
- ensure mutual exclusion 
- ensure progress, avoiding the deadlock
- They do not ensure the definire waiting for a process, or they do not guarantee non-starvation
- Are symmetric

To avoid starvation:
- Previous solution must be extended

```c
while (TRUE) {
	waiting[i] = TRUE;
	while (waiting[i] && TestAndSet (&lock));
	waiting[i] = FALSE;
	CS
	j = (i+1) % N;
	while ((j!=i) && (waiting[j]==FALSE))
		j = (j+1) % N;
	if (j==i)
		lock = FALSE;
	else
		waiting[j] = FALSE;
	non critical section
}
```

## Conclusion on hardware solutions
Advantages: 
- Can be used in multi-processor environments
- Easily extensible to N threads
- Easy to use from the software/user point of view
- Symmetric

Disadvantages:
- Not easy to implement at the hardware level
	- Need atomic operations on global variables
- Possible starvation
	- The selection of processes for entering the CS using busy-waiting is arbitrary, and managed by the processes and not by the OS
- Busy waiting on spin-lock
	- Waste of resources (i.e., CPU cycles) for waiting
		- In practice, busy-waiting is used only for very short waiting
- Priority inversion: a higher priority task is preempted by a lower priority task.
	- Consider two threads H and L, of high and low priority, respectively, accessing a resource in mutual exclusion.
	- L is in its CS, H is blocked outside until L exits its CS.
	- If a third thread M of medium priority becomes ready, it preempts L, thus L does not leave its CS promptly, causing H, the highest priority process, to remain blocked.
- A possible solution to this problem is to use the priority inheritance protocol
	- A process holding a lock automatically inherits the priority of the process with the higher priority waiting for the same lock


# Semaphores
The previous solutions are not satisfactory, because
- software solutions are complex to use from the point of view of the programmer
- hardware solutions are difficult to implement from the point of view of the hardware designer

OSs provide more appropriate primitives called semaphores

## Definition
A semaphore `S` is a shared structure including
- A counter
- A waiting queue, managed by the kernel
- Both protected by a lock
```c
typedef struct semaphore_tag {
	char lock; // Lock variable protects count
	// and queue management
	int cnt; // Counter
	process_t *head; // Thread list
} semaphore_t;
```
Operations on `S` are atomic
- Atomicity is managed by the OS
- It is impossible for two threads to perform simultaneous operations on the same semaphore

So a semaphore `S` is
- An integer shared variable
- Protected by the operating system
- Usable for mutual exclusion and synchronization


## Manipulation functions
Typical operations on a semaphore S:
`init (S, k)`, Defines and initializes the semaphore S to the value k
`wait (S)`, Allows (in the reservation code) to obtain the access of the CS protected by the semaphore S
`signal (S)`. Allows (in the release code) to release the CS protected by the semaphore S
`destroy (S)`,  Frees the semaphore S

### init (S, k)
Defines and initializes semaphore `S` to value `k`
Two types of semaphores:
- Binary semaphores
	- The value of `k` is only 0 or 1
- Counting semaphores
	- The value of `k` is non negative

```c
init (S, k) {
	alloc (S);
	S=k;
}
```

### wait (S)
- If the counter value of `S` is negative or zero blocks the calling T/P
	- If `S` is negative, its absolute value |`S`| indicates the number of waiting threads
- Not to be confused with the wait system call used to wait for a child process

The counter is decremented at each call
```c
wait (S) {
	if (S==0) 
		block();
	else S--;
}
```

### signal (S)utilizzo
- Increases the semaphore `S`
	- If S counter is negative or zero some T/P was blocked on the semaphore queue, and it can be wakeup
- Originally called V(), from the Dutch language "verhogen", i.e., "to increment"
- Not to be confused with system call signal that is used to declare a signal handler
```c
signal (S) {
	if (blocked())
		wakeup();
	else S++;
}
```

### destroy (S)
Release semaphore `S` memory:
Actual implementations of a semaphore require much more of a simple global variable to define a semaphore. This function is often not used in the examples
```c
destroy (S) {
	free (S);
}
```


## Mutual exclusion with semaphores
![[Pasted image 20251124131430.png|400]]
![[Pasted image 20251124131620.png|400]]

## Synchronization with semaphores
The use of semaphores is not limited to the Critical Section access protocol. THey can be used to solve any synchronization problem using:
- An appropriate positioning of semaphores in the code
- Possibly, more than one semaphore
- Possibly, additional shared variables

### Example 1
**Pure synchronization**
Obtain a specific order of execution:
- T$_i$ executes code A before T$_j$ exectures code B
![[Pasted image 20260213174819.png|400]]

### Example 2
**Pure synchronization**
Synchronize two T/P so that
 T$_j$ waits T$_i$
 then, Ti waits T$_j$ 
 It is a client-server schema
![[Pasted image 20260213175336.png|400]]
### Example 3
**Pure synchronization**
Implement this precedence graph
![[Pasted image 20260213175459.png|400]]
### Example 4
**Pure synchronization**
Implement this precedence graph
![[Pasted image 20260213175535.png|400]]

### Errors Examples
![[Pasted image 20260213175842.png|400]]![[Pasted image 20260213175909.png|400]]
![[Pasted image 20260213175945.png|400]]![[Pasted image 20260213175956.png|400]]
![[Pasted image 20260213180002.png|400]]![[Pasted image 20260213180007.png|400]]

Implementation of a semaphore