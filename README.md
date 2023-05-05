# FreeNOS Memory Management System
FreeNOS uses a dynamic memory management system based on a heap allocator. It even says in line 196 of Allocator.h. In Kernel.h, the Kernel() function sets up the kernel heap for dynamic memory allocation based on the architecture core. This is evidenced here.

<br/>

In Allocator.cpp, the parameters are the range of the requested size and alignment on input. The output contains the actual allowed allocated address. This is reimplemented in PoolAllocator, BitALlocator, SplitAllocator, PageAllocator, and Bubble Allocator.

<br/>

The SplitAllocator class is used to split the physical and virtual memory ranges into smaller blocks that can be allocated and deallocated dynamically. The parameters arae the same as Allocator.h, but the alignment value must be a multiple of the pageSize. The BubbleAllocator can only grow in allocated memory, but it can't free memory. The BitAllocator allocates memory by using a BitArray, in which all memory is divided in tsame sized parts called chunks. A 1 means the chunk is used, and a 0 means unused. The PageAllocator class allocates virtual memory using the memory server. All these different allocator classes stem from the main Allocator.h and Allocator.cpp files.

<br/>

In VMCtl.cpp, it has an important functions that handles a given virtual memory operation given an opcode. This is the VMCtlHandler function. If the operation is LookupVirtual, the function translates the virtual memory address in the given memory range to a physical address. If the operation is LookupVirtual, the function translates the virtual address in the given memory range to a physical address. If the operation is MapContiguous or MapSparse, the function maps the given memory range to a contiguous or sparse physical address. If the virtual address is not specified, the function finds a free virtual address in the user's private memory range. If the operation is UnMap, the function unmaps the specified memory range. If the operation is Release or ReleaseSections, the function releases the specified memory range or sections of it. If the operation is CacheClean, CacheInvalidate, or CacheCleanInvalidate, the function performs the corresponding cache operation on the given memory range. If the operation is Access, the function checks the access permissions for the specified virtual address range. If the operation is ReserveMem, the function allocates physical memory for the specified range using the split allocator. The function returns an API::Result code indicating the success or failure of the operation.

<br/>

In ApplicationLauncher.cpp, The exec() method uses the fork() system call to create a new process and then calls execve() to replace the new process's memory image with the specified executable. This causes the new process to start running the specified program. If execve() fails, an error message is printed and the new process exits with a non-zero exit code.

<br/>

In the MemoryContext.cpp, it has methods for mapping and unmapping memory ranges, releasing physical memory, finding free virtual memory address, and keeping track of the current memory context. In its constructor, it takes an instance of the MemoryMap class and the SplitAllocator class. It does this because MemoryMap is used to manage virtual memory and SplitAllocator is for physical memory. The mapRangeContigiuous method maps a range of contigious virtual pages to a block of contigious physical pages.  If the physical pages are not provided, the method uses the SplitAllocator to allocate a block of contiguous physical pages. It then maps the virtual pages to the physical pages.The MemoryContext class keeps track of the current memory context using a static m_current member variable. This allows the system to switch between different memory contexts depending on the currently executing process. The unmapRange method unmaps a range of virtual pages, and release method releases a block of physical pages.

<<br/>

The Memory.h file has the definitions for the memory access flags and a memory range structure. The Range struct represents a memory range, which includes the virtual address, physical address, size in bytes, and access flags. Each flag is represented as a bit in a bit mask, with the first flag being reprsented by the most significant bit. For example, "Readable" is represented by the bit mask 0b00000001, "Writable" by 0b00000010, and "Device" by 0b10000000.

<br/>

The MemoryMap.cpp file shows the layout of the virtual memory map layout. It contains the method range() that returns the Memory::Range object for a given memory region, and a setRange() function that sets the memory range for a given memory regeion. It contains an enum called 'Region' the defines the different memory regions available in the system. These regions are used by the kernel to manage the different types of memory in the system. The use of a dynamic memory virtual map shows how the kernel allocate and deallocate memory duing runtime, and thus dynamically. This is different from a static memory management system, where the memory layout is fixed at compile time and can't be changed during runtime.

# Discussion and Analysis

FreeNOS uses virtual memory, allowing the operating system to provide each process with its own address space, while using physical memory as needed. This helps to isolate processes from each other and provides protection for the operating system. Compared to memory management systems today, FreeNOS's virtual memory management system is similar in that it provides each process with its own address space, as is the case with modern operating systems. On the other hand, modern systems use more advanced techniques for memory allocation and management, such as demand paging, memory compression, and memory deduplication, which are not present in FreeNOS. FreeNOS allocates physical memory using a SplitAllocator, while modern systems use techniques such as demand paging to allocate physical memory as needed.s FreeNOS also supports kernel and user space heap allocators, which is not common in many general-purpose operating systems. FreeNOS uses a BitArray to allocate memory, whereas modern systems often use more complex data structures such as trees or hash tables to manage memory. Some similarities is that both modern systems and FreeNOS have the ability to map virtual memory to physical memory. Also, both systems have the ability to allocate and deallocate memory dynamically during runtime. One more similarity is that FreeNOS, like modern memory management systems, uses forking to create new processes. The exec() method in FreeNOS uses the fork() system call to create a new process and then calls execve() to replace the new process's memory image with the specified executable. This is similar to how modern operating systems create new processes using the fork() system call.

<br/>

One of the main advantages of the FreeNOS memory management system is its flexibility. The allocator can dynamically adjust the memory blocks to optimize memory usage. Another advantage is the support for both kernel and user space heap allocators. This allows the system to allocate memory for both kernel and user space processes. One disadvantage of the FreeNOS memory management system is the potential for memory fragmentation. This is because as memory blocks are allocated and deallocated, the heap allocator can leave small gaps between memory blocks. With the different allcoators, it can cause irregular block size fragmentation that FreeNOS can not keep up with. Over time, these gaps can accumulate, leading to memory fragmentation and reduced memory utilization. Another disadvantage is the potential for heap corruption. If memory blocks are not allocated or deallocated correctly, it can lead to heap corruption, causing the system to crash. Modern memory management systems are designed to be highly efficient and optimized for performance, with algorithms that can quickly allocate and deallocate memory as needed. FreeNOS just uses the built-in memory allocator in the GNU C library and implements it in the different allocator classes, whereas modern memory mangement systems use buddy allocation. For example buddy allocation, the algorithm divides the memory into a binary tree of smaller and smaller blocks, and allocates blocks by splitting larger blocks into smaller ones. These algorithms reduce memory fragmentation.

# Conclusion

FreeNOS uses a memory management system that utilizes several allocator classes such as bit, bubble, pool, and split allocator. Each allocator has a different strategy for managing memory. The bit allocator, for example, uses a simple bitmap to track used and free memory blocks. The pool allocator divides memory into fixed-size blocks and allocates them on demand, while the bubble allocator coalesces adjacent free blocks to form larger free blocks. The split allocator combines the strategies of the pool and bubble allocators.

Unlike traditional systems, FreeNOS does not use the buddy system for memory allocation, instead opting for a more efficient strategy that combines several allocator classes. FreeNOS uses a combination of allocator classes instead of the buddy system for efficient memory management, which reduces fragmentation and minimizes memory usage. This approach makes FreeNOS well-suited for smaller embedded applications. However, for large memory systems, the buddy system may be more suitable due to its ability to handle larger memory blocks efficiently. The buddy system works by dividing memory into equally-sized blocks, which are then allocated or freed as needed. This approach works well for large memory systems as it reduces fragmentation and improves memory utilization. Thus, FreeNOS does its job well.

# References
http://www.freenos.org/doxygen/modules.html
https://www.geeksforgeeks.org/buddy-system-memory-allocation-technique/
https://www.gingerbill.org/article/2019/02/16/memory-allocation-strategies-004/
https://www.geeksforgeeks.org/memory-management-in-operating-system/
https://chat.openai.com/
