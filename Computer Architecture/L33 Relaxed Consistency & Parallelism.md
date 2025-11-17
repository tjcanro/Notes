#RelaxedConsistency

Relaxed consistency: allow reads and writes to complete out of order, but use synchronization primitives to enforce ordering
- X -> Y means X must happen before Y
- Sequential consistency requires maintaining all possible orderings

- Total store ordering relaxes W->R ordering
- Partial store ordering relaxes W->R and W->W ordering
- Relaxed consistency -> all of them are relaxed. Programmer tasked with maintaining consistency

### Example

In sequentially consistent memory:
Memory instructions complete in order:
![[Pasted image 20251116081403.png]]

Total store ordering: you can read before writes complete
![[Pasted image 20251116081552.png]]
- (0,0) is valid Total store ordering in this case

Partial store ordering: you can write before writes complete, and read before writes complete
![[Pasted image 20251116081833.png]]
- P.S.O here is (0,1), which reordered writes

### Relaxed Consistency Models

![[Pasted image 20251116082001.png]]
S is the lock primitive (acquiring and releasing the lock)
- On release consistency: you need to keep ordering between locks too

![[Pasted image 20251116082826.png]]

## Data Level Parallelism
#DataLevelParallelism

SIMD: exploit data level parallelism for
- Matrix-oriented scientific compute
- Graphics (image and sound processors)

MIMD: private instruction cache in addition to data cache

SIMD is more energy efficient than MIMD
- MIMD has a lot of overhead
- Only needs to fetch one instruction per data operation
- Good for personal mobile devices
	- Also some servers

SIMD allows programmer to continue to think sequentially

### SIMD Parallelism

- Vector architectures
- SIMD extensions (of architecture)
- GPUs