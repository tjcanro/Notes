## Intro

- Programmers want as much memory with low latency
- Fast memory tech is more expensive per bit than slower
- Solution: organize memory into hierarchy where entire memory space in largest, slowest. Incrementally smaller and faster memory
- Temporal and spatial locality ensures nearly all references can be found in smaller memories --> Illusion of large memory

### Principle of Locality

Program access a relatively small portion of the address space at any moment
- Temporal locality: If an item is referenced, it will tend to be referenced again
- Spatial locality: If an item is references, items whose addresses are close by tend to be referenced soon

Service most accesses from small/fast memory
- Small usually Cache, large is main memory
- Reduce the bandwidth required of large memory

Ex:
![[Pasted image 20250902234939.png]]

**At any given moment, data is copied only between 2 adjacent levels:**
- Upper level (Cache): Closer to processor
- Lower level (Memory): Further from processor
- Inclusive: When data in L1 is also in everywhere else in memory
- Exclusive: Data at any level only retained at that level

--> What is the state of memory before processor can process that block

You transfer whole block when not present (which is contiguous, normally 4kb - 128kb)

#### Performance

Memory performance Gap:
- Most of the increase is in processors being capable of generating more memory addresses per unit of time
- Memory not able to handle that many requests
- Design is more important with multi-core processors
	- That's why multi-port, pipelined caches are necessary
	- Two levels of cache per core
	- Shared third-level cache on chip

##### Performance and Power

- Memory designers focused on:
	- Optimizing access time: cache access time, miss rate, miss penalty
	- Power becomes big consideration:
		- Second or third-level cache: Large area and power (both static and dynamic)
		- In PMDs: Cache can account for 25% - 50% of power consumption

### Terminology

- Hit: Data appears in some block in upper level
	- Hit rate: Fraction of memory accesses in upper level
	- Hit time: Time to access upper level, which is RAM access time + time to determine hit/miss
- Miss: data needs to be retrieved from block in lower level
	- Miss: 1 - Hit rate
	- Miss penalty: Time to replace a block in the upper level + time to deliver block to processor
- Hit time << Miss penalty