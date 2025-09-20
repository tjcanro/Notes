#Cache 
## Cache

For accessing cache, you need cache index, tag, and valid bits

- Direct vs 2-way vs fully associative

### Sources of Cache Misses

- Compulsory misses: first access to a block. Whenever you first access a block, cache needs to be populated
- Conflict (collision): memory locations mapped to the same location
	- Can happen in set associative or in direct mapped
	- Solution 1: Increase cache size
	- Solution 2: Increase associativity
- Capacity miss: Cache cannot contain all blocks program needs
	- Solve by increasing size, can only happen in fully associative
- Coherency (Invalidation)

### Writing to Cache

- Read is much easier than write
	- Instruction cache easier to design because you don't need to update at runtime
- Cache write: How do we keep cache and memory consistent?
- Write back: Write to cache only, write the cache block to memory when cache block is being replaced
	- Need a dirty bit: value for block updated
	- Reduce memory bandwidth requirement
	- Control can be complex
	- Problem: when a block is replaced, processor needs to stall in all misses
	- Solution: Write to buffer in miss. Memory subsystem will copy from buffer to RAM
- Write through: write to cache and memory at same time
	- Slow
	- Solution: Write buffer
		- Hold data waiting to be written to memory
		- CPU continues immediately
			- Only stalls on write if write buffer is already fall

### Measuring Cache Performance

- Components of CPU time
	- Execution cycles
		- Account for cache hit time
	- Memory stall cycles
		- Mainly from cache misses

`CPU Execution time = (CPU clock cycles + Memory stall cycles) * Clock cycle time`

`Memory stall cycles = num misses * miss penalty`

` = IC * Misses/Instruction * Miss penalty`
`= IC * Memory Accesses / Instruction * Miss rate * Miss penalty`

- Average memory Access time (AMAT):
	`Hit time + Miss rate * Miss penalty`
		Better metric than hit rate

- Miss rate (1000 instructions) = $\frac{Misses/1000\ instructions}{Memory Accesses/Instruction}$
- Instructions for accessing instructions always need to go into memory, so it's 1.00 in the bottom for that one
- Total instructions referencing memory: 100%(for instructions) + (percentage for data)
- Overall miss rate, you multiply percentage of memory accesses by their misses, and you add together (if not unified)

- Load or store takes 1 extra clock cycle on unified cache, if only one port
- AMAT better for accounting for extra instruction cycles