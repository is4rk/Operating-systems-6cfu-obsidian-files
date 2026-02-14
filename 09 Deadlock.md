# Deadlock
- Condition for deadlock
	- A P/T requires an unavailable resource, it enters a waiting state, and it waits forever
- Deadlock consists in
	- A set of P/T all awaiting the occurrence of an event that can only be caused by another process in the same set
- Deadlock implies starvation, not the opposite
	- The starvation of a P/T implies that this P/T waits indefinitely, but the other P/T can proceed in the usual way (without being in deadlock)
	- All P/T in deadlock are in starvation

## Deadlock problem
- A set of blocked processes each holding a resource and waiting to acquire a resource held by another process in the set.
	- Example: P$_1$ and P$_2$
		- each of them holds a pen drive and
		- needs another one.
- Solution with 2 semaphores A and B, initialized to 1

| P$_1$    | P$_2$   |
| -------- | ------- |
| wait (A) | wait(B) |
| wait (B) | wait(A) |

## Necessary conditions for occurrence of a deadlock
![[Pasted image 20260214013637.png|600]]

# Deadlock modeling
Resource allocation graph G = (V, E). It allows for deadlock description and analysis.

The set of vertices V is composed of processes and resources: 
- Process set P = {P$_1$, P$_2$, ...,P$_n$}
	- Processes are indistinguishable and in an indefinite number
	- Each process accesses a resource via a standard protocol consisting of
		- Request
		- Utilization
		- Release
- System resource set R = {R$_1$, R$_2$, …, Rm}
	- The resources are divided into classes (types)
	- Each resource type Rj has Wi instances
	- All instances of a class are identical: any instance satisfies a demand for that type of resource

The set of edges E is composed of
- Request edges
	- P$_i$ → R$_j$, i.e., from a process to a resource type
- Assignment edge
	- R$_j$ → P$_i$, i.e., from a resource to a process

![[Pasted image 20260214014459.png|500]]
- A resource allocation graph can be sometime simplified in a wait-for graph by
	- deleting the resource vertices
	- creating the edges between the remaining vertices
- Use and consideration similar to the resource allocation graph
![[Pasted image 20260214014542.png|400]]
- Sometimes it is useful to extend the resource-allocation graph to a claim graph by
	- adding a claim edge: P$_i$ -> R$_j$ , indicates that process P$_j$ can ask resource R$_j$ in the future
	- A claim arc is represented by dashed line 
	![[Pasted image 20260214014727.png|150]]
# Detection and recovery techniques
- The system is allowed to enter in a deadlock state, to then intervene.
- Algorithm in two steps
	- Deadlock detection (of deadlock condition)
		- The system performs a deadlock detection algorithm
	- Recovery from deadlock
		- If deadlock has been detected, a recovery action is performed

## Detection: strategies
- Given an allocation graph, deadlock can be detected by checking for cycles
	- If the graph contains no cycles, then there is no deadlock
	- If the graph contains one or more cycles then
		- Deadlock exist if each type of resource has a single instance
		- Deadlock is possible if the are several instances per resource type
			- The presence of cycles is necessary but not sufficient condition in the case of multiple instances per resource type (For multiple instances see the Banker’s Algorithm)

Examples:
- Processes
	- P$_1$, P$_2$, P$_3$
- Resources
	- R$_1$ and R$_2$ with a single instance
- A cycle exists
- Deadlock
	- P$_1$ waits for P$_2$
	- P$_2$ waits for P$_1$

![[Pasted image 20260214015611.png|200]]

- Processes
	- P1, P2, P3, P4
- Resources
	- R1 and R2 with two instances
- A cycle exists
- No deadlock
	- P2 and P4 can terminate
	- P1 can acquire R1 and terminate
	- P3 can acquire R2 and terminate
![[Pasted image 20260214015732.png|200]]

- Processes
	- P1, P2, P3
- Resources
	- R1 and R3 with an instance
	- R2 with two instances
	- R4 with three instances
- Two cycles exist
- Deadlock
	- P1 waits for R1
	- P2 waits for R3
	- P3 waits for R2
![[Pasted image 20260214015822.png|250]]

### Detection costs
- The detection phase has the high computational cost
	- An algorithm to detect a cycle in a graph is required
	- The presence of cycles can be verified by a visit in depth
	- A graph is acyclic if a visit in depth does not meet arcs labeled "backward" directed to gray vertices
		- If you reach a gray vertex, i.e., you cross a backward arc, you have a cycle
	- The computational cost of this operation is equal to
		- $Θ(|V|+|E|)$ for representations with adjacency list
		- $Θ(|V|^2)$ for representations with adjacency matrix
- When detection is performed?
	- Every time a process makes a request not immediately satisfied
	- At fixed time intervals, e.g., every 30 minutes
	- At variable intervals of time, e.g., when the CPU usage falls below a given threshold

# Recovery
- Different strategies are possible for deadlock recovery
	- Act on the vertex of allocation graphs
	- Act on the arches of allocation graph
![[Pasted image 20260214020228.png|500]]
![[Pasted image 20260214020244.png|500]]

# Conclusion on Detection and Recovery
- Detection and recovery operations are
	- logically complex
	- computationally expensive
- In any case, if a process requires many resources, starvation may occur
	- The same process is repeatedly chosen as the victim, incurring repeated rollbacks
		- To avoid starvation the victim selection algorithm should take into account the number of a process rollbacks

# Prevention techniques
Try to control how resources are requested to prevent the occurrence of at least one of the necessary conditions
- Mutual exclusion
- Hold and wait
- No preemption
- Circular wait
## Mutual exclusion
![[Pasted image 20260214020536.png|500]]

## Hold and wait
![[Pasted image 20260214020544.png|500]]

## No preemption
![[Pasted image 20260214022547.png|500]]
![[Pasted image 20260214022558.png|500]]
## Circular wait
![[Pasted image 20260214022617.png|500]]
Let F be the function that imposes a unique order among all classes of system resources R$_{i}$
- Let a process have previously requested an instance of R$_{old}$ resource, and now request a R$_{new}$ instance
- If F(R$_{new}$ ) > F(R$_{old}$ )
	- The resource is granted
- If F(R$_{new}$) ≤ F(R$_{old}$ )
	- The process must release all resources R$_{i}$ such that F (R$_{new}$) ≤ F(R$_{i}$) before getting an instance of R$_{new}$

- It can be shown that this condition is sufficient to avoid the circular wait
	- That is, if the resources are requested in a certain order, is it true that it is not possible to have a circular wait?
	- We proceed using a demonstration of type "reduction to absurdity", assuming there is a circular wait, i.e., supposing there is a set of processes that
		- They were requested in the specified order, e.g., in increasing numerical order
		- They are in circular wait
![[Pasted image 20260214023307.png|500]]

# Deadlock avoidance 
- The main algorithms
	- differ in the amount and type of information required
		- The simplest model imposes that all processes declare the maximum number of resources of each type that they will need
	- generally reduce the use of resources and the efficiency of the system
	- are based on the concept of safe state and safe sequence

## Safe state

| Safe state    | The system is able to<br>• Allocate the required resources to all processes<br>• Prevent the occurrence of a deadlock<br>• Find a safe sequence                                                                                                      |
| ------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Safe sequence | A sequence of process scheduling {P1, P2, ...,Pn} such that for each requests that could be performed by any Pi, the request can be satisfied by using the currently available resources and the other resources released by processes Pj with j < i |
![[Pasted image 20260214023649.png|500]]

## Joint progress of two processes
![[Pasted image 20260214023721.png|500]]

## Strategies
- To avoid a deadlock, one must ensure that the system remains always in a safe state
	- Initially the system is in a safe state
	- Each new resource request
		- will be granted immediately, if this allows the system to remain in a safe state
		- otherwise, granting the request will be delayed; the process that performed the request is forced to wait
- There are two classes of strategies
	- For resources having unitary instances
	- For resources having multiple instances


## Algorithm for resources with a single instance
- Based on the determination of cycles, using the claim-for graph
	- All requests must be a priori declared
	- they are represented by claim arcs
- At a time a request is performed
	- the corresponding claim arc is transformed into an assignment arc
	- Before the request is satisfied, the algorithm verify the presence of cycles
	- If no cycle is present, the conversion of the arc is performed and the resource assigned
	- Otherwise, the assignment of the requested resource would bring the system into an unsafe state. For this reason it is postponed
- Each time a resource is released
	- the assignment arc is transformed into a claim arc (to manage any subsequent request)
![[Pasted image 20260214023837.png|400]]

## Algorithm for resources with a multpiple instance
- Verify the state of the system to understand if the available resources are sufficient to complete all processes based on
	- the number of resources available to the system
	- number of resources allocated, and
	- max number of resource that the process may need 
- Each process
	- must declare in advance its maximum number of resources it may need
	- when it requests a resource, it can be blocked for a limited amount of time
	- must guarantee to return an allocated resource in a finite amount of time
- Banker's Algorithm (Dijkstra, \[1965])
	- It consists of two parts
		- Verifies that the current state is safe
		- Verifies whether the new request can be immediately granted allowing to system to remain in	a safe state
			- Simulates assigning the resource, and controls that a sequence of assignments exists that allows the system to satisfy all requests, possibly delaying the  delivery of the resources for some of the requests.
- The algorithm uses the data structures listed in the following slide
![[Pasted image 20260214024116.png|500]]

Example:
![[Pasted image 20260214024356.png|400]]
![[Pasted image 20260214024401.png|400]]
![[Pasted image 20260214024408.png|400]]
![[Pasted image 20260214024423.png|400]]
## Banker's algorithm
Verify whether a state is safe or unsafe

1.
```
∀i∀j need[i][j]= max[i][j] - allocation[i][j]
∀i finish[i]=false
```
2.
```
Find a process Pi such that finish[i]=false AND ∀j need[i][j] <= available[j]
If no such i is found goto step 4
```
3.
```
∀j available[j] += allocation[i][j]
finish[i]=true
goto step 2
```
4.
```
if ∀i finish[i]=true then system is in a safe state
```

- Complexity is
	- $O (m ⋅ n^2) = O (|R| ⋅ |P|^2)$
- It is also based on unrealistic assumptions
	- Processes must specify their demands in advance
		- The necessary resources are not always known
		- Also it is not known when a resource will be used
	- Assumes that the number of resources is constant
		- Resources may increase or decrease due to transient 	or continuous failures
	- It requires a fixed population of processes
		- The number of active processes in the system 	increases and decreases dynamically