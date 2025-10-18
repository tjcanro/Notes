
#ProcessorPipelining 

- In in-order processors (static scheduling) hardware doesn't rearrange instructions. Compiler takes care of that
- Compiler reorders to avoid hazards
- Compiler finds instructions that are unrelated and independent to exploit instruction level parallelism
- Dependent instructions must be separated, possibly, by clock cycles equal to pipeline latency. If not possible, stall
- All depends on amount of ILP possible on program and latencies of functional units in pipeline

Ex:

In order:

![[Pasted image 20251008064712.png]]

Need to wait for data to be available in this case
- Nothing to be done on stall because all instructions depend on x1. No ILP
- Only one stall because of data forwarding

If more instructions, without dependency, you can move around to avoid the stall
- Look for places without hazards, and re-arrange, to give data time to be available
- Make sure operands don't depend on values from before

## Compiler Techniques for ILP

Consider a loop:
![[Pasted image 20251008065438.png]]

![[Pasted image 20251008065450.png]]

- Array added to a scalar
- X1 contains address of last element of the array
- X2 contains address of first element in array
- f2 is the scapar

#### Pipeline stalls

Latency table:
![[Pasted image 20251008065917.png]]

Without scheduling:
![[Pasted image 20251008070000.png]]

8 cycles for one iteration

With scheduling:
![[Pasted image 20251008070033.png]]

Save 1000 clock cycles by moving instructions around

- 2 clock cycles do work
- 4 clock cycles are loop overhead (addi, bne, stalls)

#### Loop Unrolling
Replicate loop body multiple times and adjust the loop termination code

- Improves scheduling: Instructions from different iterations scheduled together
- Eliminate data use stalls (load-use) by creating independent instructions in loop body
- Use different registers for each iteration

**How much to unroll:**
- Loop runs n times - generate k copies of body
- Pair of consecutive loops generated: main loop and remainder loop
- Main loop runs n/k times
- Remainder loop runs from 0 to (n mod k) (if division is not mod 0)
- When n is large, most of the time will be on main loop

In previous example, with a k=4:
![[Pasted image 20251008071020.png]]

Since you can drop the addi and bne, also reduces stalls

- You use a lot more registers

![[Pasted image 20251008072937.png]]

- Once optimized, instructions can be moved a lot more

**Process of unrolling and scheduling must be methodical**
- Look into how confition check is handle
- Determine the loop iterations are independent before unrolling
- Use different registers for different computations
- Eliminate test and branch instructions that become unnecessary
- Determine which loads and stores can be interchanged
	- Ensure independent memory addresses
- Ensure code executes the same without scheduling

**Limiting factors**
- Decrease in amount of overhead amortized with each unroll
- Justify the use of more registers
- Code size limitations: especially for large loops

#BranchPrediction

## Predicting Branches - Dynamic

### 2-bit prediction scheme

![[Pasted image 20251008073942.png]]

- Tracks recent behavior of a single branch
- Sometimes behavior of branch can depend on behavior of other branches in the program

### Correlating Branch Predictors

- Use behavior from other branches to make a prediction
- Ex from benchmark
- ![[Pasted image 20251008074354.png]]
- If neither b1 or b2 taken, then b3 will be taken
- Global information: 00,01,10,11 --> NT/NT, NT/T, T/NT, T/T
- It can mantain table of entries containing 2-bit predictor to help determining in b3 taken or not
	- Use lower order bits from PC for b3 and the information mantained about b1 and b2 to index in the table
	- Based on how other branches perform, improve predictor
	- Number of entries is how many distinct branch address indexes you track

- (m, n) predictor uses behavior of last m branches to chooses the n-bit predictor
- 2^m predictor choices to choose from
- ![[Pasted image 20251008075101.png]]
- 4 bits from lower PC
- 2 bits for global information maintained on the two branches
- Index = 2-bit + 4-bit values
- Each predictor pi is 2 bits long

**Efficiency of correlating predictor**

- Predictors that use same number of state bits
- Bits as index formula:
- ![[Pasted image 20251008075342.png]]
- ![[Pasted image 20251008075507.png]]


### GShare Predictor

![[Pasted image 20251008075807.png]]

- Often used as a baseline

### Tournament Predictor

- Uses global predictor and local predictor and chooses between them using a selector
- Three tables: global, local, selector
- Global predictor -- branch history to index the predictor
- Local predictor -- branch address
- 2-bit saturating counter per branch to choose among predictors
	- Predictor that was most effective in recent predictions is chosen
- Better accuracy in medium sizes

![[Pasted image 20251008083222.png]]

Pis (and Gis) from global predictors, are two-bit predictors

- Selector table is also two bit predictor to choose global or local predictor
- m-bit shift reg firts into branch history
- bi - PC into selector and local predictors
- Whatever the prediction: if taken, update local predictor. If not, update selector and local predictor, to update state

#### Predictor Performance

![[Pasted image 20251008083620.png]]


### Tagged Hybrid Predictors

Use series of global predictors indexed with different length histories
- 5 component tagged hybrid predictor has 5 prediction tables
	- P(0), P(1) ... P(4)
	- ![[Pasted image 20251008083939.png]]
	- P(0) is like local predictor
	- P(1) only looks at one bit from the history
	- Second table, 2 bits, and so on
	- You also have a tag 
	- Tag is branch addressed hashed with history bits
	- Each predictor can be standard 2 or 3 bit predictors
		- 3 mis-predictions to change prediction for 3 bit
- Always chooses the one with highest number of history bits, that's matching
- Prediction of given branch is predictor with longest branch history that also matches tags

### Initializing Predictors

- Large predictors can be initialized randomly
	- Will take a while for predictor to be useful
- Valid bit can be included
	- Can use hint bit for taken or not
	- Used before dynamic prediction was a thing
- Based on branch direction
	- Forward not taken
	- Backward taken
- Can have impact on prediction performance, especially on smaller programs and larger branch mis-prediction penalties

**Performance**
![[Pasted image 20251008085009.png]]

## Intel i7 Branch Prediction

- Due to deep pipelining and multiple  issues per clock, between 30 and 256 instructions in pipeline
	- Prediction is crucial
- i7 920 used two level predictor
	- Smaller first level predictor for cycle constraints
		- Predict branch every clock cycle
	- Larger predictor as backup
- Each predictor combines 3 predictors
	- 2-bit predictor
	- Global history predictor
	- Loop exit predictor
		- Counter to predict number of loop iterations
	- Best predictor chosen among the 3 by tracking accuracy of each
- Global and 2-bit maybe replaced with tagged hybrid predictor
- Separate unit for target addresses for indirect branches and stack to predict return addresses