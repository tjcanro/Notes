#Cache
## Cache Optimization

### Block Size Optimization

- Larger block size to reduce miss rate
	- Also reduces compulsory misses
- Larger block size increases miss penalty
- Larger block sizes => reduced number of blocks
	- Increases conflict misses, and capacity misses in fully associative
- Block size depends on latency and bandwidth

### Cache Size and Associativity Optimization

- Larger cache reduces miss rate
	- Reduce capacity misses
	- Longer hit time, higher cost for comparators
- Higher associativity to reduce miss rate (conflicts)
	- Rule of thumb: 8-way set associative is as effective as fully associative
	- Other rule: Direct mapped cache of size N has about same miss rate as 2-way set of size N/2
	- More associativity means increased hit time
		- N comparators: cost, time to compare tags
		- Faster processors encourage simple cache - increased clock cycles (miss penalty)

### Multi-level Cache

- Improving miss penalty is as beneficial as improving miss rate
- Two-level cache
	- First level, close to processor. Small to match clock clock cycle time of processor
	- Second level large enough to capture misses from first level
- AMAT = Hit time(L1) + Miss rate (L1) * Miss penalty (L1)
- Miss penalty (L1) = Hit time (L2) + Miss rate (L2) * Miss penalty(L2)
- Local miss rate = Misses / Memory accesses to cache
- Global miss rate = Misses in a cache / Total number of memory accesses
	- L2 cache miss rate is Miss rate(L1) * Miss rate(L2)
- Speed of first level cache affects clock rate of processor
- Speed of L2 only affects miss penalty of first level cache
- Design Decisions
	- Second level cache has to be much larger than L1
	- Increase associativity reduces miss rate with little clock cycle penalty
	- Cache design --> Fast hits and few misses
		- L2 has less hits than L1. Focus on fewer misses

#### Read over Writes and Address Translation

- Assume write-through cache with a write buffer
- If block that was updated is in write buffer and a read miss occurs to the block
	- Should processor wait until block was written from buffer to memory before reading back?
	- If no conflicts, processor can read from buffer before writing to memory
- In write-back cache and read is replacing a dirty block
	- Copy dirty block to buffer, read the memory and write the dirty block to memory
	- Processor can get data and wait on it rather than waiting for memory to finish writing dirty block

## Memory Technology and Optimization

- Performance metrics
	- Latency -> Cache
	- Bandwidth -> Multiprocessor and I/O
	- Access time
		- Time between read request and when word arrives
	- Cycle time
		- Minimum time between unrelated requests
- SRAM memory has low latency, use for cache
- Organize DRAM chips into banks for high bandwidth

- SRAM (Static RAM)
	- 6 transistors/bit (expensive)
	- Minimal power to retain bit in standby mode
	- On-chip cache SRAMs width marched the block size of the cache
		- Tags stored in parallel to each block
		- Allows entire block to be read or written in one cycle
	- Access time is proportional to number of blocks
	- Energy consumption depends on num bits and blocks
- DRAM
	- One transistor/bit
	- Must be re-written after being read
	- Must be periodically refreshed
- DRAM technology
	- Bits organized in rectangular array
		- DRAM accesses an entire row
	- Read data into row buffer
	- Address lines are multiplexed
		- Upper half is Row access strobe (RAS)
		- Lower half is Column access strobe (CAS)
		![[Pasted image 20250915203536.png]]
	- Activate command opens the bank, loads into row buffer. Once done reading, precharge command is sent. Makes any future access ready
- DRAM Organization
	- Amdahl
		- Memory capacity should grow with processor speed
		- Memory capacity and speed haven't kept pace
	- Optimizations:
		- Multiple accesses to same row
		- Synchronous DRAM
			- Added clock to DRAM interface
			- Burst mode: supply successive words from row with reduced latency
		- Wider interfaces
		- Double data rate (DDR) DRAM
			- Transfer on rising and falling clock edges
		- Multiple banks on each DRAM device
- DDR:
	- DDR2: Lower power and higher clock rate
	- Subsequent improvements have even lower power and higher clock rate
	- GDDR5 is graphics memory based on DDR3
	- Reducing power in SDRAMs
		- Lower voltage
		- Low power mode (ignores clock, continues to refresh)
	- Graphics memory has higher bandwidth
		- Wider interfaces (32 vs 16 bit)
		- Higher clock rate
			- Done by soldering memory rather than socketed DIMM modules

#### Stacked/Embedded DRAMs

- Stacked DRAMs in same package as processor
	- High Bandwidth Memory (HBM)
	![[Pasted image 20250912103434.png]]

#### Flash Memory

- Type of EEPROM
- NOR flash: bit cell like a NOR gate
	- Random read/write access
	- Instruction memory in embedded systems
- NAND flash: bit cell like NAND gate
	- Denser (bit/area), but page at a time access (0.5KiB to 4KiB)
	- 25 nano sec for first byte, 40 MiB/s for subsequent bytes
	- SDRAM much faster: 40 ns for first byte, 4.8 GB/s for subsequent
	- Much faster than magnetic disk
- NAND has to be erased in blocks before being overwritten
- Nonvolatile, can use as little as zero power
- Limited number of write cycles (~100,000)
- Much cheaper than SDRAM

#### Memory Dependability

- Memory susceptible to comic rays
- Soft errors: dynamic errors
	- Detected and fixed by error correcting codes
	- Parity bit to detect in sequence of bits
		- One per 8 data bits
		- Chipkill: RAID-like error recovery technique
- Hard errors: permanent errors
	- Use spare rows and replace defective rows