#ProcessorPipelining 

## Simple RISC V Implementation

### Instruction stages
- Instruction Fetch:
	- IR <-- Mem\[PC]
	- NPC <-- PC + 4 : This is the next PC, you are getting PC + 4 in advance, which is what's used the most
- Instruction decode / Register fetch:
	- Decode: set control signals
	- Load the values onto registers
	- A <-- Regs\[rs1]
	- B <-- Regs\[rs2]
	- Immediate field of Instruction register gets sign extended (upper 12 bits)
- Execution/Effective address (EX): Can be one of
	- Mem reference: add base address and offset
	- Register-register ALU op
	- Reg-immediate ALU
	- Branch
		- ALUOut <-- NPC + (Imm << 2)
		- Cond <-- (A == B)
- Memory Access/branch completion (MEM)
	- Memory reference
		- Load Memory data Reg (LMD) Mem\[ALU_out] (for load) or MEM\[ALUOut] <-- B (for store)
	- Branch
		- If condition, update PC
- Write-Back (WB)
	- reg-reg or reg-imm: Regs\[rd] <-- ALU_Out
	- Load: Regs\[rd] <-- LMD

### Datapath
![[Pasted image 20250925213619.png]]

### Basic Pipeline

**Introduce Pipeline Regs**

- Ensure data doesn't get overwritten
	![[Pasted image 20250925214003.png]]

Pipeline registers also have control signals

## Pipeline Hazards

- Prevent next instruction from executing in designated clock cycle
	- Structural Hazard: HW cannot support --> add hw to solve
	- Data Hazard: Instruction depends on result of prior instruction still in pipeline
	- Control Hazards: pipeline of branches and other instruction that change the PC
- Common solution is to stall the pipeline until hazard is resolved, "inserting a bubble" --> more latency

Structural when two instructions try to read from memory at a time, you can have bubble or two memories.

--> For regs, no structural always because write is in first half of clock cycle and read on the second

### Basic Block

Sequence of instructions with no embedded branches (except at end) and no branch targets (except beginning)

- Instruction within basic block are likely to depend on each other
	- Amount of overlap can be exploited within basic block is likely less than average block size
- Exploit parallelism (ILP) across basic blocks

### Dependencies

- Understanding dependencies between instructions is important to exploit ILP

#### Types
- Data dependences: Instruction i produces result that may be used by instruction j 
	- If k depends on j, it also depends on i
- Name dependence:
	- Anti-dependence: Instruction j writes a register or memory that i reads
		- Ordering must be preserved so i reads right value
	- Output dependence: i and j write to same reg or memory location
		- Ordering must be preserved to ensure memory is consistent
	- Control dependence: Ordering of i with respect to branch instruction so program executes as expected

### Data Hazard

- Pipeline changes order of read/write access to operands
- Three types

**Read after Write**
- Read a register x by instruction j occurs before write of register x by i
- Hazard: Instruction j would use wrong value if not handled
**Write after Read**
- Read reg x by i after write of reg x by j
- Hazard: i would use wrong value
**Write after Write**
- Write of reg x by i occurs after write of reg x by j
- Reg x could keep wrong value if not handled

#### Example

![[Pasted image 20250925220224.png]]

All instructions depend on R1 --> Read after write

- Or and XOR are fine in this case
- Stalls in this case stall the whole pipeline
- Instructions before the pipeline will continue executing so no deadlock

### Forwarding

**Do not wait until result is stored**

- Requires additional connections and control logic in dp

- Forward the ALU results from EX/MEM and MEM/WB pipeline regs to ALU Inputs
- Forwarding hw detects if previous ALU op has written reg corresponding to source of current ALU op. Control logic selects fw result as ALU input rather than value read:
	![[Pasted image 20250925220859.png]]
In the second case, EX/MEM contains result of subtract
- Need to forward from MEM/WB

Second example (LD into SD)
	![[Pasted image 20250925221102.png]]

### Hazard Requiring Stalls

Load - Use: Pipeline interlock detects hazard and stalls pipeline

![[Pasted image 20250925221229.png]]

You need to stall, until value is available
![[Pasted image 20250925221302.png]]

## Control or Branch Hazard

- Branch determines flow of control
	- Fetch next instruction depends on branch outcome
- Taken branch if PC changes to target address
- Non taken if PC is PC+4

To handle, branch successor does two IF
	![[Pasted image 20250925221533.png]]

### Reducing Branch Penalties

#### Static

- Freeze or flush pipeline
	- Hold or delete any instruction that follow a branch 
- Treat branch as non taken
	- Must not change processor state until branch outcome is known
	- Complex logic to know when state might change and how to revert
- Treat every branch as taken
	- Start fetching and executing the target as soon as address computed
	- Once-cycle improvement when taken
- Delayed branch
	- Add instructions after branch instruction
	- Instructions executed whether or not branch is taken

**Delayed branch**
	You need to make sure instruction isn't affecting state and is useful
- Instructions following branch are added to delay slots
- Use time while determining if taken to execute another special instruction

- Static scheme relies on information available at compile time
- Use information collected before to predict branches

#### Dynamic

- Branch-prediction buffer or branch history table
	- Small memory indexed by lower portion of the address of branch instruction
	- Bits to indicate if branch was taken or not
- Another branch with same low-order bits can affect outcome
- 1-bit prediction scheme
	- Use only one for taken or not
	- Even if almost always taken, scheme will predict incorrectly twice
		- Miss causes flip
- 2-bit prediction scheme
	- Has to be taken twice
	- FSM:
		![[Pasted image 20250927233835.png]]

## Control in RISCV

- Two muxes in EX stage depending on instruction type
- Top for branches, bottom for reg-reg ALU or another type
- Mux in IF chooses PC+4 or branch target]
- Mux in WB stage based on load or ALU

- Instruction issue: Moves from ID to EX
	- Has been issues once you checked for hazard
- All data hazards checked in ID phase
	- If data hazard, stall, and enable forwarding to determine which data
- Detecting interlocks simplifies implementation

- Stall is just a no-op (can be add 0 + 0)

**Situations**
- No dependence, dependence requiring stall, dependence overcome by forwarding, dependence with accesses in order

**Control Load Interlock**
- Load use hazard
- Check if either src reg match with destination reg
	![[Pasted image 20250928121543.png]]
	- Stall to fix

**Control - Forwarding logic**
![[Pasted image 20250928121648.png]]
- Compare src to destination register, forward accordingly