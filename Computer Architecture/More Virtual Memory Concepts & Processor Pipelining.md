#VirtualMemory 

When running program, OS allocates virtual address space
![[Pasted image 20250921115036.png]]

PC to base of text (where instructions are stored)

- Text: program code
- Static data: global variables, constant stuff
- Dynamic data: Heap (malloc in C)
- Stack: automatic storage

When starting function, store return address and any other data you need

- When program is executed, OS uses the loader to allocate virtual address space
- Sets the appropriate registers to execute the program
- Populates page table for the program and when the pages are allocated frames in the physical memory

## Fragmentation & Relocation

- Fragmentation: Areas of memory space become unavailable
- Relocation: Move program or data to new region of address space (possibly fixing all pointers)
- External fragmentation: Can't store some block because some little space is being wasted
- Intel came up with frames (variable sizes) to fix
- ![[Pasted image 20250921120436.png]]
- Internal fragmentation: Program is not an integral number of pages
	- Part of last page frame is wasted
	- ![[Pasted image 20250921120623.png]]
	- Can't store another program there
	- External fragmentation is a bigger problem

### Optimal Page Size

- Choose page that minimizes fragmentation
- Large page size --> Internal fragmentation more severe (unused memory)
- Increase in # of pages/name space => larger page table
- In general, larger page size used because memory gets larger as price of RAM drops, and gap between processor speed and disk speed is wider
- Most machines use 4k - 64k byte pages today

### Page Replacement Algorithms

- Similar to cache block replacement
- LRU: Candidate page is one used in distant past
	- Good performance
	- Hard to keep track

#ProcessorPipelining
## Instruction Execution

- PC --> Instruction memory, fetch instruction
- Register numbers --> register file, read registers
- Depending on instruction class
	- Use ALU to calculate
		- Arithmetic result
		- Memory address for load/store
		- Branch target address
	- Access data memory for load/store
- PC = PC + 4 or target address
- What determines clock period?
	- Non-pipelined: slowest instruction
	- Pipelined: slowest stage

### Execution Cycle

![[Pasted image 20250921121733.png]]

- You can split in stages
### Stages in RISC Architecture

- Focus on integer subset of RISC - load-store word, branch, integer ALU operations
- Instruction Fetch (IF)
	- PC to memory; Fetch current and update PC
- Instruction Decode/Register Fetch (ID)
	- Decode instruction and read registers
- Execution/Effective address cycle (EX)
	- ALU operates on the operands performing:
		- Memory reference: adds the base register to offset to form address
		- Register-register or register to immediate op
		- Conditional branch
- Memory Access (MEM)
	- Load --> reads from memory with effective address
	- Store --> Writes to memory from second reg using effective address
- Write Back (WB)
	- Write result into reg file
		- From load
		- From ALU

**CPI = 1** : One cycle per instruction
![[Pasted image 20250921123216.png]]

- Longest delay is clock period
- Not feasible to vary period for different instructions

### RISC Pipeline

- Simplification - Single clock cycle pipeline
	- Each stage 1 clock cycle
![[Pasted image 20250921123345.png]]

- Optimizing throughput of the program
- Each instruction still takes the same time

### Datapath of RISC Pipeline
![[Pasted image 20250921123558.png]]

- Data memory and Instruction memory separate allows access to both at same time without hazard

### Performance Issues With Pipelining

- Pipeline increasing instruction throughput but no improvement on individual instruction
- Imbalance among pipe stages reduces performance: Clock can only run at slowest pipeline stage
- Pipeline overhead:
	- Pipeline register delay
	- Clock skew
		- Delay between when clock signal arrives at two different registers
	- Registers between stages:
		- ![[Pasted image 20250921124100.png]]
		- Temporary buffer that the other stages can use, so stages don't have to wait

