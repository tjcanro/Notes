
### Review of Moore's law
- Smaller (Moore's law) and faster due to clock speed (Dennard scaling)
- In 1980s, bigger increase due to RISC, Unix, ILP and Memory caches
- 2003, single processor performance improvement ended (beginning of multicore)
- Focus on exploiting parallelism
- Still limits on perf due to Amdahl's law: domain-specific processors/cores

- Moore's law no longer able to double performance, and end of dennard (voltage) scaling
- Head towards exascale computing:
	- Exponentially increasing parallelism: multicore CPI, new parallel architectures (GPU, PIM, etc), HPC
- Extreme heterogenity: compute, data storage/movement

### Parallel Computing
#### Serial Computing
- Problem broken into discrete series of instrucitons
- Instructions executed sequentially on a single processor
#### Parallel Computing
- Problem broken into discrete parts that can be solved concurrently
- Each part executed simultaneously on different processors

#### Multi-core CPUs
- All standalone computers are parallel in HW
	- Multiple functional units
	- Multiple execution units/cores
	- Multiple HW threads

#### Parallel Computing: GPUs, FPGAs, PIMs

- Nvidia GPUs with many more Cuda cores, Tensor Cores (Very specific for ML) and Ray Tracing cores
- Graphicore IPU (Intelligent Processing Unit) /PIM (Process in memory)
	- Massively parallel MIMD
	- Processor in the memory
	- High performance/efficiency for future ML trends
	- Model & dData in tightly coupled large locally distributed SRAM

### HPC Systems

- Stand-alone computing nodes interconnected to form parallel computing clusters and HPC systems
- ![[Pasted image 20260116000503.png]]
- Independent computers with various
	- Computing devices
	- Accelerators
	- Memory/storage, devices/archs

- Connected by networks of various:
	- Topologies (the form of the interconnections), comm latency, bandwidth (from the pipes), scalability

- Top HPCs in the world listed based off benchmark
	- Top500: 
		- Based on HPL (High-Performance Linpack).
		- Solves a dense system of linear equations.
		- Maximizes floating-point throughput (FLOPS) under ideal conditions.
	- HPCG:
		- Solves sparse linear systems using conjugate gradient methods
		- Emphasizes:
			- Memory bandwidth
			- Memory latency
			- Network communication
			- Irregular data access

## Programming Models

- Distributed memory (message passing)
- Shared memory (threads)
- Hybrid
![[Pasted image 20260121125737.png]]
Programming model give an abstraction on the physical hardware and memory architecture

### Message Passing

- Process/tasks use their own local memory during computation
- Tasks exchange data thorough cooperative send and receive messages
	- Send must have a matching receive
	- Programmer responsible for parallelism and synchronization
	- Message Passing Interface (MPI) is the standard

### Shared Memory
- Thread based programming
- Data can be shared or private to a thread
- Each has local access to private data owned by that thread