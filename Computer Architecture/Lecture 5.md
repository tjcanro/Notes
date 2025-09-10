## Building Processors and Memory

![[Pasted image 20250902200123.png]]

- After slicing, and processing steps, you need to test it. Then they are diced and once the dies are packages, test again and throw out the ones that aren't good
- Good dies get factored into the manufacturing cost

### Trends in Cost
- Cost driven down learning curve
	- Yield
- DRAM: price closely tracks cost
- MicroP: Price is dependent on volume
	- 10% less for each doubling of volume
	- More volume means learning curve lowers, and gives manufacturers power of purchase

## Integrated Circuit (IC) Cost

``` 
Cost of IC = (cost of die + cost of testing die + cost of packaging and final test) / Final test yield
```


```
Cost of die = Cost of wafer / (Dies per wafer * Die yield)
```

Dies per wafer formula:
![[Pasted image 20250902201025.png]]

#### Bose-Einstein Formula:

![[Pasted image 20250902201216.png]]
- Defects per unit area 0.012-0.016 (for 28nm process)
	- For 16nm it's 0.016-0.047
	- All are 2017 data
- N = process-complexity factor = 7.5-9.5 (for 28nm in 2017)

### Dependability

- System's ability to deliver consistently across period of time
	- Reliability is more about failures. Probability of system failure over time
	- Dependability increases reliability
- Service accomplishment: delivered as specified
- Fault may or may not lead to failure of component

### Dependability Measures

- Mean Time to Failure (MTTF)
- Annual Failure Rate (AFR)
	- Percentage of devices expected to fail in a given year for a given MTTF
- Service interruption - Mean time to repair (MTTR)
- Mean time between failures (MTBF = MTTF + MTTR)
- Availability = MTTF / (MTTF + MTTR)
- Improve availability
	- Increase MTTF
		- Fault avoidance, fault tolerance, fault forecasting
	- Reduce MTTR
		- Improved tools and processes for diagnosis and repair

## MTTF vs AFR

- Disk quoted for 1,000,000-hour MTTF
- Web service, with 50k servers, with 2 disks/ server
- AFR:
```
1 year = 8760 hr
MTTF = 1,000,000
AFR = 8760/1000000 = 0.876%

With 100,000 disks, expect 876 to fail per year
```

### Performance

Desktops or servers in a datacenter?

Desktop --> which completes program first: **Response Time/Execution Time**
Server --> Most jobs in a day: **Throughput/Bandwidth**

- Performance(x) = 1/execution time(x)
- For computers X and Y --> Performance(x)/Performance(y) = Execution time(y)/Execution time(x) = n
- X is n times faster than Y
- Response time:
	- Experienced by the user
	- Total time including: processing, I/O, OS overhead
- CPU time:
	- Time spent processing a particular task (Not including I/O, context switching)
	- Includes user and system(kernel) CPU time

### Benchmarks

- Kernels
	- For matrix multiplication
- Toy programs
	- Sorting, for example
- Synthetic benchmarks
	- Dhrystone apps, used in embedded. Mimic actions?
- Benchmark suites are certain specs. Bunch of benchmarks ran together, done to reduce bias
	- SPEC2017, TPC-C
	- SPECRatio = ExecutionTime(reference) / ExecutionTime(tested)

## Principles of Computer Design

- Take advantage of Parallelism
- Principle of Locality
	- Reuse of data and instructions
	- 90% of execution time in only 10% of the code --> Allows memory hierarchy
- Focus on Common Case
	- **Amdahl's law**
	- Defines speedup that can be gained by using a feature
	- Speedup = performance of task with enhancement when possible / performance without enhancement
	- What to improve to get maximum benefit

#### Amdahl's Law

```
ExecutionTime(new) = ExecutionTime(old) * ((1 - FractionEnhanced) + Fraction(enhanced) / Speedup(enhanced)
```

- Fraction enhanced is how many seconds of it can use the enhancement out of the total
- Speedup is how much it improves when used

Overall Speedup
`Speedup(overall) = 1/ [ (1-Fraction(enhanced) + (Fraction(enhanced)/Speedup(enhanced) ]`

### Performance Equation

- CPU Time = CPU clock cycles for a program * clock cycle time
- CPU Time = Clock cycles / clock rate
- CPI (Clock cycles per Instruction) = CPU clock cycles for program / Instruction count
- CPU time = Instruction count * CPI * Clock cycle time

For different Classes of instructions, use:
![[Pasted image 20250902232730.png]]

- Must include pipeline effect, misses, and other inefficiencies. Because of that, measure it, not take from reference manual