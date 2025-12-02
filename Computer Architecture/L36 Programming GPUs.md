#GPUs 

#### Example (with pthreads)

Use SAXPY --> Y(i) = a * X(i) + Y(i)

![[Pasted image 20251201211001.png]]

worker function that implements worker logic

![[Pasted image 20251201211033.png]]

main function executes as a thread

- Allocate memory for threads
- Create thread, call worker method, and pass index to work on
- Join all threads (main thread will wait for all other threads to complete)

#### Parallel Problem Decomposition

- Assume very large computing problem that can benefit from parallel processing
- Programmer or compiler must decompose problem into many small problems
	- Small problems can be solved in parallel
- In multicore CPU, programmer explicitly defines threads and tasks that the thread must complete

### Programmer Model - GPU

- Global function that is executed by one thread is called a kernel
	- Function must be designed to be executed by many threads
- Set of concurrent threads that can execute the same thread program and may cooperate to compute a result is called a thread block
	- Cooperates using barrier synchronization and shared memory space
		- \_syncthreads()
- Grid: Set of thread block that execute the same kernel
- ![[Pasted image 20251201211742.png]]
- When kernel invoked, programer specifies number of threads per block, and number of blocks

### GPU Memory Model

![[Pasted image 20251201211839.png]]
- Thread block shares memory
- Global memory between grids
- Inter grid synchronization, wait for everything to complete before executing another kernel function

#### CUDA Example - SAXPY

![[Pasted image 20251201231715.png]]

global --> kernel code, memory is globally shared

256 threads per block
- Define blocks --> n is size of array --> one element per thread of array

- Launches n blocks -- assigns one thread to each element of X & Y
- Spins 256 threads per block
- Compare this to pthread code:
	- No need to create and manage threads
	- Loops transformed into parallel kernels

### CUDA Paradigm

- Thread blocks are dispatched to SMs
	- Each block threads execute on an SP in the SM
- Number of blocks dispatched to an SM depends on SM resources
- You can have multiple blocks assigned to the same SM
- ![[Pasted image 20251201232312.png]]
- Blocks not dispatched initially are dispatched when some blocks finish execution


### CUDA Paradigm - Continued

- Copy data from CPU memory to GPU memory
	- CPU starts the code
	- Need to copy kernel to GPU global memory, and data
- Launch the kernel
- Copy data from GPY memory to CPU Memory

![[Pasted image 20251201232715.png]]

