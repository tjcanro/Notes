
#ThreadLevelParallelism

**Example**
Suppose 100 processors. What fraction of original computation can be sequential if we want speedup of 80? Program either uses all processor or only one processor

- Use Ahmdal's
	- ![[Pasted image 20251102091559.png]]
- Trying to calculate Frac (enhanced) with speedup = 80
- You are really calculating frac. parallel., so speedup parallel is 100
	- ![[Pasted image 20251102091722.png]]
	- Only 0.25% can be sequential

### Challenges in Parallel Processing

- Two main problems:
	- Limited parallelism available in program
	- Large Latency and remote accesses in parallel processor
- Limited parallelism can be overcome through
	- New algorithms that offer better parallel performance
	- Software that maximizes time spent executing all processors
		- Distribute work evenly across processors
- Overcome impact long remote latency by:
	- Reducing frequency of remote accesses
		- HW: cache shared data
		- SW: restructure data to make more accesses local
	- Overlap latency using multithreading or through prefetching
	
--- 
#SharedMemory
## Centralized Shared Memory

Inclusive memory hierarchy: objects in cache are in RAM

Assume 4 processors and a shared RAM
- Whenever an access, transfer to local cache
- If a block is in multiple local caches, shared

**Global State**
- Defined by main memory
	- Assume inclusive memory hierarchy

**Local State**
- state defined by individual caches associated with the processor

**Private Data**
- Data used by single processor
- Copied from main memory to local cache

**Shared Data**
- Data shared amongst multiple processors
- Data copied from main memory to local cache of all processors that want to use the data

What happens when one of the processors updates data in other processor's local cache

#CacheCoherence
## Cache Coherence

- Memory system is coherent if any read of a data item returns the most recently written value of that data item
	- Simple def
- Memory system should be defined along the lines of 
	- Coherence: What values can be returned during read (you can define a model but data has to be the same in all processors)
	- Consistency: When a written value (updated value) will be returned by a read
- Coherence and consistency are complimentary

### Example
![[Pasted image 20251102095713.png]]

Write-back Cache: dirty bit to indicate value is outdated. Don't write unless block replaced from cache
- Assume WB cache

- Coherency alternative definitions:
	- Mem system is coherent if read by A to X that follows a write by A to X, always returns new value
		- No writes by other processors occur between write and read by A
	- Read by A to X that follows write by B to X returns new value if read and write are sufficiently separated in time
		- No other writes to X between two accesses
	- Writes to same location are serialized
		- Writes to X by any two processors are seen in same order by all processors

#### Write Serialization

- Say A writes 5 to X followed by B writing 6 to X
	- What value will following processors read?
- Without serialization: 
	- 'C' might read x=6 indefinitely but 'D' could retain x=5
- Serialization ensures all processors will see latest value at some point
- Simple assumptions
	- Write does not complete until all processors have seen the effect of the write
	- Processor does not change the order of any write with respect to any other memory access (between writes and reads)
		- Reads can be reordered but forces processors to finish write in program order

## Cache Coherence - Schemes

- Caches provide both migration and replication of shared data

**Migration**: Move data from memory to local cache
- Reduces latency

**Replication**: Multiple copies of data across processors
- Reduces contention

- Track the sharing status of each block
	- Not assuming all blocks are shared, as that would waste clock cycles
	- Similar to valid and dirty bits

- **Directory based**
	- Sharing status of particular block of physical memory is kept in one location
		- SMP: centralized directory
		- DSM: distributed directory

- **Snooping**
	- Every cache has a copy of the data from physical memory could track the status of the block
		- Cache controller monitor or snoop on interconnect network (broadcast medium) to determine weather the copy held is being requested

### Snooping Protocol

![[Pasted image 20251102135625.png]]

Value invalidated and then when processor B has to read, cache miss. Processor A will write the value and value will be copied to cache of B

#### Ways to Implement
- Write invalidate protocol - Processor has exclusive access to a data item before writing that item
- Write update protocol - Update all the cached copies od the data item when that item is written
	- Also called write broadcast
	- Consumes more bandwidth since any updates to data items must be broadcast to shared cache lines

#### Invalidation

- Use bus to implement invalidate protocol
	- Single-chip multicore - bus is connection between private caches (L1, L2) and shared outer cache (L3)
- Processor "acquires" bus access and broadcasts the address to be invalidated on the bus
- All processors continuously snoop on the bus, watching addresses
	- If address in cache, corresponding data is invalidated
- When two processors attempt to write shared blocks at same time
	- Arbitration for the bus will serialize the invalidate operation

#### Cache

- Where to find a data item on a cache miss
	- Write-through cache - all updates sent to memory
	- Write-back cache - recent updates can be in private cache
- In write-back cache, each processor snoops every address placed on shared bus
	- Send the data from the processor that has the dirty copy of the requested cache block --> From cache to cache
- Increased complexity to retrieve block from another processor's private cache
	- Also takes longer than retrieving it from shared L3

#### Overview
![[Pasted image 20251102190719.png]]

FSMs:

![[Pasted image 20251102194425.png]]

Each block in the cache has it's own state

Summary of states over multiple operations:
![[Pasted image 20251109220858.png]]
- Read/Write hit or miss placed with the respective block, so if different block, nothing happens for other processors
- Continuation:
  ![[Pasted image 20251109221410.png]]


