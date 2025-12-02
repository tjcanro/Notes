Final is Ch 3.4 - 3.9; Ch 5.1 - 5.6; Ch 4.1 - 4.2; Appendix C.5

SIMD Parallelism
- Vector architectures
- SIMD extensions (x86 extensions, started with MMX (multimedia extensions), then SSE - streaming SIMD ext. and presently AVX - advanced vector extensions (wider width regs than SSE))
- GPUs

#VectorArchitectures
## Vector Architectures

**Basic Idea:**

- Read sets of data elements into "vector registers"
- Operate on those registers
- Disperse the results back into memory

Registers are controlled by compiler
- Used to hide memory latency
- Looks at first element, and transmit one element at a time every clock cycle
- Leverage memory bandwidth
- Compiler determines when to move data and right back

#### Example

![[Pasted image 20251123055003.png]]

**RV64V**

- Vector Registers
	- 32 of them and each is 64 bits wide
	- Vector reg file must provide enough ports to feed the functional units
	- Read/write ports connected by a pair of crossbar switches
- Vector functional units
	- Fully pipelined => start a new operation every clock cycle
	- Control unit to detect hazards
- Vector load/store unit
	- Fully pipelined
		- Words can be moved between vector registers and memory with a bandwidth of one word per clock cycle after initial latency
- Scalar registers
	- Provide data as input as well as compute addresses to pass to load/store unit
	- 31 in RV64V

**RV64V Instructions**

- .vv - both operands are vectors
- .vs - first operand is a vector, second operand is scalar
	- .sv - Scalar, vector
- DAXPY Example (Y = a * X + Y) - Same as homework but in vector:

- First, enable the configuration for 4 double precision fp regs
- Load scalar a
- Load vector X (vld instruction)
- Vector-scalar mult (vmul)
- Load vector Y
- Vector-vector add (vadd)
- Store the sum
- Disable vector regs

8 instructions vs 258 for scalar code
- Loops can be vectorized when no dependencies between iterations in the loop

- .vv, .vs, .sv: not used since compiler generates generic code. Assembler looks at operands and determines what to use

### Vector Execution Time

- Depends on length of operand vectors, structural hazards, data dependencies
- RV64V functional units consume one element per clock cycle
	- Execution time approx the vector length
- Convoy: Set of vector instructions that could potentially execute together
- Chime:
	- Unit of time to execute one convoy
	- m convoys execute in m chimes for vector length n
	- vector length n, requires m x n clock cycles

### Example

- Chaining
	- Allow vector operation to start as soon as the individual elements of its vector source operand become available
		- Equivalent forwarding in scalar pipeline
- Example:
- ![[Pasted image 20251130222715.png]]
- 3 convoys: vld and vmul
- vld and vadd
- vst
- 3 chimes, 2 FP ops per result, cycles per FLOP = 1.5
- For 32 element vector, sequence would take 32 x 3 = 96 clock cycles

## GPUs
#GPUs

### History

- VGA (Video Graphic Array) 90s
	- Memory controller and display generator connected to some DRAM
- In 2000, single chip graphics processor incorporated every detail of traditional high end workstation graphics pipeline
	- Processors oriented to 3D graphics
	- Vertex/pixel processing, shading, texture mapping, rasterization
- Computations became more precise over time
	- Integer to fixed point to single-prevision
	- Recently double-precision floating point
- GPUs have become massively parallel programmable processors - 100s of cores and 1000s of threads

- First, set up triangle, convert coordinates to 3D space
- Rasterization: which pixels lie in this triangle (per pixel values)
- Texture mapping: defines how object looks like
- Shading: Use rasterization and texture to generate image

### GPU Trends

- GPUs implement OpenGL and DirectX for graphic processing

- OpenGL - open standard for 3D graphics programming
- DirectX - Mucrosoft multimedia programming interfaces
	- Ditect3D for 3D graphics

- These APIs have well-defined behavior allowing to build hardware acceleration of graphics processing functions
- Visual computing - intersection of graphics processing and parallel computing

- Heterogenous system - Combination of GPU and CPU to achieve best performance - PCs, game consoles
	- CPU - multicore
	- GPU - manycore

- GPUs have evolved from hardwired, limited capability VGA controller to programmable parallel processors
	- Unified array of many programmable processors
- Unification:
	- Increased scalability - more programable processor cores increase total system throughput
	- Effective load balancing - any processing function can use the whole processor array

### CUDA

- Large floating-point processing power of GPU processor array can be used to solve non-graphics problems as well
- Programming model must allow defining massive parallelism directly while allowing scalable execution
- General purspose computation on GPU - GPGPU
	- Programming GPU using graphics API and graphics pipeline for non-graphics task
- Compute Unified Device Architecture (CUDA)
	- Scalable parallel programming model and software platform for GPU
	- Bypasses graphics interfaces and API
	- Program for C/C++
- SIMD programming style
	- Program for one thread which can be instatiated and executed by many threads in parallel
	- Allows programming CPU cores
- GPU processor architecture exposed as:
	- Implementing programmable graphics API
	- Massively parallel processor array

#### Historical PC Architecture

![[Pasted image 20251201070738.png]]

- North bridge: High speed chipset that connected CPU to high speed devices
- South bridge: Low speed chipset that connected slow peripheral devices
- Framebuffer: Dedicated memory that stores pixel data representing what appears on the screen

#### Contemporary Architecture

![[Pasted image 20251201070955.png]]

- PCI-Express - PCIe - Standard I/O interconnect that uses point-to-point links

GPU to access CPU memory --> Generate virtual address, MMU translates

### GPU - Logical Pipeline

![[Pasted image 20251201071351.png]]
Blue ones are programmable

![[Pasted image 20251201071408.png]]

- Input assember: read vertex data and organizes data into different primitives (points, lines, triangles)
- Vertex shader: performs tasks to coordinate transformations, model to screen
- Geometry shader: operates on whole primitive
- Setup and Rasterizer: fragments per-pixel for screen. Computes colors, and texture coordinates, and shades
- Pixel shader: computes the object itself, at the per-pixel level
- Raster operations/Output merger

### Basic GPU Architecture

![[Pasted image 20251201071828.png]]

- SM: Streaming Multiprocessor
	- Instruction Cache, Constant Cache( Small read only cache for multiple threads to access), Multi-threaded Issue unit
	- SFU: Dedicated hardware for complex math
- Texture unit and Texture L1 Cache: Texture unit does sampling and filtering on texture components. Generate Texels that are stored in L1 cache. They are used for rendering 3D components
- Dram is GDDR: Graphic Data RAM