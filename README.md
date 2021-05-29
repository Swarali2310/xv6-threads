# xv6-threads

Adding support for kernel threads in xv6

### Problem

Adding threads to xv6 Operating System using following system calls
i) clone() -- used for thread creation
ii) join() -- wait for a thread completion

Building a small thread library atop the system calls
i) thread_create()
ii) thread_join()
iii) lock_init()
iv) lock_acquire()
v) lock_release()

### Solution

In the clone system call, we imitate fork except for the difference that threads use the same page table directory as the process 
(meaning they share the same memory as the process that created them). 
We store on the stack the fake PC(0xFFFFFFFF) and arg2 and arg1 in that order. 
The stack pointer is then set to point to the fake PC , and the Instruction pointer points to the function pointer provided in the parameter of the clone call. 
Once all the other flags are inherited by the child, the clone call completes.

In the join system call, we iterate over the process table to check which all processes are the children of the current process and share the same page directory 
(because we want to only tackle threads, we have wait() for processes). 
If it is found, we check if its state is zombie, and then we reset all its data in the page table. 
If the current process is already killed, or does not have any threads, we return with an error. 
But if there are children, then the current process sleeps to wait for its children to finish their execution.
