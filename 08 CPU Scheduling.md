# Fundamental concepts
One of the main targets of multiprogramming is to maximize the use of the CPU resource. To reach this target, more than one task (i.e. process thread) is assigned to each progam:
- The scheduler must implement the better scheduling algorithm for the assign of the CPU to a task 
- Cost functions evaluate scheduler performance
- Different applications require different algorithms and cost functions

# Algorithm
The general scheduling procedure is:
- CPU is assigned to a task
- Each time a process enters a waiting state, terminates, an interrupt is received, etc., it is necessary to perform a context switching operation
- For each context switching:
	- The task in the running state is moved in the ready queue
	- A task in the ready queue is moved in the running state

# CPU scheduler
Several types exist:
- Short-term scheduler
	- Selects the process to which assign the CPU within the set of processes ready for execution in the main memory
	- Is executed very frequently
	- Is executed with timings in the order of milliseconds after an interrupt due to a timer or an I/O operation
	- Must be very fast
![[Pasted image 20251215103227.png|500]]

- Medium-term scheduler
	- Moves processes from the main memory to the second memory and vice versa
		- Selects which process to insert in the ready list 
	- Is executed less frequently
		- Is executed with timings in the order of seconds
	- In practice, it controls the number of processes in RAM
![[Pasted image 20251215103214.png|550]]

- Long-term scheduler
	- Also called job scheduler or admission scheduler selects the programs the system wishes to process based on its resources
	- Is executed much less frequently
		- Is executed with timings in the order of minutes
	- It basically controls the degree of multiprogramming
![[Pasted image 20251215103314.png|550]]

The scheduler manages the processes waiting for a device through (process) queue: 
- There are several queues, one per device
- Each queue is a linked list
![[Pasted image 20251215104124.png|580]]

# Queuing diagram
The queuing diagram shows the possible process transitions from one queue to another one:
- each rectangle represents a queue
![[Pasted image 20260213232523.png|550]]

# Important algorithms
![[Pasted image 20260214004845.png|500]]
![[Pasted image 20260214005131.png|500]]
## FCFS
Algorithm
The CPU is assigned to the tasks following the order in which they requested it:
- Tasks are managed through a FIFO queue
	- A new task is inserted in the queue tail
	- A task to serve is extracted from the queue head
- Scheduling can be sketched by means of a Gantt diagram
	- Bar chart showing the planning (start and end times) of the activities (Remeber that no task is interrupted, i.e., the CPU can only be released voluntarily)
![[Pasted image 20260214005639.png|400]]
![[Pasted image 20260214005701.png|400]]
- Advantages
	- Easy to understand
	- Easy to implement
- Disadvantages
	- Waiting times
		- Relatively long
		- Variables and not optimal
	- Unsuitable for real-time systems (no preemption)
	- Queue effect
		- Short tasks queued after long tasks, wait for a long time uselessly

## SJF
Algorithm
- To each task is associated the duration of the next CPU request (next CPU burst)
- The tasks are scheduled in order of duration of their next request
	- Scheduling in order of length
	- In case of ex-aequo (i.e., tasks with the same length) the FCFS scheduling is applied
![[Pasted image 20260214005819.png|400]]
- Advantages
	- It can be demonstrated that SJF is an optimal algorithm, using the waiting time as a criterion
		- By moving the short processes before the long ones, the waiting time of the first decreases more than the increase of the waiting time of the seconds
- Disadvantages
	- Possible starvation
	- Difficult of application, due to the impossibility to know a priori the future behavior of the task
		- Next burst time is unknown
		- It is possible to estimate this time using different methods (e.g., the exponential average)

Exponential avarage: $$τ_{n+1} = α⋅t_n + (1 - α) ⋅ τ_n$$
Where:
$τ_{n+1}$: Expected value for the next burst
$α⋅t_n$: (Real) duration of the n-th burst
$α = [0, 1]$: controls the relative weight recent vs past history
$$α = 0 \rightarrow τ_{n+1} = τ$$ $$α = 1 \rightarrow τ_{n+1}= t_n$$
Proceeding by substitution:
$$τ_{n+1}=α⋅t_n+(1-α)⋅α⋅t_n-1+.. +(1-α)^j⋅α⋅t_{n-j}+...+ (1-α)^{n+1}⋅ τ_0$$
Since both $α$ and $1-α$ are minor than 1, older terms weight less

## PS
- A priority associated to each task
	- Priority is typically represented with integer number
	- The higher the priority the smaller the integer number
	- Priorities can be determined based on
		- Internal criteria: used memory, number of used files, etc.
		- External criteria: owner of the task, etc.
- CPU is allocated to the task with higher priority
	- PS = SJF with the duration of the CPU burst substituted with the priority
![[Pasted image 20260214011010.png|400]]
- Drawbacks
	- Possible starvation
		- In highly loaded systems, tasks with low priority can wait forever
			- MIT: IBM stopped in 1973 with a process queued since 1967
		- A possible solution to starvation is aging of tasks
			- The tasks priority is gradually increase over time

## RR
Round Robin or circular scheduling. Is a version of FCFS with preemption.
- Algorithm
	- The CPU usage is divided into "time quantum" (i.e., discrete temporal intervals)
	- Each task can use the CPU for a maximum time equal to the quantum, and then it is inserted again in the ready queue
	- The ready queue is managed using a FIFO policy
		- New processes are inserted in the ready queue
- Designed specifically for time sharing (and some basic real-time systems)
![[Pasted image 20260214011218.png|400]]
![[Pasted image 20260214011228.png|400]]
- Drawbacks
	- The average waiting time is relativelly long
	- Substantial dependence of performance on the length of the quantum
		- Quantum long: RR degenerates into FCFS
		- Quantum short: to much context switching are performed, and switching/management times are very high (if compared with useful work)

## SRTF
Version of SJF with preemption
- Algorithm
	- It proceeds with a scheduling of type SJF, but
	- if a task with smaller burst time (than the running one) is submitted, the CPU is preempted in favor of the new task
- Similar characteristics of the SJF scheduler
![[Pasted image 20260214011418.png|400]]
- Advantages
	- Short tasks are handled very quickly
	- Because the task with the least remaining time is executed and its remaining time can only decrease, context switching occurs only when a new processes arrive
	- The overhead required by the algorithm is minimal
- Drawbacks
	- Like SJF, it requires an accurate estimates of the execution time
	- Like SJF suffers of starvation


## MQS
Applied to situations where tasks can be classified into different groups (Foreground, background, system, etc.)
- Algorithm
	- The ready queue is divided into different queues
	- Each queue can be managed with its own scheduling algorithm
	- It can be modified to allow the transfer of tasks between the various queues
		- MQS with feedback
![[Pasted image 20260214011538.png|200]]

**Considerazioni aggiuntive:**
- Lo scheduler è un task che deve essere schedulato in maniera simile agli altri task
	- Nello scheduling senza prelazione
		- Lo scheduler è invocato ogni volta che un programma termina o lascia il controllo
	- Nello scheduling con prelazione
		- Lo scheduler è invocato periodicamente da un interrupt periodico della CPU
		- Gli altri task non possono prevenire questo procedimento
- Scheduling can be performed at the process or thread level
	- If the OS allows the use of threads, the scheduling is normally performed at the threads level (processes are not taken into account)
- Threads scheduling
	- The SO takes into account only T at kernel level, and it ignores T at user level (which are managed through a library)
	- As a consequence, the scheduling can be performed only for T at kernel level (if they exist)
- Scheduling for multiprocessors systems
	- All previous examples have been made assuming 	the existence of a single CPU
	- In the case of more than one CPU, load can be 	shared
	- The load balance is automatic for OS with waiting queues common to all processors
	- There are several schemes
		- Asymmetric multi-processing: a master processor	distribute the load among slave processors
		- Symmetric multi-processing: each processor provides for its own scheduling
- Scheduling for real-time systems
	- They try to respond in real-time and within predefined deadline to events
		- Events (e.g., raise of a signal and subsequent interrupt) guide the scheduling
		- Latency is defined as the time elapsing between the occurrence of an event and its management
	- There are two types of real-time systems
		- Soft real-time
			- They give priority to critical processes, but do not guarantee response times (only probabilistic guarrantees)
		- Hard real-time
			- The execution of the tasks is guaranteed within a maximum time limit (deadline)