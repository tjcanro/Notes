
- Speedup: ration of serial runtime over time by parallel program on p processors
	- S = T(serial) / T(parallel)
- Linear speedup (Ideal scaling)
	- 4 cores => 4x speedup
- Sub-linear speedup expected due to parallel overhead


**Strong Scaling**: increasing parallelism with FIXED problem size

**Weak Scaling**: Increasing parallelism AND problem size

#### Amdahl's Law for Strong Scaling

- Speedup of application with fixed workload by increasing # of processors *(strong scaling)*
	- Measure in implemented system
	- Predict using Amdahl's Law
- ![[Pasted image 20260221180240.png]]
- ![[Pasted image 20260221180835.png]]
- As speedup of parallelized approaches infinity, at most you can get 1/1-a

### Gustafson's Law for Weak Scaling

- Predicted theoretical speedup of application when increasing workload while increasing # of processors (weak scaling)
- ![[Pasted image 20260221181507.png]]