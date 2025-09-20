#Cache
### Summary

- Reduce hit time
	- Small and simple first-level caches'
	- Way prediction
- Increase bandwidth
	- Pipelined caches, multibanked caches, non-blocking caches
- Reduce miss penalty
	- Critical word first, merging write buffers
- Reduce miss rate
	- Compiler optimizations
- Reduce miss penalty or miss rate via parallelization
	- Hardware or compiler prefetching

## Reduce Hit Time

#### Direct Mapped Cache
Note that there is a data delay, and a tag delay
- Data/Tag delays
- ![[Pasted image 20250916011832.png]]
- Optimization: read the data while comparing the tag. Use the data if Hit

#### Set Associative Cache
You know which data to use from the mux
- Data delay cannot overlap with tag delay. And delay can only select after comparing tag (multiplexer delay)
- ![[Pasted image 20250916012147.png]]

#### Associativity and simulation results

L1 Size and Associativity simulations (Cacti sim):
- Access time 
	![[Pasted image 20250916012411.png]]
- Energy simulation
	![[Pasted image 20250916012623.png]]
	- Energy is affected by the number of blocks
	- You can organize cache in bands and only activate certain bands when trying to access. Saves energy

### Way Prediction
*Predict the way to pre-set mux*

- Miss prediction means longer hit time

**Cache block has prediction bits, keeping track of  things like how many times block has been accessed lately**

- Becomes similar to direct mapped cache in the sense that it picks one, it could be a miss though

#### Implementing way prediction

- First method is choosing a line and loading data. Compare all the tags in parallel, if it matches just use, if not, you already compared --> single cycle
- Second method: compare only one tag, if miss, other cycle to compare other tags
- Prediction accuracy
	- >90% for two-way
	- >80% for four-way
	- I-cache better accuracy than D-cache
- Extend to predict block too
	- "Way selection": Can give you exact block to pick
	- Increases miss-prediction penalty: need to re-set prediction bits since you reuse the address

## Increased Bandwidth

### Pipelined Caches

- Pipeline cache access to improve bandwidth

Pipeline basics: Split instructions in different stages
- Start other stages while one is running, for more efficiency
- You might have more clock cycles per instruction but you increase throughput

- Increases branch miss-prediction penalty
- Easier to increase associativity

### Multibanked Caches

- Organize cache as independent banks to support simultaneous access
- Interleave banks according to block address
- Arange using module of block address
- You can turn some banks off, for energy efficiency
- Can pre-fetch from separate banks at the same time, so also more efficient
- ![[Pasted image 20250916014835.png]]

### Nonblocking Caches

- Allow hits before previous misses complete
- No stalling when miss in the cache, start executing others
- Ex: 
- ![[Pasted image 20250916015142.png]]
- "Hit under a miss"
- How many misses can I support>"Hit under multiple miss" --> L2 cache must support non-blocking caches
- Processors can hide L1 miss but not L2 miss penalty

- Deciding how many misses to support:
	- Temporal and spacial locality in miss stream (depends on access pattern)
	- Bandwidth of responding memory or cache
	- To allow more at lower level (L2), L1 must support
	- Latency of memory system
- Implementing:
	-  Hit and miss may collide: Miss and then a Hit on the same block
		- Even misses can collide
		- Need to track outstanding misses
	- For outstanding misses: Miss Status Handling Registers (MSHR)
		- If n misses, n registers that hold metadata you can compare

## Reduce Miss Penalty

### Critical Word First, Early Restart

Processor typically needs one word out of a block. Don't wait for the full block to come in. **Critical Word First**
- **Request missed word from memory first**
- Send to processor as soon as it arrives
**Early Restart**
- **Request words in normal order**
- Send missed word to processor as soon as it arrives

- Effectiveness depends on block size and likelihood of another access to the same block that hasn't been fetched

### Merging Write Buffer

- When storing to a block that is pending in write buffer, update write buffer
- Do not apply to I/O Addresses
- ![[Pasted image 20250917002010.png]]

## Reduce Miss Rate

### Compiler Optimizations

**Loop Interchange**
- swap nested loops to access memory in sequential order
- Try to access things sequentially, if nested loop you can swap order in some cases so you take advantage of order:
	![[Pasted image 20250917002423.png]]

**Blocking**
- Instead of accessing entire rows or columns, subdivide matrices into blocks
- More memory accesses but better locality of addresses
- Not done by compiler, something expected from programmer

Ex: Assume cache can hold NxN matrix, as well as one row from another matrix

![[Pasted image 20250918212408.png]]

- No easy way to swap the loops
- N^3 + N^2

![[Pasted image 20250918212642.png]]
- B is the blocking factor. Number of accesses to memory are reduced by factor of B
- Spacial and temporal locality

### Hardware Prefetching
Fetch two blocks on miss (include next sequential block)

- If instruction I not found in cache, bring in both I and I+1
- You could store in a buffer, processor can look in the buffer

### Compiler Prefetching
Insert prefetch instructions before data is needed, to have data ready when needed

- Non-faulting: prefetch doesn't cause exceptions
- Can be register prefetch or cache prefetch
- Combine with loop unrolling and software pipelining

#### Loop Unrolling
- If in a loop, operations that aren't dependent on each other can be unrolled because it allows the CPU to be faster. Will revisit more later