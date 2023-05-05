# FreeNOS Memory Management System
FreeNOS uses a dynamic memory management system based on a heap allocator. It even says in line 196 of Allocator.h. In Kernel.h, the Kernel() function sets up the kernel heap for dynamic memory allocation based on the architecture core. This is evidenced here.

<image>

On line 84 of Allocator.cpp, the parameters are the range of the requested size and alignment on input. The output contains the actual allowed allocated address. This is reimplemented in PoolAllocator, ButALlocator, SplitAllocator, PageAllocator, and Bubble Allocator.

<image>

The SplitAllocator class is used to split the physical and virtual memory ranges into smaller blocks that can be allocated and deallocated dynamically. The parameters arae the same as Allocator.h, but the alignment value must be a multiple of the pageSize. The BubbleAllocator can only grow in allocated memory, but it can't free memory. The BitAllocator allocates memory by using a BitArray, in which all memory is divided in tsame sized parts called chunks. A 1 means the chunk is used, and a 0 means unused. The PageAllocator class allocates virtual memory using the memory server. All these different allocator classes stem from the main Allocator.h and Allocator.cpp files.

<image>

In ApplicationLauncher.cpp, The exec() method uses the fork() system call to create a new process and then calls execve() to replace the new process's memory image with the specified executable. This causes the new process to start running the specified program. If execve() fails, an error message is printed and the new process exits with a non-zero exit code

<image>

In the MemoryContext.cpp, it has methods for mapping and unmapping memory ranges, releasing physical memory, finding free virtual memory address, and keeping track of the current memory context. In its constructor, it takes an instance of the MemoryMap class and the SplitAllocator class. It does this because MemoryMap is used to manage virtual memory and SplitAllocator is for physical memory. The mapRangeContigiuous method maps a range of contigious virtual pages to a block of contigious physical pages.  If the physical pages are not provided, the method uses the SplitAllocator to allocate a block of contiguous physical pages. It then maps the virtual pages to the physical pages.The MemoryContext class keeps track of the current memory context using a static m_current member variable. This allows the system to switch between different memory contexts depending on the currently executing process. The unmapRange method unmaps a range of virtual pages, and release method releases a block of physical pages.

<image>

The Memory.h file has the definitions for the memory access flags and a memory range structure. The Range struct represents a memory range, which includes the virtual address, physical address, size in bytes, and access flags. Each flag is represented as a bit in a bit mask, with the first flag being reprsented by the most significant bit. For example, "Readable" is represented by the but mask 0b00000001, "Writable" by 0b00000010, and "Device" by 0b10000000.

<image>

The MemoryMap.cpp file shows the layout of the virtual memory map layout. It contains the method range() that returns the Memory::Range object for a given memory region, and a setRange() function that sets the memory range for a given memory regeion. It contains an enum called 'Region' the defines the different memory regions available in the system. These regions are used by the kernel to manage the different types of memory in the system. The use of a dynamic memory virtual map shows how the kernel allocate and deallocate memory duing runtime, and thus dynamically. This is different from a static memory management system, where the memory layout is fixed at compile time and can't be changed during runtime.

# Discussion and Analysis
test

# Conclusion
test

# References
test
