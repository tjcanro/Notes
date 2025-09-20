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

