#HBM
#Cache

## HBM

Use of DRAM interposed with CPU, for reduced latency and improved bandwidth

- Can we use a larger cache in this case? (128MiB to 1GiB)
- Smaller blocks require substantial tag storage
- Larger blocks could be inefficient --> introduces fragmentation

Approach (L-H):

- Each SDRAM row is a block index
- Each row contains a set of tags and 29 data segments
- 29-set associative
- Hit requires CAS

SRAM-Tags:
- Store tags in separate module in SRAM

Alloy Cache:

- Mold tag and data together
- Use direct mapped

Both schemes require two DRAM accesses for misses:
- One for tag and one for data
- Solutions:
	- Map to keep track of blocks
	- Predict likely misses

HBM performance comparisons:
![[Pasted image 20250918222413.png]]

#VirtualMachines

## Virtual Machine

Each VM is a computer on its own: Includes its own OS, Binary/Library and Apps
- Different VMs talk with Hypervisor
- Treated as different machines
- Allows complete isolation
- VM OS is called Guest Operating System

Hypervisor: Virtual machine monitor
- Does translation from instructions of VM to instructions on the Host

- Host emulates resources
- Improved isolation, while avoiding security issues and sharing of resources
- Slight performance impact
### Virtual Machine Monitor or Hypervisor

- Maps virtual resources to physical resources
	- Memory, I/O devices, CPUs become virtual
- Guest code runs on native machine in user mode
	- Traps to VMM on privileged instructions and access to protected resources
	- Hypervisor takes care of privileged instructions
- VMM handles real I/O devices
- XenVm --> knows guest OS is running on VM
- On servers, since Hypervisor is very simple and robust, you don't need the Host OS layer, Hypervisor can talk directly to hardware

### Instruction Set Support

- User and System modes
- Privileged instructions (everything that has to do with hardware) only available in system mode
	- Trap to system if executed in user mode
- All physical resources only accessible using privileged instructions
	- Including interrupt controls, page tables, I/O regs
- More support for virtualization is being added (like the introduction of supervisor user mode)

#VirtualMemory

## Virtual Memory

### Memory addressing - Physical

- We considered addresses of loads/stores go to caches/memory
- Complicated when multi-processes or multi-user
	- Assign addresses in a program so you know for sure no conflicts

Illusion of very large memory
- Sum of the memory of many jobs is greater than physical memory
- Address space of each job larger than physical memory

Basically combine RAM and disk, from the POV of programs
- Always available (fast and expensive) physical memory to be efficiently utilized
- Simplifies memory management and programming
- Exploits memory hierarchy to keep access time low

Involves at least two storage levels: main and secondary
- Main (DRAM): nanoseconds, M/GBytes
- Secondary(HD): milliseconds, G/TBytes

**Virtual address: used by programmer**
**Virtual address space: collection of addresses**
**Memory Address: Address of a word in physical memory. Also known as "physical address"**

Ex:
![[Pasted image 20250918225519.png]]

- Software and hardware for no conflicts
- OS keeps track of software translation tables
- Hardware --> Cache recent translations

### Basic  Issues with Virtual Memory Design

- Size of blocks (pages *Pages are bascially virtual blocks*)? transferred from secondary to main storage
- Page brought to main, if main full some page must be released to make room for new page
	- Replacement policy
- Missing page fetched from secondary memory only on page fault
- ![[Pasted image 20250918234959.png]]

### Address Map

Virtual address space bigger than physical

- MAP: V --> M U {0} address mapping function
- MAP(a) = a' if virtual address a present in physical address a' in M
	- 0 if data not present in M (need to allocate address in M)
- Flow:
![[Pasted image 20250921013131.png]]

**Some virtual addresses are in physical address, some might live in disk**

#### Paging Organization

- Typically 4Kib to 64 KiB in size
- In this case fixed, it's possible to have varying size pages
- Page size is unit of mapping, as well as unit of transfer from physical to virtual

- Address mapping:
	- All 64 bits usually not used, memory not that big
	- Virtual address split in page no. and page offset
	- If 1k page size, 10 bit page offset
- OS Keeps page table, stored in memory, which defines 
- Page table: what pages are available in memory and where to find
- One page table per process, since context switch needs to also switch page table
- Start of page table stored in special register
	- ![[Pasted image 20250921094405.png]]
- Page table storing valid (disk or memory), address rights (read/write), and physical address
- Page offset can be used as-is

Ex:
	![[Pasted image 20250921094447.png]]

### Address Mapping Algorithm

- Where is page stored? --> valid bit
- Access rights --> R,R/W, X
- Protection violation fault: Hardware raises exception
- Page fault:
	- Causes trap
	- Context switch - current process suspended while page is fetched from secondary storage: page fault usually handled in software by OS because page fault takes million+ cycles

## Hardware/Software Interface

- What checks does processor perform during load/store memory acess
	- Effective address computed in pipleine is virtual
	- Before accessing memory, must perform virtual-physical mapping
		- Critical to performance 
	- If there is a valid mapping, load/store proceeds as usual, adresss sent to cache, DRAM is the mapped address
	- If no valid mapping, or protection violation, processor doesn't know how to handle
		- Throw exception
		- Save PC of instruction so it can be retried later
		- Jump into an OS exception handling routine
		- Each OS has its own routines

## Virtual Address and Cache

![[Pasted image 20250921095417.png]]

One more access to translate VA to PA
- Cache access very expensive, this "innermost loop" that you want to go as fast as possible
- Why access cache with PA?
	- VA caches have *aliasing* problem
		- Two different virtual addresses map to the same PA
		- Two different cache entries holding data for the same PA

### Translation Lookaside Buffer (         TLB)

- A way to speed up translation to use special cache or recently used page table entries
- Dirty bit: the frame in main memory has been updated
- Valid: mapping is valid
- ![[Pasted image 20250921100448.png]]
- TLB access time shorter than cache access time
	- Much less than main memory access time
	- Very small miss rate
- ![[Pasted image 20250921100722.png]]
- TLB hit means data exists in main memory for sure
- TLB can be fully associative, set associative or direct mapped
- Usually small, permits large/fully associative
- ![[Pasted image 20250921100846.png]]

Overall picture:
![[Pasted image 20250921100920.png]]

### Overlapped TLB Access
(Referring to TLB fetch at the same time as L1 cache address)
- Overlapped only works as long as address bits used in index into cache do not change as result of VA translation
- Limits things to small cache with large page sizes, or high n-way set associative caches if you want large cache
- In this case, it's possible only because L1 Cache is virtually indexed and physically tagged. If physically indexed, can't overlap
- Use virtual address to find cache
- Index bits <= page offset

