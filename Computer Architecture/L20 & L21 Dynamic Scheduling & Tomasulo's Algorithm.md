#DynamicScheduling 

## Scoreboarding

- HW technique to execute out-of-order when sufficient resources and no data dependences
- This HW tracks state of each functional unit as well as register usage
	- Validate structural and data hazards
- In issue stage, instruction decoded and checked against scoreboards
	- No structural hazards (functional unit available)
	- No WAW hazards
- In read operands stage, instruction waits until no RAW hazards
- In write back, check scoreboard to ensure there are no WAR hazards before write is allowed
- Doesn't handle antidependences and output dependences
	- Both WAR and WAW can be eliminated using reg renaming

#Tomasulo
## Tomasulo's Approach

- Scheme in which we dynamically rename regs to handle the two dependences
	- Designed for IBM processor with no cache and only 4 FP regs
- Can be extended to reduce the effect of control dependences (called speculation)
	- Predict outcome of a branch
	- Execute the instruction at predicted destination addr
	- Take corrective actions when prediction was wrong

**2 Key Principles**
- Dynamically determine when an instruction is ready to execute
- Rename registers to avoid unnecessary hazards

**Raw Hazards**
- Avoid by execute instructions only when its operands are available
	- Same as scoreboarding
- WAR and WAW hazards that arises from name dependences are eliminated by register remaining
- Rename all destination registers including those with pending read or write for an earlier instruction
	- Why should destination regs be renamed for earlier instructions?
- Note that in out of order execution, renaming the destination registers for earlier instructions ensures that out of order does not affect any instructions that depend on an earlier value of an operand

### Register Renaming example

![[Pasted image 20251015071916.png]]

RAW on f0 from 1 and 2. Add and store, on f6, and subtract and multiplication on f8

Anti-dependeces (WAR) on add and sub (f8) and store and multiply (f6)

**Assume S & T two temp registers**
![[Pasted image 20251015072152.png]]
WAR has been removed

- Subsequent uses of f6 don't have to be replaced by S because latest value is in f6.
- Subsequent use of f8 does have to be replaced by T
	- There could be intervening branches between preceding code and later use of f8
	- Compiler analysis of hw support

- Tomasulo's can handle renaming across branches

---
## Out-of-order Execution, Generally

- Instruction Queue (FIFO)
- Bus that connects to units, like FP Adders, FP multipliers, Mem Unit
- Assume independent instructions

- Is unit free?
- Dependeces?

In example, assume load is 1 cycle, add is 3 clock cycles, Mul is 6 clock cycles, Div is 12 cycles

- Block of functional units while an instruction completes, if it takes longer clock cycles
- Instruction 4 is stalled
![[Pasted image 20251015073147.png]]

Hypothetical scenario:
- If compiler smart, execute other instructions in the meantime and then execute the necessary one

**Solution: Use Buffers**
![[Pasted image 20251015073421.png]]

## Reservation Stations

- Buffers associated with functional units
- Each functional unit has reservation station
- Reservation station fetches and buffers an operand as soon as available
	- Eliminates need to get operand form register
- Pending instructions designate the reservation that will provide their input
	- Tags used to identify the reservation station

Ex of tags:
```
add f0, fi, f2 --> Add_1
.
.
.
mul (mul_1) f5, f1, f0 -- Becomes --> mul f5, f1, Add_1
```

- Register specifiers are renamed to the names of reservation station (tags) when the instructions are issued

**Important Properties**
- Reservation stations at each functional unit determines when an instruction can begin execution at that unit
- Result are passed directly to functional units from reservation stations

## Tomasulo's Implementation

![[Pasted image 20251017062239.png]]

Instructions have been issued and waiting on operands being ready
- Operands assigned to a specific tag
- Check operand ready, pull from FP reg
- Tag can be name of instruction that will be executed, that way you can know if ready
- **Once adder is done, through result to common data bus (CDB). Can go to register, any reservation station, or to store buffer**
- **Eliminates need to go back to reg file to read values, as it can immediately go to reservation station**

- Load buffer and store buffer hold address. Store also holds data
- Address then computes effective address

- Two busses on FP registers because one for each operand

## Tomasulo Pipeline

Excluding IF stage:
- Issue:
	- Get next instruction from head of instruction queue
	- Check for available reservation station
		- If not, stall because of structural hazard
	- Track functional units that will produce operands (if operands not in regs)
- Execute
	- If all operands available, execute operation at corresponding functional unit
	- If operands not available, monitor CDB while waiting --> On dependence, instead of reg name, have tag. When instruction can see tag is available, append operand and start operation
	- ![[Pasted image 20251017064435.png]]
- Write result
	- Write result on CDB, if CDB available
		- Into registers and any reservation stations waiting for the result
	

RAW eliminated by just waiting until result is available, in this case

- If independent instructions are ready, additional instructions can execute at the same time

**Components**
- Reservation stations
	- Handles distributed hazard detection and instruction control
- Tag fields
	- Names for extended set of virtual registers used for renaming
	- 4-bit long to specify reservation station or load buffer (bits to address sum num of reservation station and entries of load buffer)
- Register specifier
	- Register names that are used in instructions
		- Discarded when instruction issued to reservation station
	- Used to assign tags
	- Tags allows eliminate dependences and out of order execution

## Reservation Station Fields

- OP - (Opcode)
- Qj, Qk --> Tag of the reservation stations that will produce the source operand
	- Value of 0 indicates source operand is already available
- Vj, Vk --> Value of source operands
	- Only one of V or Q fields is valid for each operand
	- Vk is used to hold the offset field for loads
- A --> memory address for load or store
	- Initially immediate value, later becomes effective address that has been calculated
- Busy flag --> if reservation station is occupied
- Register File field:
	- Qi --> tag of reservation station from which result will be stored in this register

## Advantages of Tomasulo

- Distributed reservation stations and CDB
	- Multiple instructions waiting on a single result, while already holding other operand, can be released simultaneously
		- Broadcasting result on CDB
- Eliminate WAW and WAW hazards by renaming regs using reservation stations
	- Store operands in reservation station as soon as available

### Example

![[Pasted image 20251017070035.png]]

Note that Vj cannot be active at same time as Qj. Same happens with the Ks. This is because operand can either be tag or operand value

**Register status is where the last or latest value will come from**

-> Division and addition can be issued together, even when there is a WAR hazard
	This is because the reservation station will know that the value for an operand will already be captured. No need to worry about what is in the reg file

- Instructions waiting will get value at the same time