#HWSpeculation 

## Operation in ROB

CDB line goes to buffer and from there to reg file
![[Pasted image 20251018025351.png]]

- Hold instructions in FIFO order -- as issued
- When instructions complete, results placed into ROB
	- Supplies operands - more registers similar to RS (reservation station)
	- Waiting operands tagged with ROB buffer number instead of RS
	- Conceptually, reg renaming moved to reorder buffer
- Instruction commit - value(s) at the head of ROB placed in registers
	- In-order commit
- Easy to undo speculated instructions on mis-predicted branches or on exceptions
- ![[Pasted image 20251018025655.png]]

## Speculative Tomasulo's - Stages

- Issue
	- Get next instruction from head of instruction queue
	- Check for available reservation station & reorder buffer slot
		- Issue instruction and send operands & **ROB number for destination**
			- Also called dispatch
- Execute
	- Same as before, operands available => execute. If not, monitor CDB for result
- Write Result
	- Write result on the CDB (if available)
		- Into ROB & any reservation stations waiting on the result
		- Update ROB right after result available because instruction can be issued later for reservation station to use, instead of register file
- **Commit (also called completion or graduation)**
	- **Instruction at head of ROB & result available => update register with result and remove instruction from ROB**
	- **Mispredicted branch flushes ROB; restarted from correct successor**

### Diagram for Tomasulo with Reorder Buffer

![[Pasted image 20251018031330.png]]

No need for another buffer for store

Destination in reservation stations are the the value of the ROB number

Example:
![[Pasted image 20251018031708.png]]

Execute a load --> stop having only immediate and have reg in load buffer
![[Pasted image 20251018031925.png]]

When in execution, remove from the buffer and put in write state

- Once done executing:
  ![[Pasted image 20251018032027.png]]
- All values updated and update reorder buffer
- Those instructions have to wait on write state, as branch is not done yet

- For instructions before the branch, can commit right after write stage if at the top of the queue. Update register file and remove from ROB

**The ROB is a circular queue. That way you don't insert things at the top when new instructions come and you keep the fixed size**

Point where prediction is correct:
![[Pasted image 20251018051416.png]]
- Speculative instructions are then committed.

If branch prediction was wrong:
- All speculative get flushed
- Add successive instruction to head of ROB

For exceptions:
- Hold handling exceptions until instruction is at the head of the buffer
