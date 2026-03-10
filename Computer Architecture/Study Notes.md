## Chapter 1 – Fundamentals of Quantitative Design and Analysis  

- **Different goals for different classes of computers**  
  Personal computers (balance cost/performance), embedded systems (low power, reliability, cost), servers (throughput, availability), PMDs (battery life, size), clusters (scalability, parallelism).  

- **Power wall, ILP wall, memory wall — implications**  
  - **Power wall:** limited by heat dissipation and energy use.  
  - **ILP wall:** diminishing returns from deeper pipelines and out-of-order execution.  
  - **Memory wall:** CPU speed growing faster than memory latency; bottleneck in data access.  
  → Together, they constrain performance scaling via clock speed or ILP alone.  

- **Why multicore technology became important**  
  Because increasing frequency and ILP hit physical/power limits. Multiple simpler cores improve performance per watt and exploit thread-level parallelism.  

- **Why focus on the common case in optimization**  
  Optimizing frequent operations gives the largest performance impact (Amdahl’s Law); uncommon cases have small overall effect.  

- **Trends in technology**  
  - **Integrated circuit logic (Moore’s Law):** transistor counts ~double every 2 years (now slowing).  
  - **Performance trends – Bandwidth vs Latency:** bandwidth improves faster than latency; latency is harder to reduce.  
  - **Power vs Energy:**  
    - Mobile: limited by battery energy.  
    - Datacenters: limited by cooling and TDP (Thermal Design Power = sustained power).  
  - **Trends in power:**  
    - **Clock gating:** disables idle circuits to save dynamic power.  
    - **Static vs Dynamic power:** 
	    - static = leakage. Power consumed even when idle. Grows as transistors shrink. Now a major concern 
	    - dynamic = switching activity. When transistor goes 0 to 1  
    - **Higher clock rate:** increases dynamic power quadratically.  
    - **Power gating:** turns off unused blocks to cut static power.  

- **Classes of parallelism**  
  - **Data-level (DLP):** same operation on many data (SIMD, vector).  
  - **Instruction-level (ILP):** overlapping independent instructions (pipelining, superscalar).  
  - **Thread-level (TLP):** multiple threads or cores.  
  - **Request-level:** parallel requests in servers.  

- **Pipelines — depth effects**  
  Deeper pipelines → higher *throughput* (more instructions per time) but longer *latency* (each instruction takes more cycles).  

- **Flynn’s taxonomy**  
  - SISD – single instruction, single data (scalar).  
  - SIMD – single instruction, multiple data (vector).  
  - MISD – multiple instruction, single data (rare).  
  - MIMD – multiple instruction, multiple data (multicore).  

- **Measuring, reporting, and summarizing performance**  
  - **Speedup:** ratio of execution times → `Speedup = OldTime / NewTime`.  
  - **Throughput:** total work done per unit time.  
  - **Time definition:** use *execution time* (CPU time = user + system), not elapsed time.  
  - **Benchmarks:** standardized workloads to compare real performance and guide design tradeoffs.  

- **Quantitative principles of computer design**  
  - **Amdahl’s Law**  
    - Formula: `Speedup = 1 / ((1 – Fraction_enhanced) + (Fraction_enhanced / Speedup_enhanced))`.  
    - Use: shows limited gain from improving only part of a system.  
    - Importance: guides designers to target improvements that affect the common case.  
  - **Processor performance equation**  
    - `CPU Time = Instruction Count × CPI × Clock Cycle Time`  
      or `= (Instruction Count × CPI) / Clock Rate`.  
    - `CPI = Σ (fraction_i × CPI_i)` for each instruction class.  
    - Components: instruction count, CPI, and clock cycle time (each affected by ISA, microarchitecture, and technology).

## Appendix B – Review of Memory Hierarchy  

### 3 C’s of cache misses
- **Compulsory (cold):** first access to a block; reduced by prefetching or larger blocks.  
- **Capacity:** working set exceeds cache size; reduced by larger caches or blocking.  
- **Conflict:** multiple blocks map to same set; reduced by higher associativity or better indexing.

---

### Temporal vs Spatial locality
- **Temporal:** recently used items reused soon (loops, stack vars).  
- **Spatial:** items near one another accessed together (arrays, sequential code).  
- **Trends:**  
  - **Instructions:** high temporal and spatial locality (sequential fetch).  
  - **Data:** depends on structure; arrays show spatial, pointer-based less so.

---

### Cache addresses
- **Bit fields:**  
  - Offset = log₂(block size)  
  - Index = log₂(number of sets) = log₂(cache size / (block size × associativity))  
  - Tag = remaining bits  
- **Effects of parameters:**  
  - ↑Block size → ↓compulsory but ↑conflict/penalty.  
  - ↑Cache size → ↓capacity misses but ↑hit time.  
  - ↑Associativity → ↓conflicts but ↑complexity and access time.

---

### 6 Basic cache optimizations
1. **Larger block size:** ↓ compulsory misses (spatial locality).  
2. **Larger caches:** ↓ capacity misses.  
3. **Higher associativity:** ↓ conflict misses.  
4. **Multilevel caches:** hides miss penalty.  
5. **Prioritize read misses:** lowers miss penalty.  
6. **Avoid address translation during indexing (VIPT):** reduces hit time. Use page offset to index cache (same in virtual and physical memory)
---
### Advanced Cache Optimization Categories

1. Reducing the hit time—Small and simple first-level caches (associativity) and way-prediction.
Both techniques also generally decrease power consumption.
2. Increasing cache bandwidth—Pipelined caches, multibanked caches (simultaneous access by having independent cache banks), and non-
blocking caches. These techniques have varying impacts on power consumption.
3. Reducing the miss penalty—Critical word first and merging write buffers.
These optimizations have little impact on power.
4. Reducing the miss rate—Compiler optimizations. Obviously any improvement
at compile time improves power consumption.
5. Reducing the miss penalty or miss rate via parallelism—Hardware prefetching
and compiler prefetching. These optimizations generally increase power con-sumption, primarily because of prefetched data that are unused.

---

### Write-back vs Write-through
- **Write-through:** writes to lower memory every time; simpler but ↑ bandwidth use.  
- **Write-back:** updates only on eviction; ↓ traffic but needs dirty bits.  
- **Impact:** tradeoff between coherence simplicity (write-through) and efficiency (write-back).

---

### Virtual memory
- **What:** per-process virtual address space mapped to physical memory.  
- **Purpose:** protection, isolation, flexible allocation, ease of programming.  
- **Helps:** allows large/sparse address spaces.  
- **Hurts:** page faults and TLB misses add delay.  
- **Page tables:** store VA→PA mappings; usually multi-level for space efficiency.  
- **Speed-ups:** use a **TLB** and **VIPT caches** to overlap translation.

---

## Chapter 2 – Memory Hierarchy Design

### 11 Advanced cache optimizations
1. **Small & simple caches:** ↓ hit time.  
2. **Way prediction:** guess correct way → ↓ hit time.  
3. **Pipelined access:** ↑ cache bandwidth.  
4. **Nonblocking caches:** hit-under-miss improves throughput.  
5. **Multi-banked caches:** parallel bank access.  
6. **Critical word first / early restart:** return needed word first → ↓ miss penalty.  
7. **Merging write buffer:** combines adjacent writes → ↓ miss penalty.  
8. **Compiler code/data layout:** improves locality.  
9. **Loop interchange / blocking:** align data access with cache lines.  
10. **Compiler-controlled prefetch:** hides latency.  
11. **Hardware prefetch (I/D):** automatically fetches upcoming lines.  
✅ Effectiveness depends on workload and cache size/latency trade-offs.

---

### Memory technology (basics)
- **SRAM:** 6T cell, fast, no refresh, more area/power → used for caches.  
- **DRAM:** 1T+capacitor, slower, dense, needs refresh → main memory.  
- **DRAM access:** row (RAS) then column (CAS); reusing open row exploits locality.  
- **Locality optimization:** access data within same row to reduce activate/precharge cost.  
- **DDR SDRAM:** double data rate; transfers on both clock edges → ↑ bandwidth.

---

### ProtectioTn: Virtual memory & virtual machines
- **Virtualization:** running multiple OSes on one machine under a **VMM/hypervisor**.  
- **VM protection:** isolates guests; VMM controls privileged state.  
- **Why popular:** consolidation, portability, testing, security.  
- **Protections offered:** memory, CPU, and I/O isolation.  
- **VMM responsibilities:** resource allocation, trapping privileged ops, maintaining illusion of full hardware.  
- **ISA support:** reduces overhead; lack of it forces costly trap/emulation.  
- **Running modes:** separate VM and VMM modes maintain privilege boundaries.  
- **Privileged instructions:** affect system state; VMs trap these to the VMM for handling.  
- **Overhead:** nested address translations, TLB pressure; minimized with hardware assist (EPT/NPT) or tagged TLBs.

---

## Appendix C – Pipelining: Basics

### What is pipelining?
Overlaps instruction stages to execute multiple instructions concurrently.

### 5 pipeline stages
1. **IF** – Instruction Fetch  
2. **ID** – Decode & register fetch  
3. **EX** – Execute / ALU ops  
4. **MEM** – Memory access  
5. **WB** – Write result back  

**Performance issues:** hazards (structural, data, control) and branch penalties.

---

### Pipeline hazards
- **Structural:** same hardware used by multiple stages → add units or stall.  
- **Data:** one instruction depends on result of another → use forwarding or stall.  
- **Control:** branch changes PC flow → predict or delay.

**Performance impact:** adds stall cycles → increases CPI.

---

### Forwarding
- **Helps:** when data is available early (ALU→ALU, ALU→store).  
- **Not useful:** load-use hazard (`lw` followed by use next cycle).  
- **Example:**
```asm
  lw  x1, 0(x2)
  add x3, x1, x4  # must stall 1 cycle (cannot forward from MEM yet)
```
 
---

### Pipeline stalls
Inserted bubbles to preserve correctness; reduce throughput and raise CPI.

---

### Branch hazards
- **Branch penalty:** cycles lost on misprediction or flush.  
- **Reduction:** static/dynamic prediction, delayed branching, early resolution.

---

### Exceptions
- **Categories:** I/O, arithmetic, memory, external interrupts.  
- **Requirements:** precise state (complete all prior instructions, squash following ones) for correct restart.

---

## Chapter 3 – Instruction-Level Parallelism (ILP)

- **What is ILP?** Parallel execution of independent ops within a single thread. Pipelining/multi-issue exploit it.
- **How pipelining exploits ILP**: Overall stages of different instructions

#### Dependences

- **Data (true)**: RAW -> creates hazards
- **Name**: output or anti-dependence (WAW/WAR) --> solve by renaming registers
- **Control**: ordering from branches

#### Data Hazards
- **RAW**: j reads before i writes.
- **WAW**: j and i write same dest out of order.
- **WAR**: j writes before i reads (needs renaming).
### Compiler ILP exposure
- **Scheduling, loop unrolling**: fill delay slots/overlap latencies; consider deps, code size, **register pressure**.
- **Unrolling**: Independent iterations, enough registers to hold, good memory access patterns

### Reduce Branch Cost with Prediction
- Predict target so pipeline is busy -> lower CPI
- Static vs dynamic: dynamic uses history tables. Static is based on predefined algorithms
- 2-bit: saturating counters (mis-predict twice)

### Dynamic Predictors
- **Correlating (m, n):**  
  Use **global branch history (m bits)** together with the **branch address (n bits)** to index the predictor table.  
  Captures relationships between outcomes of *different* branches (e.g., nested loops or dependent conditions).

- **Tournament predictors:**  
  Combine **local** and **global** predictors.  
  A **selector** (meta-predictor) tracks which predictor is currently more accurate for each branch.  
  Works well for medium to large tables by adapting to varying branch behaviors.

- **Local vs Global information:**  
  - **Local predictors:** record history for each individual branch — capture per-branch patterns.  
  - **Global predictors:** use one shared history register — capture correlations across branches.

- **How branches are addressed in predictors:**  
  - Each predictor table is **indexed using the branch’s program counter (PC)** and possibly the **branch history** (local or global).  
  - For example, `index = (PC XOR global_history) mod table_size`.

- **Aliasing problem:**  
  - Occurs when **different branches map to the same predictor entry** due to limited table size.  
  - **Problems:** one branch’s history can **corrupt** another’s prediction (noise).  
  - **Solutions:**  
    - Increase table size.  
    - Use better **hashing (e.g., XOR of PC and history bits)**.  
    - Add **tags** to distinguish entries (used in TAGE predictors).  
    - Partition predictors or use selective updates.
