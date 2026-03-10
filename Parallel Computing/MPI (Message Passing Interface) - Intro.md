#MPI
- Examples we'll do Hello_world, send_receive, mat_mult
- Each process/tasks(P) use their own local memory during computation
	- Multiple tasks can reside on the same machine or across multiple
- Tasks can exchange data through send and receive messages
	- Send must have matching receive
	- Programmer handles parallelism and sync
- Industry standard for message passing
- MPI specs on mpi-forum.org

MPI: specfication for developers and users of message passing libraries

- Bindings have been defined for C and Fortran90
- MPI library implementations differ in version supported

Libraries:
- Open MPI: https://www.open-mpi.org/
- Open source, used by HiPerGator

## Basic MPI Programs

- `#include "mpi.h"`
- MPI_Init starts the program
- MPI_Finalize() ends MPI

Same MPI program executed concurrently on each process

SLURM Script to Run MPI Program

- MPI code specifies what each process (rank) does
- SLURM script specifies how the code is mapped onto the cluster

Sample:
![[Pasted image 20260121131042.png]]

### Hello World

![[Pasted image 20260121131905.png]]

Blue are MPI
- Int Rank gets populated with the rank
- Int size gets populated with the size (will be 8 for all of them)
- In this case, order is random between all processes

### MPI_Recv Example

Variables for rank, total ranks, source, destination, tag, message and MPI_Status status (status for MPI_Recv statements)

![[Pasted image 20260121132707.png]]

First statement to see who I am

- Rank 0 usually used as Master, rest workers
- If not 0, construct greeting message, then **MPI_Send**
- If 0, then for all ranks, receive the message, then print it. This will print in order

### MPI Broadcast example

Once you have data, send to all ranks

- MPI broadcasts are used often

## Matrix Multiply

![[Pasted image 20260128172613.png]]

- can use 3 nested loops for doing it in C

Note that:
	`C[i][j] = Sum from 0 to NCA-1(A) of (A[i][k] * B[k][j])`

Called "embarrassingly parallel"
- Use a master/workers style architecture
- Master rank performs coordination:
	- Distributes rows of Matrix A, "evenly" among worker ranks
	- Send all of Matrix B to each worker ranks
- Assume 8 ranks, 7 worker ranks perform a matrix computation:
	- On its own data to create partial C Matrix
	- Sends partial result back to master rank
- Matrix A: NRA 60; NCA 12
- Matrix B NRB: 12; NCB 10
- Avgrow = NRA / numworkers
		60/7 = 8
- extra = NRA % numworkers = 4
- 4 workers get 9 rows of A, 3 workers get 8 rows of A

Process for one rank:
![[Pasted image 20260128183331.png]]

Master sends chunk and all B, then worker sernds the result

#### Algorithmic Approach

![[Pasted image 20260129111929.png]]

- MPI_Send (S) and MPI_recv (R) need to be "paired"
- MPI_Send and MPI_recv are "blocking" function calls
- S-R pairs: coordinator sends S data (A*, B) to each worker R
- S-R pairs: Each worker rank sends S partial results (C*) back to coordinator (R)
- They run **the same code**

#### Code

- Declare variables, as well as matrices, for all
- Synthetically fills A and B
- Master sends data to all workers
- Receive results from worker tasks (notes offset to construct result matrix)
- Workers first receive all the data, compute, and send it back 

Master:
![[Pasted image 20260129112951.png]]

###  MPI Functions Parameters

MPI Send Parameters: 
![[Pasted image 20260129114353.png]]

Matrix Ex:
![[Pasted image 20260129114620.png]]

MPI Recv parameters

![[Pasted image 20260129114449.png]]

Master continued

![[Pasted image 20260201220812.png]]

Offset is sent back so master knows where to place results

NOTE:

- MPI_Status has the source rank stored