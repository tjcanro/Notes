
#Synchronization 

## Synchronization: Data Race

Two processes (P1 and P2) accessing same area of memory
- P1 writes, then P2 reads
- Data race if P1 and P2 don't synchronize
	- Result of P2 depends on order of accesses

- Hardware support required
	- Atomic read/write memory operation
	- No other accesses to the location allowed between read and write
- Could be a single instruction
	- Atomic swap of register <-> memory
	- Or atomic pair of instruction
- Test-and-set primitive (what locks and atomic ops used to be called)

### Handling Atomic Operations

- Example: "Atomic Swap" interchanges a value in a register for a value in memory
	- Loads the value from a memory location into the register
	- Stores the value in register into the memory location
- Atomic swap can be used to implement locks:
	- Lock is represented by a memory location L
		- L=1 -> Locked
		- L=0 -> not locked
	- Important that when done with critical section, lock is released
- Lock with atomic swap?:
  ![[Pasted image 20251111163838.png]]
- Atomic swap because other threads could be running at the same time, so no guarantee that the swap would happen in time

### Possible Implementation of Atomic Swap

- May use two special load/store instructions
	- Must guarantee no other interleaving instruction will change loaded/stored memory location
	- **Load Reserved** (or load linked) --> guarantees no other instructions can change location
		- LR Rd, Offset(RS1) --> status reg that tracks mem location
	- **Store Conditional**
		- SC RS2, Offset(RS1) --> checks status reg
		- If store conditional fails, writes 0 to RS2
- Implementation:
	- ![[Pasted image 20251111164948.png]]
	- (should be bez, check if x3 is equal to 0, retry if it is)
	- **Atomic Fetch and Increment** (like on semaphores?)
	- ![[Pasted image 20251111165402.png]]

### "Locks" Using Coherence

- In a single CPU/memory scenario
	- OS does not allow context switching between LR and SC
- In shared memory system
	- Memory controller may keep a record that an LR was issued to L and does not allow a store or another LR to L before an SC is executed on L
	- In an MSI coherent cache system, LR and SC can be achieved as:
		- Executing LR sets L to modified
		- A write-miss on L generates a "request-to-write"
		- Ignore requests for L until the SC is executed on L
- With atomic operations, we can now implement **Spin Locks**

**Spin Locks**: Processor continuously tries to acquire, spinning around a loop until it succeeds
- Used when lock is expected to be held for a very short time

#### Advantages

- Spin lock can be implemented on a local copy (cache) rather than the global memory on each attempt
- Principle of locality - processor that recently acquired the lock is highly likely to acquire the lock again

- To optimize implementation of spin lock on coherent system, requires that local copy of the "lock" is read before attempting to acquire a lock by doing a swap operation
	- Read the lock variable state until it is in unlocked state
	- Race against all other processors to see which can lock the variable first

#### Example in Coherence

![[Pasted image 20251111194538.png]]

## Memory Consistency Models Intro
#MemoryConsistency

Coherence ensure multiple processors see a consistent view
- Does not answer when a processor must see the updated value (from another processor)
- ![[Pasted image 20251111203002.png]]

- Assume A(=0) and B(=0) are cached in both processors. What values of A and B will P1 and P2 see?
	- Assume writes take immediate effect and are immediately seen by other processors?
	- Assume that write invalidate is delayed and processor is allowed to continue?
		- Both can lead to different and unexpected results

### Sequential Consistency

- A multiprocessor is consistent if result of any execution is same as if all were done sequentially, and the operations of each processor appear in this sequence
- Simplest way is delay completion of any memory access until invalidations caused by that access are completed before letting processor continue
	- Can't place write in write buffer and continue with read
	- Delay the read of A or B UNTIL B=1 or A=1 has completed
- Simple but reduces performance

#### Programmer's View

- Synchronized program -> All accesses to shared data are ordered by synchronization operations
	- Shared variable being read and written to by two different processors surrounds the read and write with a lock and unlock
		- Ensures mutual exclusion for the write and ensures the read is consistent
- If accesses are unsynchronized, behavior of the program would be unpredictable
- Synchronization libraries provide correct and optimized implementation of mechanisms
- Use of standard synchronization primitives ensures sequential consistency even if the architecture implements a relaxed consistency model
