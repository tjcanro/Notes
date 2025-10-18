#DynamicScheduling

Possibly read the putting it all together section of the book

## Supporting Multi-cycle operations

Separate functional units for integers and FP operations
![[Pasted image 20251009063358.png]]

Issue is having instructions that take up the functional units for too long
- Subsequent instructions start piling up

Solution: Make floating point operations a pipeline too

- ![[Pasted image 20251009065031.png]]
- When instruction is using the adder, move the pipeline as well
- This splits the FP operation units into more, smaller units

- Multiplication 4 cycles. Division 24 (can change but clearly division is way longer)

- Ex:
- ![[Pasted image 20251009065311.png]]
- Divide not pipelined. Possible structural hazard
- They complete out of order. Could possibly have more than one write in a given cycle
- Various run-times, instructions don't reach WB in order
	- Write after write hazard possible
- Handling exceptions can become problem since instructions can complete out-of-order
- Order is the one that causes most issues

You sometimes have performance mode where you tolerate exceptions being handles out-of-order
## Disadvantages with Compiler Scheduling

- Compiled code is efficient for given pipeline
	- Different binaries for different microarchitectures, even if ISA is the same
- Some cases of dependency will only be resolved on runtime
	- Memory reference or data-dependent branch, or result of dynamic linking
- Cache miss can result in several cycle delays
	- Preferable to run instructions while you wait

## Dynamic Scheduling

- When data dependence is detected by compiler in in-order pipeline
	- Compiler tries to hide data dependence by re-arranging independent instructions
	- Compiler tries to exploit forwarding logic to prevent hazards
	- If data dependence can't be hidden, pipeline stalled
		- No new instructions fetched or issues until dependence cleared

**Hardware technique where hardware reorders instruction execution to reduce stalls**
- Must maintain data flows and exception behavior

- Increases hardware complexity

**Advantages**
- Eliminates need to have multiple binaries and recompile code for different microarchitecture
	- Allows code compiled for one pipeline to run efficiently on another
- Capability to re-oder at runtime: handle runtime dependencies
- Tolerate unpredictable delays
	- Can wait for cache to return data, for ex
- Compiler pipeline scheduling can be used on processor with dynamic scheduling

#### Example


![[Pasted image 20251009075903.png]]
- Read-after-write hazard

Without dynamic scheduling, sub can't execute, because of dependence of fadd on ddiv causing a stall
- fsub doesn't have any data dependencies
- At ID stage, structural and data hazards can be checked
	- Instruction is "issued" when data hazards, if any, resolved
- Process of issuing instruction can be split in two:
	- Check structural hazards
	- Wait for absence of data hazard

Instructions issued in order, however, instructions can execute as soon as the data operands are available
- Out of order execution and out of order completion
- Out of order execution introduces WAR and WAW hazards
- ![[Pasted image 20251009080515.png]]

### Exception Handling in Dynamically Scheduled pipelines

- Delay notification until processor knows instruction should be next completed
- Dynamically scheduled processor could generate imprecise exceptions
	- Causes processor state to be different from the state of processor when instructions executed in program order
- Imprecise exceptions due to 
	- Pipeline complete instructions later in program order than the one causing exception
	- Pipeline not completing instructions earlier in program order than the one causing exceptions
	- They make it difficult to restart execution after exception

## Pipeline to support out of order execution

- ID stage must be split
- Issue stage - Decode instructions, check for structural hazards
	- Instructions pass this stage in order
- Read operands - Wait until no data hazards, then read operands
	- Instructions may be stalled or bypass each other
	- Instructions may enter execution stage out of order
- IF stage precedes issue stage
- IF -> Issue -> Read -> Ex -> Mem -> WB
- Pipeline should allow multiple instruction in execution at the same time
	- Needs those pipelined units from before, or multiple units
