#IntroToCompArch
# Architecture  & Organization

- Architecture are attributes that have direct on impact on logical execution of a program
	- Visible to programmers
	- ISA, number of bits for data types
	- Ex: Support division operation
- Organization is invisible to developer, it's the operation units and their interconnections
	- Control signals, interfaces, memory topology
	- Ex: Specialized division unit or use of subtract

- Previously concerns on:
	- ISA Design
	- Decisions regarding: registers, addressing, operations, etc
- Real world: Specific requirements of target machine
	- Design to maximize performance with certain constraints. Includes ISA, microarchitecture (same as com org), hw

# General Overview of Comp Org

High level program (like in C) goes to assembly (through compiler), goes to binary (through assembler)

- ISA takes you from software to hardware
- Program performance and hw performance improvements are main concerns
- Parallel processing consequences?

## Performance of a Program

Need to understand components that affect performance

- Algorithms: Number of high level statements and I/O operations
	- Time/Space complexity
- Programming Language, compiler, and architecture
	- Machine level instructions for each statement
- Processor and memory
	- How fast instructions can be executed --> Speed determined by the clock speed
- I/O Subsystem (hw and OS) --> How fast I/O ops are executed


# Von Neumann Architecture

Simple computer design
- Bus connecting processor, memory, input, output
- First concern is memory. No instruction memory separate from data
	- PC points to instruction. Fetch from memory in instruction like `t = v[k]` 
	- Processor has ALU and Registers

## Stored-Program Concept

- Instructions and data stored in memory as data

![[Pasted image 20250827215031.png]]

## Key Components

### Datapath

- Collection of functional units
	- Data processing (ALU, Multipliers)
	- Registers --> with something like 8 flip flops
	- Buses
- Registers quickly accessible location
- Bus

### Control

- Cycle of every instruction

![[Pasted image 20250827215647.png]]

### Single Bus organization

- Address registers and data registeres that map to memory
- All instructions need to share bus. Inefficient, many clock cycles

# Design Considerations

- What operations are supported?
	- Operands for those operations
- How instructions are represented in memory?
- How to represent data elements (operands)
- How to reference memory?
- Next instruction?

## Instruction Set Architecture

### Class of ISA

- General purpose registers
- Register memory vs load/store (Where is data from?)
	- On register, you can get from reg or get directly from memory
	- Load-store always goes through register, always put memory data in reg and get from there

- RISC-V Registers
	- 32 general purpose registers:

![[Pasted image 20250827224055.png]]

Saved register --> values saved to stack and reloaded back into the reg when function returns. In example below, that's why you use temporaries (t0-t3)

![[Pasted image 20250827224733.png]]

## ISA - Memory

- Memory Addressing:
	- RISC-V: byte addressed, aligned accesses faster --> can reference any individual byte
	- Word addressed, points to whole block (4 bytes)
	- Other examples: offset (displacement relative to some base location), autoincrement, indexed, PC-relative
- Types and size of operands (8-bit, 32-bit, 64-bit)
- Addressing modes:
	- Register ex: ADD R4, R1
	- Other ex: ADD R4, 3 (Immediate)
	- Displacement: ADD R4, 100(R1)

## ISA - Operations and Control Flow

- Operations
	- Data transfer, arithmetic, logical, control, branch, etc.
- Control flow instructions
	- Use of content registers (RISC-V) vs. status bits (ARM, x86)
	- Branching. If condition met do something, if not, something else

## ISA - Encoding

- Fixed (RISC-V, ARM v7/8) vs Variable length (x86)
- R-type, I-type, S-type, B-Type, U-type (32 bits as immediate value), J-type
- Distinguished by opcode and function that allows you to determine which instruction you are tying to execute
- Structure design for optimizing the cycles for consuming instructions
- Ex of encode:
	![[Pasted image 20250827230922.png]]


