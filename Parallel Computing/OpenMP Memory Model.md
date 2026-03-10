
Provides a "relaxed consistency" shared memory model

- Threads can maintain a local copy of shared memory
- Not required to maintain exact consistency with all other threads all the time
- When necessary that all threads view a variable the same
	- Programmer responsible
	- Variable is FLUSHed by all threads

### Flush and Synchronize

- Flush operation implicit for the following:
	- Entry/exit of parallel region
	- Implicit and explicit barriers
	- Entry/exit of critical regions
	- Whenever lock set or unset
- **Not implicit at:**
	- Entry to work-sharing (for, section, etc) regions
	- Entry/exit of master regions

### Producer-Consumer Pattern

- Shared a, flag:
  ![[Pasted image 20260302185235.png]]
- This can be incorrect
- Thread 1 consumer the values for a and flags produced by thread 0
- Assume thread 0 is made to execute first and update the shared var
	- "Relaxed consistency": Thread 0 updates its local copy of the shared variables
	- Will not be consistent until implicit/explicit flush

![[Pasted image 20260306123643.png]]

Ex has too many flushes, needed for variables that need to be updated between threads