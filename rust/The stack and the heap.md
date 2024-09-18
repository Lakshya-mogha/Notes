Both the stack and heap are parts of memory available to your code to use at runtime, but they are structured in different ways.

## The Stack

The stack stores values in the order it gets them and removes the value in opposite order,  **LIFO - *Last in first out*.

- Adding a value in stack is called as **pushing** onto the stack.
- Removing is known as **popping** of the stack .

All stored data on the stack must have a known, fixed size. Data with an unknown size at compile time or a size that might change must be stored on the heap instead.

## The heap 

The heap is less organised, when you put data on the heap, you request a certain amount of space.
- The memory allocator finds an empty spot in the heap that is big enough, marks it as being in use, and returns a pointer, which is the address of that location.
- This process is called allocating on the heap or **allocating** (pushing values onto the stack is not considered allocating).

You can store the pointer on the stack, but when you want the actual data, you must follow the pointer. 

1. Pushing to the stack is faster than allocating on the heap.

	- The allocator never has to search for a place to store new data, that data is always at the top of the stack.
	- Allocating space on the heap requires more work because the allocator must first find a big enough space to hold the data and then perform bookkeeping to prepare for the next allocation.

2. Accessing data on heap is slower than accessing data on the stack.


==Main purpose of ownership is to manage heap data.
