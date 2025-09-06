## Parallelism

### In Applications
- Data-Level Parallelism (DLP) - One instruction on different parts of the data at same time
- Task-Level Parallelism (TLP)
### In Architecture
- Instruction-Level Parallelism (ILP)
	- Exploits DLP - pipelining, speculative execution
- Vector architectures / GPUs
	- DLP --> single instruction on collection of data
- Thread-Level Parallelism
	- DLP or TLP - interaction between parallel threads
- Request-level parallelism
	- TLP (decoupled tasks)

## Flynn's Taxonomy

- Single instruction stream, single data stream (SISD)
	- Uniprocessor with pipeline and speculative execution
- Single instruction stream, multiple data stream (SIMD)
	- Vector architectures, Multimedia, GPUs
- Multiple instruction stream, single data --> not commercial application
- Multiple instruction steams, multiple data streams (MIMD)
	- Tightly (you ensure no sync issues) or loosely coupled (independent tasks, no sync issues)

## Tech Trends
- Moore's law slowing down
- DRAM Capacity is slowing (25-40%)
- Flash capacity 50-60% increase capacity
- Magnetic disk capacity slowed to 5% a year (used in server because cheap+reliable)

## Bandwidth & Latency

**Bandwidth or throughput**: Total work in a given time
- Ex on bandwidth: how much data I can move from pt A to pt B in a second
**Latency or response time**: Time between start and completion of event (depends on clock speed)

(Latency improvement = bandwidth improvement)

## Power

Trends
- Energy limitation for mobile devices
- Cooling limitation for datacenters

- Problem: power in, power out
- Thermal Design Power (TDP)
	- Based on sustained power consumption
	- Used as target for power and cooling
	- Lower than peak power, higher than average
- **Clock rate can be reduced dynamically to limit power consumption**
- Energy per task often better measurement

### Dynamic Energy and Power

#### Dynamic Energy
- Transistor switch from 0->1 or 1->0
- `1/2 * Capacity load * Voltage^2`
- Dynamic power
- `1/2 * capacity load * Voltage^2 * Frequency switched`
- Reducing clock rate reduced power, not energy
	- Time depends on clock rate so at the end the same energy is consumed
- Clock rate increase is slowing because of cooling limitation

#### Reducing Power
- Do nothing well
- Dynamic Voltage-Frequency Scaling --> Dependence on compute load, or lower power state for DRAM, disks
- Overclocking, turning off cores
	- Overclock for short bursts to complete faster, save power

#### Static Power
- 25-50% of total power
- `Current * Voltage`
- Scales with transistors
- Reduce with power gating
- Not turn on certain things when not in use



