# Fundamental concepts
One of the main targets of multiprogramming is to maximize the use of the CPU resource. To reach this target, more than one task (i.e. process thread) is assigned to each progam:
- The scheduler must implement the better scheduling algorithm for the assign of the CPU to a task 
- Cos functions evaluate scheduler performance

# Algorithm
FROM u04
The general scheduling procedure is:
- CPU is assigned to a task
- Each time a process enters a waiting state, terminates, an interrupt is received, etc., it is necessary to perform a context switching operation
- For each context switching:
	- The task in the running state is moved in the ready queue
	- A task in the ready queue is moved in the running state

# CPU scheduler
Several types existL
- Short-term scheduler
	- Selects the process to which assign the CPU within the set of processes ready for execution in the main memory
	 Is executed very frequently
	 Is executed with timings in the order of milliseconds after an interrupt due to a timer or an I/O operation
	 Must be very fast
![[Pasted image 20251215103227.png|500]]

 Medium-term scheduler
 Moves processes from the main memory to the second memory and vice versa
	● Selects which process to insert in the ready list 
 Is executed less frequently
	● Is executed with timings in the order of seconds
 In practice, it controls the number of processes in RAM
![[Pasted image 20251215103214.png|550]]

 Long-term scheduler
 Also called job scheduler or admission scheduler selects the programs the system wishes to process based on its resources
 Is executed much less frequently
	● Is executed with timings in the order of minutes
 It basically controls the degree of multiprogramming
![[Pasted image 20251215103314.png|550]]

The scheduler manages the processes waiting for a device through (process) queue: 
- There are several queues, one per device
- Each queue is a linked list
![[Pasted image 20251215104124.png|580]]

# Queuing diagram