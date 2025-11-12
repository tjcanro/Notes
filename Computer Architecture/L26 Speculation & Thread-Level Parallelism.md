#ILP 
#HWSpeculation 

- Mis-speculation degrades performance and power
	- May cause additional misses (cache, TLB)
- Prevent speculative code from high cost misses (L2)
- Speculating through multiple branches
	- Complicates recovery
- Energy efficiency --> Speculation only energy efficient if highly accurate
- Misspeculation is higher on integer benchmarks. Easier to speculate on floating point operations that repeat a lot

### Address Aliasing Prediction

- Predicts whether two stores or a load and a store refer to the same memory address
	- If they don't refer to same address, may be safely interchanged
	- If same address, must wait until memory addresses accused by instructions are known 
- Outcome is boolean
	- Small predictors do reasonably well
- Must recover after misprediction
	- Addresses predicted to be different but turn out to be same
		- Processor must restart sequence, similar to mispredict branch

### ILP Limitations

- Dependences
	- Data
	- Control
	- Memory
- Diminishing returns - HW complexity grows faster than performance gains
	- Out of order execution requires ROB or physical regs, etc.
	- Speculation
- Mispredictions can lead to wasted energy

## Thread-Level Parallelism
#ThreadLevelParallelism

- Thread  -> Smallest unit of execution within a process
	- Own PC, reg, stack
	- Shares same addr space of a process
		- Allows accessing data of other threads within same process
- Multithreading
	- Allows multiple threads to share functional units of single processor
- Multiprocessors - tightly coupled processors whose coordinations and usage are typically controlled by single OS
	- Shared memory through shared address space
		- Tightly coupled = share common memory and single OS instance
- Two flavors
	- Single chip with multiple cores (multicore)
	- Multiple chips each usually multicore

### Flynn's Taxonomy

- SISD: uniprocessor with pipeline and speculative exeuction
- SIMD: vector architecture, GPUs
- MISD
- MIMD: Tightly coupled (sharing memory and single software manages) or loosely coupled (share memory through message passing)

### MIMD

- Threads executing on different data should be able to interact with other threads and/or the data to solve a problem
	- May not be required if threads are solving different problems
	- ![[Pasted image 20251028233244.png]]
- If no shared address space, processors have to speak. If shared memory
- Different flavors depend on memory architectures and the virtual address space of each processor

### Microprocessor Architecture

- For n processors, need at least n threads
	- With multithreading, 2x - 4x higher
- Gain size --> amount of computation assigned to each thread
- Independent threads within single process are identified by programmer

### Multiprocessor Types

- **Symmetric multiprocessors (SMP)**
	- Small number of cores
	- Share single memory with uniform memory latency
	- ![[Pasted image 20251028235150.png]]
	- Private caches and shared cache, plus shared main memory and IO system
- Some multicores have nonuniform access to the outermost cache (NUCA)
	- Ex: L3 in IBM Power8 distributes across the chip

- **Distributed shared memory (DSM)**
	- Memory distributed among processors
	- Non-uniform memory access/latency (NUMA)
	- Processors connected via direct (switched) and non-direct (multi-hop) interconnection networks
		- ![[Pasted image 20251028235633.png]]
		- With large number of processors, memory bandwidth couldn't handle
			- Local accesses more predictable