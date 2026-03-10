### Distributed Memory Programming Model
(Message Passing)

- Processes/tasks (P) have their own local memory
	- Multiple tasks can reside on the same physical machine or across multiple
- Tasks exchange data with "Send" and "receive"
	- Send must have a receive
- MPI is standard for message passing

## Shared Memory

- Thread-based programming
- Data can be shared or private to a thread
	- Shared: all have access
	- Mostly implicit synchronization

### Execution Model

- Pragma keyword used for specifying shared
![[Pasted image 20260213133449.png]]

- Slurm script specifies the num of threads

#### Hello World Ex:

![[Pasted image 20260216130040.png]]

- Fork, each thread gets tid
- Only master prints

#### Slurm Script:
![[Pasted image 20260216130223.png]]

- In this case, one node and one rank. You could have multi-parallelism --> Multiple ranks and multiple threads per each
- To compile, use `gcc -fopenmp hello.c`

![[Pasted image 20260216130117.png]]

- global tid becomes 1000, but privately (in parallel copy, it becomes 0)
- After going back to master thread, it will go back and print the global value (1000)
- SharedV is the last tid to execute

### OpenMP Directives

- Directive applies to succeeding block or OpenMP construct
- ALL start with **#pragma omp**

	- Data environment clauses
		- Shared, private, ...
	- Parallel regions
	- Work Sharing
	- Synchronization
		- barrier, single/master, critical/atomic

### Parallel Regions Directive

![[Pasted image 20260216132302.png]]

![[Pasted image 20260216132316.png]]

#### Clauses

- Shared() and private(). You can specify which variables are parallel or shared
- `#pragma omp for` --> Shared N, OpenMP figures out how to split between threads

### Implicit Data Sharing Rules

- Shared variable: only one instance
- Private: each thread has local copy
- Implicit rules:
	- Variables declared outside parallel region are shared, except loop iteration variables
	- Variables inside parallel region are parallel

### Explicit Data Sharing Rules

- Use shared and private clauses in `#pragma omp parallel`
- Recommended you specify this for clarity
- Declare private variables inside parallel regions whenever possible


## Work Sharing
### "Omp for" Sharing Directive

- Distribute loop iterations over threads (unroll the loop)
- ![[Pasted image 20260216133104.png]]

#### Example

![[Pasted image 20260216133327.png]]

![[Pasted image 20260216133440.png]]

- Default: chunk = N/#threads, and static
- Can also do scheduling kind, either static or dynamic
- Static does in order for all threads

#### Dynamic

- First come first serve, faster thread continues getting loop iterations. Doesn't wait for some other thread to work