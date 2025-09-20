#Cache
## Cache Memory

### Processor Address

- SW -> Writing to memory
- LW -> reading from memory
- 32 bit long address (depends on arch) 

Transfers:
- Disk to Main memory: Pages
- Main Memory to cache: Block
- Cache and processor: Cache blocks or cache lines

Blocks:
- Anywhere from 1 word (32 bytes) to 4 words
- 1 word is 4 Bytes
- RISC-V is byte addressable, you need to be able to identify each byte
- 2 bits to reference byte in each word
- Byte offset (2 lsb bits in the address) are used
- Word offset to define the word in the block (3 bits since 8 words in block)
![[Pasted image 20250906231500.png]]

### Types of Cache

#### Direct-Mapped Cache
Each location in memory has a specific addressed that they can be mapped to.

Location 0 can be occupied by location 0 in every block
- Cache index in the address <1:0>
![[Pasted image 20250906235914.png]]

- A bunch of memory mapped to the same location. How do I know which one is valid?:
	- Tag
	- Valid bit: know weather data in cache location is valid or not. If not, load from memory

##### Tag

- Store block address as well as data
- Only high-order bits are necessary
- Compare tag to high order data address
- ![[Pasted image 20250907000415.png]]
- If data not present in location, valid bit is 0

#### Ex

- 32 bit memory address
- 2^n direct mapped cache
- Cache index: lower n bits of address
- ![[Pasted image 20250907000715.png]]
- After going to index, if valid bit 0, classify as miss, fetch data, write tag, set valid
- Misses: Can also happen when tag doesn't match [**Important**]

### Other types:

![[Pasted image 20250907001412.png]]

- 2-way set associative: (2 way because each set can accommodate two blocks)
- Index bits point you to the set. Data can be in either of the blocks
- Bits point you to the set you need to access. Both blocks have same two LSBs 
- Similar to direct but you group together

- Fully associative: No cache index: One big set, basically. Need to search all cache to see if data exists or not. More hardware complexity

#### 2-way associative
- Cache index selects a set from the cache
- The tags in the set are compared in parallel
- Data selected based on result
- Diagram:
  ![[Pasted image 20250907002143.png]]
- Mux to select which data once the tags are compared in parallel

#### Fully Associative
- Kind of like finding data on an array
- Need to compare all the tags
- Diagram:
	![[Pasted image 20250907002340.png]]
- All comparators in parallel

### Block Replacement

- When cache is full and miss occurs
	- Choose a block to remove
	- **Important: Data/instruction must exist in cache always**
	- Direct mapped: based on index, no choice so easy policy
- Associative cache
	- Policy
- How to remove the block
- Policies:
	- Random, easy
	- LRU: Typically approximated
		- Relies on locality
	- FIFO: Oldest block