## Parallel Computer Architectures (PCA)

Flynn's Taxonomy:
![[Pasted image 20260116131928.png]]

Most modern supercomputers have MIMD architectures, with SIMD sub-components, like GPUs

- MIMD can be categorized into:
	- Shared memory systems
	- Distributed memory systems

## Classification Based on Memory Architectures

- Distributed memory
	- Each processor has own local memory
	- Processors exchange data using explicit message passing
	- Scalable and can use COTS processors and networking
	- Programmer is explicitly responsible for data communication
	- Non-uniform memory access time, non-uniform memory access times: local vs remote transfers
	- ![[Pasted image 20260121125623.png]]
- Shared memory
	- All processors talk to centralized memory through a shared bus
	- Single address space
	- Global address space is user-friendly for programmer
	- Not as scalable, due to traffic
	- ![[Pasted image 20260121125634.png]]

#### Shared Memory Architectures

**Uniform Memory Access (UMA)**
- Symmetric Multiprocessor (SMP) machines
- Identical processors
- Equal acccess and access times to memory
- ![[Pasted image 20260116133329.png]]
**Non-Uniform Memory Access (NUMA)*
- Linking SMPs
- One SMP can directly access memory of another SMP
- Not all processors have equal access time to all memoriues
- Memory accesses across link is slower
- ![[Pasted image 20260116133245.png]]

**Hybrid Distributed-Shared Memory Acrhictecture**

![[Pasted image 20260116133412.png]]

- Both shared and distributed
- Shared memory component: cache coherent SMP node
	- Can access that node's memory as global
- Distributed memory in networking of multiple SMP nodes

- Good scalability but increased programmer complexity
- Most powerful HPC systems are hybrid
