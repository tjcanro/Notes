
#SharedMemory 
#CacheCoherence 

## Coherence Protocol Extensions

- MESI (Modified, Exclusive, Shared, and Invalid)
	- Exclusive state used to indicate that cache block is present only in a single cache but is clean
	- Any updates to the block in E state can be written without generating any invalidates
	- Any read misses to block in E state must be changed to S state to maintain coherence (some other processor trying to read)
- MOESI (Modified, Owned, Exclusive, Shared, and Invalid)
	- Owned state is used to indicate that a cache block has been updated and is out-of-date in memory
	- Used to send directly to other processor
	- Other caches that are newly sharing the block will keep in shared state
	- When read miss, owner of the block supplies the block
	- When block in owner's cache replaced, it must be written back to memory

### Limitations of Snooping Protocols

- For multicores, single shared bus becomes a bottleneck with just a few cores
	- Multicore designs implement higher bandwidth interconnection schemes
	- Multiple independent memories to allow larger number of cores
- IBM Power8 up to 12 processor in single multicore uses
	- 8 parallel busses to connect distributed L3 chaces
	- Up to 8 memory channels - RAM
- Snoop bandwidth - amount of data per second that a processor or internconnect can handle for cache coherence snoop traffick
- With snooping, every cache must now examine every miss
	- Consumes bandwidth which would not have ocurred in single core uniprocessor
	- Increasing the interconnection bandwidth pushes the problem to the cache

### Increasing Snoop Bandwidth

- Cache tags can be duplicated, and the snoop traffic can be directed to use the duplicated tags instead of the cache
	- Specialized hardware to take care of snoop traffic. Goes and changes state on cache and reduces cache bandwidth use with this
- Outermost shared cache (L3) can be distributed so each has a portion of the memory and handles snoops for that portion of addr space
- Place a directory at the outermost shared cache (L3)
	- Use dir at L3 implies that when block is accessed, access need not be "snoop" or broadcast to all L2 cache but only those that the directory indicates may have copy
	- Can be distributed similar to L3
	- Intel Xenon uses
- Example power8 implementation:
  ![[Pasted image 20251109225139.png]]

## Cache Misses - SMP

- Coherence misses - misses from inter-processor communication
- True sharing miss:
	- When one processor updates shared block and another processor reads shared block following update
- False sharing miss:
	- Single valid bit per cache entry
	- Word not being updated still results in a miss (2nd word updated and other processor reads 3rd word in same block)
	- If valid bit per block, no need to invalidate

#### Example

- Assume words z1 and z2 are in the same block (shared state) or both P1 and P2
- ![[Pasted image 20251109230355.png]]

### Performance Study on Cache Characteristics
![[Pasted image 20251109230612.png]]

Commercial Workload:
![[Pasted image 20251109230704.png]]

(PAL is sequence of OS instructions)

Memory Cycles per instruction:

![[Pasted image 20251109230934.png]]

From 2 -> 4 -> 8 there is not much difference because true sharing miss cycles isn't much different. Capacity misses become much less cycles on increased size, as well as compulsory

In respect to processor count:
![[Pasted image 20251109231154.png]]

Block size:
![[Pasted image 20251109231212.png]]

Assumed 2MB with 2-way set associative

- When true sharing comes down, a lot of localization on this workload
- Note increasing block size increases traffic to memory and possible contention with other cores

#### OS Workload

- Kernel and user miss rate studied
- ![[Pasted image 20251109231525.png]]
- Kernel different from user. Can be analyzed that block size is important in kernel, due to locality:
  ![[Pasted image 20251109231626.png]]
