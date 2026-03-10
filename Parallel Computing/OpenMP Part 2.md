### omp for: private, firstprivate, lastprivate

![[Pasted image 20260218133803.png]]

- For private, x becomes a new private variable, which the threads share, but it's separate from outside the parallel region
- After parallel region, x stays as 44
- First private is the first thread that picks up. Last private comes out as what the last thread got

### Hybrid Model - MPI + OpenMP

- MPI between nodes
	- Scalability: facilitiates cooperating shared memory (OpenMP) programming across SMP* ndoes
- OpenMP inside SMP* node
	- Manages workload on each SMP node
	- Threaded programming
	- Efficient use of shared memory SMP systems. Less overhead than message passing

## Implied Barrier

- Two work sharing loops in one parallel region
- Distribution of iterations to threads may not be identical
- Ensures results are available when needed
  ![[Pasted image 20260302122955.png]]

## Reduction Clause

- `#pragma omp for reduction(operator: list)`
- ![[Pasted image 20260302123204.png]]
- Ex: 
  ![[Pasted image 20260302123326.png]]
- Dot product calculation
- Initialize a and b
- ![[Pasted image 20260302124817.png]]
- Sum becomes a special shared variable. All partial sums will be summed up
- If using a normal shared variable, chance to get wrong answer

## OMP section(s) --> Work Sharing Directive

- Defines a set of structured blocks (can perform different functions)
	- To be executed in parallel by a set of threads
- No assumptions about order of execution
- If more sections than threads, some threads will execute more than one section (sequentially)
- If more threads than sections, some threads will be idle

![[Pasted image 20260302130151.png]]

Ex:
![[Pasted image 20260302130323.png]]

