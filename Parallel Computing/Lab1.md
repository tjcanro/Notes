## Monte-Carlo Integration and MPI

Method for estimating integrals

![[Pasted image 20260203120206.png]]

Solution estimated with uniform random variable evenly distributed over \[a, b]. Estimate h(x) converges to the solution as number of samples N grows.

Calculate with MPI:

![[Pasted image 20260203120419.png]]

Substitute for f(x) we have g(a,b) and h(x):
![[Pasted image 20260203120526.png]]

Concept
![[Pasted image 20260203120649.png]]

Serial code:
![[Pasted image 20260203120936.png]]

Partition and distribute work to worker ranks
- "Unroll loop" and divide evenly among workers (equivalent to divide the rows of the matrix A in mat_mult.c)
- Each worker performs percentage of total # of loops
- Use mat_mult as template
- Result should be around ~18,17.XXX

Run with args:
![[Pasted image 20260203122644.png]]

For init and rand_seed()
- Simple function (can just be srand(); or a few lines)
- srand() function; initialize seed for rand() function

Node: each rank should start with different seed

Estimate function:
- Outline: need to initialize, divide up the problem as even as possible
- Master rank doesn't have to initialize/divide and go to MPI_send input to to wokers
- The workers figure out themselves how many iterations myN themselves
- For each rank:
	- Calculate myN
	- partialSum = 0
	- for loop through myN and generate random variable between a and b, compute f(x(i)), add result to partialSum

### Part C

