1.
a) my_value = 0
reduced_value = 15

b) my_value = 10
reduced_value = 0

c) my_value = 20
reduced_value = 0

d) my_value = 30
reduced_value = 0

2.


```
averow = NRA/numworkers;
extra = NRA%numworkers;
offset = 0

for (dest=0, dest < numranks, dest++){
  rows = (dest <= extra) ? averow+1 : averow;
  b_send_counts[dest] = NRB*RCB; // send the whole matrix B
  b_displs[dest] = 0;
  a_send_counts[dest] = rows * NCA;
  a_displs[dest] = offset;
  offset = offset + a_send_counts[dest];
}

int MPI_Scatterv (&A, a_send_counts, a_displs, MPI_INT,
&A, a_send_counts[taskid], MPI_INT, MASTER, MPI_COMM_WORLD)

int MPI_Scatterv (&B, b_send_counts, b_displs, MPI_INT,
&B, b_send_counts[taskid], MPI_INT, MASTER, MPI_COMM_WORLD)

```

3.

a) Rank 0:
my value = 4
reduced value = 0

b) Rank 1:
my value = 5
reduced value = 0

c) Rank 2:
my value = 6
reduced value = 0

d) Rank 3:
my value = 7
reduced value = 4

e)

```
my_value = my_id + numprocs;
count = 1;
dest = 3;

if (my_id != dest){
  MPI_Send(&my_value, count, MPI_INT, dest, mtype, MPI_COMM_WORLD);
}
if (my_id == 3){
  reduced_value = my_value;
  for (int i=0, i < numprocs, i++){
    if (i != dest){
      int temp_val;
      MPI_Recv(&temp_val, 1, MPI_INT, i, mtype, MPI_COMM_WORLD, &status)
      if (temp_val < min_val){
        reduced_val = temp_val
      }
    }
  }
}

```

4.
a)
```
sendcounts[0] = 10
sendcounts[1] = 15
sendcounts[2] = 5
sendcounts[3] = 10
displs[0] = 15
displs[1] = 80
displs[2] = 60
displs[3] = 5
```
b)
```
int *recbuf = malloc (sizeof(int)*N);
MPI_Comm_rank(MPI_COMM_WORLD, &myrank);

MPI_Scatterv (databuf, send_counts, displs, MPI_INT,
recbuf, displs[myrank], MPI_INT, source, MPI_COMM_WORLD))
```

5.
a)
```
sendcounts[0] = 10
sendcounts[1] = 10
sendcounts[2] = 15
sendcounts[3] = 5
displs[0] = 5
displs[1] = 15
displs[2] = 80
displs[3] = 60
```
b)
```
for (int i = 0; i < sendcounts[myrank]; i++){
  recbuf[i] *= myrank
}

MPI_Gatherv(&recbuf, sendcounts[myrank], MPI_INT, &databuf, sendcounts, displs, MPI_INT, 2, MPI_COMM_WORLD)
```

**Note:**
**int MPI_Gatherv(const void *sendbuf, int sendcount, MPI_Datatype sendtype,**
                **void *recvbuf, const int recvcounts[], const int displs[],**
                **MPI_Datatype recvtype, int root, MPI_Comm comm)**

6.
```
pragma omp parallel shared (N, databuf, sendcounts, displs) private (rec_buf, tid)
/* Start parallel region and fork threads */
{
  int rec_buf = malloc (sizeof(int)*N);
  tid = omp_get_thread_num();
  
  for (int i = 0; i < sendcounts[tid]; i++){
    rec_buf[i] = databuf[displs[tid] + i];
    rec_buf[i] = rec_buf[i] * tid;
    databuf[displs[tid] + i] = rec_buf[i];
    #pragma omp flush
  }
}
```

7.
a)
```
T0, Iteration 1, a = 1
T0, Iteration 1, b = 1

T0, Iteration 2, a = 3
T0, Iteration 2, b = 3

T1, Iteration 1, a = 5
T1, Iteration 1, b = 5

T1, Iteration 2, a = 8
T1, Iteration 2, b = 8
```

b)
No, since both can execute the code at the same time and they deal with shared variables, it's likely T0 and T1 print in different orders and update the value at different times, depending on the run

8.

**Note: Using "single" here wouldn't work since it places barriers, so multiple threads don't execute in parallel, it just makes one thread execute** 

```
double x, y, z, final_result;
final_result = 0.0;
int tid;
#pragma omp parallel shared(x, y, z, final_result)
private(tid)
{
  tid = omp_get_thread_num();
  #pragma omp sections {
    #pragma omp section {
      x = calculate_x(...);
      printf("T%d: x = %f\n", tid, x);
    }
	#pragma omp section {
      y = calculate_y(...);
      printf("T%d: y = %f\n", tid, y);
	}
	#pragma omp section {
	  z = calculate_z(...);
	  printf("T%d: z = %f\n", tid, z);
  }
  #pragma omp flush
  #pragma omp master{
      final_result = x*y + z;
      printf("Final result is %d, final_result);
  }
```

9.
**Note: Static Scheduling happens in order of every thread, they just execute in random order**

```
T1: c[10] - c[19]
T0: c[0] - c[9]
T3: c[30] - c[39]
T0: c[40] - c[49]

T0: c[80] - c[89]
T2: c[20] - c[29]
T2: c[60] - c[69]
T3: c[70] - c[79]

T2: c[100] - c[103]
T1: c[50] - c[59]
T1: c[90] - c[99]
T0:

T2:
T3:
T1:
```

10.

```
int A[N];
int tid;

for (int i = 0; i < N; i++){
  A[i] = i;
}

#pragma omp parallel shared(A, N) private(tid) {
  tid = omp_get_thread_num()
  if (tid > 0){
    #pragma omp critical{
      for (int i = 0, i < N; i++){
        A[i] = A[i] * tid;
      }
      #pragma omp flush
    }
  }
}
```

## Miscellaneous

(a) In developing an application, we determined that it does not scale well in term of strong
scaling. Give me one sentence* explaining what that means. (2 pts)

It means that increasing parallel processing doesn't significantly increase performance as long as the number of elements processed stays the same

(b) Since the application does not scale well in terms of strong scaling, then it must be a
good candidate for weak scaling. Is this statement true or false? Give me one sentence*
explaining your answer.

False. Depending on the application, increasing the problem size may or may not improve performance, as having poor strong scaling doesn't necessarily mean better weak scaling

```
c) # of total threads: 128
# of total nodes: 4
# of total cores: 64
# of total ranks: 16
```

d)
- Flush 1 is unnecessary because flush 2 will update both a and the flag. Flush 5 is not necessary since thread 0 already flushed variable a.

e) 
Use Gustafson's Law because weak scaling
```
20 = N + (1-N)*0.15
N = 23.35
```

f) Pragma omp for vs pragma omp section
"For" directive defines a loop that will be executed by multiple threads
"Section" directive allocates a separate thread to execute the code of a section

g) How can OpenMP thread send data to another thread:
Use shared variables (write to shared memory, read data from shared memory)

h)
- Barrier: This directive will ensure all threads arrive to this point in execution before continuing
- Single: Ensures the a single thread is the only to execute the following section
- Critical: Ensures only one thread is executing this section at any given time
- Ordered: Specifies structured block in a loop region that will be executed in the order of the loop iterations
- Flush: Makes a thread's local view of shared memory consistent with main memory (reflect latest values)
