#DistributedSharedMem

## Directory Protocols

Snooping require communication among all caches on every cache miss
- Limits scalability
- Another approach: Use centralized directory to keep track of every block
	- Which caches have each block
	- Dirty status of each block
- Implement in shared L3 Cache
	- Keep a bit vector of size = # cores for each block in L3
	- Not scalable beyond shared L3
	- Possible to have bandwidth contention and other issues of snooping protocols

### Alternative Approach - Distribute memory
![[Pasted image 20251111103149.png]]

- Directory also distributed across processors
- You need to ensure consistency across directories
- Broadcast request to all directories (same snooping problem)
- Note that block only resides in a particular bank of the cache

Solution:
- Location of each memory block is determined by its address
- Similar to snooping cache, state of every block is tracked
	- Exclusive, shared, invalid
- Directory entry tracks the locations where a memory block is cached
- Memory block can be in one of these states:
	- Uncached - no processor has it
	- Shared/clean - cached in one or more processors and memory is up-to-date
	- Exclusive/modified/dirty - one processor (owner) has data; memory out of date
- Node - Single multicore processor that implements coherence
- Three types:
	- Home node (H)
		- Node that stores the block and its directory entry in its memory
	- Local node (L)
		- The node where a request originates
			- L may be the same as H
		- Reads or writes the cache block
	- Remote node (R)
		- The node that has a copy of the cache block

### Enforcing Coherence

- Coherence is enforced by exchanging messages between nodes
- For each block, directory maintains a set called **Sharer** which contains the list of nodes that share a block or the node that owns a block
- When a node (L) tries to read a block it can result in a read hit or a read miss

- **Read hit**: reads data from the cache
- **Read miss**: from the block address, identify home node. Send message to H of the requested block
	- The directory in H may indicate the block is:
		- Not cached
		- Shared/Clean
		- Exclusive/modified

#### Handling Read Miss

- Block is shared or uncached
	-  L sends request to H
	- H sends block to L
	- State of block is now "shared" in dir
	- State of block is "shared" in L (Needed to keep consistency on read hit)
- Block is exclusive in another cache
	- L sends request to H
	- H sends data fetch message to R
	- R sends data to H where it is written to memory
	- H sends data to L
	- L and R set state of block to "shared"
	- H adds L to the Sharer set
	![[Pasted image 20251111110615.png]]
	- Another possible one could be that R sends directly to L

#### Handle Write Miss

**Block is uncached**
- Similar to read miss (L --> H; H--> L)
- State of the block is set to "exclusive"

**Block is exclusive**
- Similar to read miss on exclusive block
- State of the block is set to "exclusive" in H and L
- State of the block is set to "Invalid" in R

**Block is shared**
- L sends request to H
- H sets the state to "exclusive"
- H sends the block to L
- Invalidate all remote nodes (R) --> All of them Ack
- H sets the sharers to L

#### Handle Write Hit

**Block is "exclusive" in L**
- Just write the data

**Block is "shared" in L**
- L sends write hit to H to set the block as "exclusive"
	- L sets state to exclusive
- H sets the state to "exclusive"
- H sends "invalidate" message to each sharer (R)
- R sets the block's state to "invalid"

### Coherence Protocol - Node

![[Pasted image 20251111121520.png]]

 **Directory**
![[Pasted image 20251111122532.png]]

#### Directory-based Coherence Example

- Case 1
	- X uncached state in home directory
![[Pasted image 20251111122901.png]]

**Scenario**
- Pj reads X --> read miss --> set to shared
- Pj writes to X --> Write hit --> set to exclusive 
---
- Case 2
	- X is exclusive in home directory and owned by Pk (dirty(d) in Pj)
**Scenario**
- Pk reads X

![[Pasted image 20251111123739.png]]
Read miss --> Set to shared in directories

If write --> write miss -->  invalidate Pj --> Make Pk the exclusive 

---

- Case 3
	- X is shared in home directory and clean in Pj and Pk

**Scenario**
- Pj reads X
- Then Pk writes into X
![[Pasted image 20251111124548.png]]

- Write hit --> Invalidate other and make exclusive

## Synchronization
#Synchronization

### Diner Philosopher's Problem

Shared resources

**Solution**: 
- Atomic lock: grab both forks or neither

#### Parallelism
- Multiple cores, multiple threads
- How to co-ordinate tasks?
	- When are they independent?
	- When they depend on result of previous task
- Synchronization is necessary to impose ordering
	- Concurrent computations where there is dependence
- Proper instruction set design can help support efficient synchronization primitives
 