
MPI Status is a data structure that can be queried. Useful when message received with MPI_ANY_TAG or MPI_ANY_SOURCE

- Replacement for point-to-point, for simplicity and efficiency
- Every rank executes

- Collector operation involves all processes in communicator:
	- Data movement
	- Collective computation
	- Synchronization

### Basic Collectives

![[Pasted image 20260209165323.png]]

- MPI_Bcast: distribute same data from one process to all others in communicator
- MPI_Scatter: dsitribute different data from one process to all others in a communicator
- MPI_Gather: Gathers together values from group of processes in a commuinicator
- MPI_Reduce: Combines data from all processes in communicator and returns to one process

#### Broadcast

`int MPI_Bcast(void *buffer, int count, MPI_Datatype datatype, int root, MPI_Comm comm)`

![[Pasted image 20260209165823.png]]

If I call myself the root, I broadcast

#### Reduce

`int MPI_Reduce (void *sendbuf, void *result, int count, MPI_Datatype datatype, MPI_Op op, int target, MPI_Comm comm)`

- All ranks send to target rank

Ex:

![[Pasted image 20260209174605.png]]

Can also do with arrays

All ops:
![[Pasted image 20260209174738.png]]

#### Scatter

- Sends from root to all ranks
- `MPI_Scatter ( void *sendbuf, int send_count, MPI_Datatype send_datatype, void *resultbuf, int recv_count, MPI_Datatype recv_datatype, int source, MPI_Comm comm`
- ![[Pasted image 20260210174129.png]]

#### Gather

- Opposite of scatter

![[Pasted image 20260210174518.png]]

#### MPI_Scatterv

- Similar to scatter but variable message size
- Will use in lab 2
- ![[Pasted image 20260210174627.png]]
- send_count[] and disps[] are vectors
- Gaps and overalps are allowed between send blocks in source data
- Data blocks of different sizes allowed
- Data can be distributed to ranks in any order
- Source also has a receive buffer

#### GatherV

- Variable message-size version of MPI_Gather
- Same logic as Scatter and ScatterV
- ![[Pasted image 20260210180217.png]]
- From code demo:
	- After scattering, do an action (multiply by 10) and gather (back to the source)

Note:
- Print order is not necessarily execution order


#### AllReduce

- Same as reduce, reduced result is sent to all ranks. Same syntax as reduce, excvept no target rank
- ![[Pasted image 20260211133009.png]]


#### AllGather

- Gathered result sent to all ranks
- No target
- ![[Pasted image 20260211133115.png]]
- ![[Pasted image 20260211133126.png]]
