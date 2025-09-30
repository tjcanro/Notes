#ProcessorPipelining 

## Reducing Branch Penalty

Recall:
![[Pasted image 20250928211204.png]]

If branch is taken, 3 clock cycle delay

**Optimization**

- Compute target address in advance:
	![[Pasted image 20250928211331.png]]

## Pipeline and Exceptions

- Instructions executed in stages and are not completed for several cycles

**Other instructions in pipeline can raise exceptions**
- Forces processor to abort instructions before they complete
- Difficult to know if an instruction can safely change state of processor
- Exceptions, faults, interrupts used interchangeably

#### Situations that cause exceptions
- I/O device request
- Invoking OS from user program (syscalls)
- Breakpoint (debugger --> programmer requested)
- Page fault
- Memory protection violation
- Integer arithmetic overflow
- HW Failure

### Requirements on Exceptions

**Synchronous vs Asynchronous**
- Synchronous: Event occurs at same place every time program is executed with same data and memory allocation
	- Syscalls, Breakpoint, Overflow, Page fault, memory protection
- Asynchronous: Usually caused by external devices to processor and memory (Except HW malfunciton)
	- I/O, HW, power failure

**User requested vs coerced**
- User directly asks for it -- Syscalls, breakpoint
- Coerced --> caused by some hw event not under control of user program

**User maskable vs non-maskable**
- Maskable --> Event that can be masked or disabled by user task
	- Like when using try/except in C++
	- Breakpoint, integer arithmetic overflow
- Nonmaskable --> Event that cannot be masked or disabled by user task
	- All OS and memory related are not maskable
	
**Within vs Between Instructions**
- Within - Event prevents instruction completion. Usually synchronous
	- Integer arithmetic overflow, page fault, memory protection violation
	- Not guaranteed but likely: HW, Pwr
- Between - Allows for current instruction to complete: Usually user requested
	- Syscalls, breakpoint
	- Typically I/O device request

**Resume vs Terminate**
- Resume, program execution continues after event
- Terminate, program execution stops after event

Implementing exceptions that occur within instructions where instruction must resume is hard
- Another program (interrupt routine) must be invoked
	- Save current state of program
	- Correct exception
	- Restore state
	- Return control to program where instruction that caused exception is tried again
- Restartable --> Processor provides ability to handle exception and restart without affecting execution of program

**Most difficult: within instruction, must be resumed or restarted**

- What happens when page fault occurs?
	- Observed in Mem stage
		- OS intervention
	- Other instructions in pipeline in different stages
	- Pipeline must be shut down and state saved, so instruction can restart

**Steps taken**
- Force trap instruction into pipeline on next IF
	- Trap instruction redirect's control to interrupt handler
- Interrupt-routine (exception handling) invoked and executed
	- Interrupt routine saves address of the exception causing instruction in special register
- After exception handled, instruction that caused is restarted

### Handling Exceptions

- Precise exceptions: pipeline can be stopped to allow instructions before faulting instruction to complete
	- Instructions following the faulting instruction can be restarted from scratch
	- Ideal --> Faulting instruction would not have changed the state

- Modern high-performance processors provide two modes - precise exceptions mode and fast mode
	- For floating point, becomes hard. Chill on integer pipelines
- Demand paging (some pages in disk and only after certain point, page is demanded from disk)and IEEE arithmetic trap handlers require processors to handle exceptions precise
	- Either hw or software

Two exceptions:

In load then add:
- Load raised page fault
- Add raised arithmetic exception
- Solution: RISC V Precise exceptions, handle page fault first, restart execution, then arithmetic exception will happen again

What if load in MEM stage and instruction page at IF stage?:
- **Solution is Status vector for all instructions, both recorded**
- Let all instructions till the end, only check status vector in the WB stage (last one)
- Write signals disabled until exceptions handled

## Pipeline Scheduling

- In in-order processors, instructions executed as issued
- Compiler can reorder instructions at compile time to avoid hazards (data, control)
- Sequences of instructions that are unrelated and independent must be identified to exploit parallelism (ILP)
- When instructions that depend on each other, separate by clock cycles equal to pipeline latency (reoder so you don't need to insert stall)
- Depends on amount of ILP in the program and latencies of functional units in pipelines