
#Tomasulo 

### WAW Hazard

No overwriting because the registers are renamed to reservation station tag:

![[Pasted image 20251018003945.png]]

F0 not updated when multiplicaiton executes, since we know which reservation station writes to the reg

When in one of the Qs, fetch value from bus, not register

## Tomasulo's Algorithm Examples

Full Algorithm:

![[Pasted image 20251018021332.png]]

**Loop Based Example**

- Predict branches taken
- Loop unrolled dynamically by hardware
- Assume all instructions have been issued (2 iterations in this case)
- ![[Pasted image 20251018022314.png]]
- Can do the loads instantly thanks to reservation station tags instead of registers

### Loads and Stores in Tomasulo's

- Handling out of order loads and stores
- Accessing different addresses? --> no problem
- Accessing same address --> WAR and RAW hazards
- Two stores to the same address --> WAW
- Before executing load, check if any uncompleted store that precede load in program order
	- Check if stores share same data memory address as load
- Before executing store, check if any uncompleted loads that precede in program order and share memory address

**How do we detect these hazards?**

- One way is to perform effective address calculations in program order
	- Not optimal
	- You basically don't calculate effective address until other ones done
- Load instruction
	- Calculate address calculation in order
	- Check if any address conflict by examining A field of all active store buffers
	- If conflict, stall load until conflicting store completes
- Store instruction
	- Check both load and store buffers for conflict
		- Stores cannot be reordered with respect to either load or store

Note: No tag for store buffers because stores cannot give you a result, not needed

## Tomasulo's CDB & Use

- Performance can be limited by the single CDB
- Additional CDBs can be added
	- Increases required hardware
	- Must interact with each reservation station
	- Must include associative tag -matching for each CDB at each RS

- Modern day use of Tomasulo's algorithm
	- Presence of caches and undpredictable delays motivate dynamic scheduling
		- Out of order execution is desired featured
	- Non-numeric codes, difficult to schedule, can benefit from Tomasulo (reg renaming, dynamic scheduling)
		- Speculation is also important
	- Helps good performance without compiler involved

## Hardware-Based Speculation

#HWSpeculation

- Tomasulo with prediction cannot overcome control dependence
	- Fetch instruction but don't issue or execute until branch is resolved
- Speculation overcomes this limitation

**Speculation: Execute instructions along predicted execution path but only commit the results if prediction was correct**

- Instruction commit: Allow instruction to update the reg file when instruction is no longer a speculation
	- Additional hardware to prevent irrevocable action until an instruction commits
		- Updating state or taking an exception

### Speculation for improving ILP

- HW-based speculation:
	- Dynamic branch prediction -> choose instructions to execute
	- Speculation --> Allow execution of instructions before control dependences are resolved
		- Must have ability to undo effects of incorrectly speculated sequence
	- Dynamic scheduling --> Deal with scheduling to different combinations of basic blocks

## Adding Speculation to Tomasulo's

- Need possibility to revert changes
- Separate bypassing the result among instructions from instruction completion
	- Additional step --> Instruction commit

- Update memory/reg file only when instruction is no longer speculative
	- Allow executing out-of-order
	- Force committing in-order

- Reorder Buffer needed (ROB): Set of buffers that hold results of instruction that have finished execution but haven't been committed
	- Also used to pass results among instructions that may be speculated
	- No need to get results from reg file, you can continue speculative branch

### Reorder Buffer (ROB)

- Reg file not updated until commit
- ROB stores results while instruction still speculative
	- Source of operands
	- ROB extends architectural registers like RS
	- Similar to store buffer in Tomasulo
		- Store buffer can be integrated into ROB

#### ROB --> Field

- Instruction type:
	- Branch - no destination result
	- Store - memory address destination
	- Reg operation - ALU or load - reg destination
- Destination - where instruction result should be written
	- Register number - loads and ALU
	- Memory address - stores
- Value: value of instruction result until instruction commits
- Ready: indicates instruction completed exec and value ready


