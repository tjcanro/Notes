
### Explicit Barrier

- Use `#pragma omp barrier`
- Master threads at barrier until last thread in team completes

### Implied Barrier

- Two work sharing loops in one parallel region
- Distribution of iterations to threads may not be identical
- Implied barrier ensures results are available when needed

	![[Pasted image 20260302134608.png]]

#### Nowait Clause

- `#pragma omp nowait`
- Removes implied barrier after work-sharing construct
  ![[Pasted image 20260302134759.png]]
- Not a section, shared parallel
- All threads working, not one per section
- second nowait in this case is not necessary

### Synchronization: omp single/master

- `#pragma omp single`
	- Section of code must be run by a single available thread
- `#pragma omp master`
	- Section of code must be run only by a master thread
- The rest of threads in the team wait at the implicit barrier at the end of the single/master construct
- ![[Pasted image 20260302140455.png]]

### Critical/Atomic

- Useful to avoid a race condition, but still has random order

#### Critical

- `pragma omp critical`
	- All code in block will be run by each thread, but only one at a time
	![[Pasted image 20260302183919.png]]

#### Atomic

- `pragma omp atomic (read,write,update,capture)`
- Only memory updates (not entire block of code) are performed atomically
	- More efficient than critical
	- "Locks" shared data being updated

### Ordered

- `#pragma omp for ordered [clauses]`
- Or, `#pragma omp ordered`
- Specifies structured block in a loop region that will be executed in the order of the loop iterations