## Memory Layout of the C Program

When you run any C program, its executable image is loaded into the RAM of the computer in an organized manner which is called the process address space or Memory layout of the C program.  
This memory layout is organized in the following fashion:

1. Text or Code Segment
2. Initialized Data Segments
3. Uninitialized Data Segments (BSS)
4. Stack Segment
5. Heap Segment
6. Unmapped or Reserved Segment

[![](https://embetronicx.com/wp-content/uploads/2017/12/memory-layout.png)](https://embetronicx.com/wp-content/uploads/2017/12/memory-layout.png)

### Text or Code Segment

Code segment, also known as text segment contains machine code of the compiled program. The text segment of an executable object file is often a read-only segment that prevents a program from being accidentally modified. So this memory contains **`.bin`** or **`.exe`** or **`.hex`** etc.

As a memory region, a text segment may be placed below the heap or stack in order to prevent heaps and stack overflows from overwriting it.

### Data Segments

_Data segment_ stores program data. This data could be in form of initialized or uninitialized variables, and it could be local or global. The data segment is further divided into four sub-data segments (initialized data segment, uninitialized or .bss data segment, stack, and heap) to store variables depending upon if they are local or global, and initialized or uninitialized.

#### Initialized Data Segment

_Initialized data_ or simply _data segment_ stores all global, static, constant, and external variables (declared with **`extern`** keyword) that are initialized beforehand.

Note that, the data segment is not read-only, since the values of the variables can be changed at run time.

This segment can be further classified into the initialized read-only area and initialized read-write area.

All global, static, and external variables are stored in initialized read-write memory except the const variable.

```C
//This will stored in initialized read-only memory
const int i = 100;  

//This will stored in initialized read-write memory
int j=1;
char c[12] = "EmbeTronicX"

int main()
{

}
```

#### Uninitialized Data Segment

The uninitialized data segment is also called as BSS segment. BSS stands for ‘**Block Started by Symbol**’ named after an ancient assembler operator. The uninitialized data segment contains all global and static variables that are initialized to zero or do not have explicit initialization in the source code.

```C
//This will stored in uninitialized read-only memory
static int i = 0;
int j;

int main()
{

}
```

#### Stack Segment

[Stack](https://en.wikipedia.org/wiki/Stack_(abstract_data_type)) is the place where automatic variables are stored, along with information that is saved each time a function is called. Each time a function is called, the address of where to return to and certain information about the caller’s environment, such as some of the machine registers, are saved on the [stack](https://embetronicx.com/tutorials/p_language/c/stack-data-structure-in-c-programming/). The newly called function then allocates room on the stack for its automatic and temporary variables. This is how recursive functions in C can work. Each time a recursive function calls itself, a new stack frame is used, so one set of variables doesn’t interfere with the variables from another instance of the function.

So, the Stack frame contains some data like return address, arguments passed to it, local variables, and any other information needed by the invoked function.

 A “**stack pointer (SP)**” keeps track of the stack by each push & pop operation onto it, by adjusting the stack pointer to the next or previous address.

The stack area traditionally adjoined the heap area and grew in the opposite direction; when the stack pointer met the heap pointer, free memory was exhausted. (With modern large address spaces and virtual memory techniques they may be placed almost anywhere, but they still typically grow in opposite directions.)

If you want to learn the stack implementation, you can refer to [this article](https://embetronicx.com/tutorials/p_language/c/stack-data-structure-in-c-programming/).

#### Heap Segment

Heap is the segment where dynamic memory allocation usually takes place.

The heap area begins at the end of the BSS segment and grows to larger addresses from there. The Heap area is managed by **`malloc`**, **`realloc`**, and **`free`**, which may use the **`brk`** and **`sbrk`** system calls to adjust its size (note that the use of **`brk/sbrk`** and a single “heap area” is not required to fulfill the contract of **`malloc/realloc/free`**; they may also be implemented using **`mmap`** to reserve potentially non-contiguous regions of virtual memory into the process’ virtual address space). The Heap area is shared by all shared libraries and dynamically loaded modules in a process.

### Unmapped or reserved segment

Unmapped or reserved segments contain command-line arguments and other program-related data like the lower address-higher address of the executable image, etc.

## Memory layout in C with examples

Just see the below example. I will tell you the Memory layout using a practical example.

### Example

**Step 1:**

We will see the memory layout of the below program.

```C
#include <stdio.h>

int main(void)
{
    return 0;
}
```

Compile and Check memory.

```C
[embetronicx@linux]$ gcc memory-layout-test.c -o memory-layout-test

[embetronicx@linux]$ size memory-layout-test

text       data        bss        dec         hex       filename
960        248          8        1216         4c0    memory-layout-test
```

**Step 2:**

Let us add one global variable in the program, now check the size of bss.

```C
#include <stdio.h>

int embetronicx;   //uninitialized global variable. It will stored in bss

int main(void)
{
    return 0;
}
```

Compile and Check Memory.

```C
[embetronicx@linux]$ gcc memory-layout-test.c -o memory-layout-test

[embetronicx@linux]$ size memory-layout-test

text       data        bss        dec        hex    filename
960        248         12         1220       4c4    memory-layout-test
```

**Step 3:**

Let us add one static variable which is also stored in bss.

```C
#include <stdio.h>

int embetronicx;   //uninitialized global variable. It will stored in bss

int main(void)
{
    static int i;  //Uninitialized static variable stored in bss 
    return 0;
}
```

Compile and Check.

```C
[embetronicx@linux]$ gcc memory-layout-test.c -o memory-layout-test

[embetronicx@linux]$ size memory-layout-test

text       data        bss        dec          hex    filename

960        248         16         1224         4c4    memory-layout-test
```

**Step 4:**

Let us initialize the static variable to non zero which will then be stored in Initialized Data Segment (DS).

```C
#include <stdio.h>

int embetronicx;   //uninitialized global variable. It will stored in bss

int main(void)
{
    static int i = 10;  //Initialized static variable stored in Initialized Data Segment
    return 0;
}
```

Compile and Check.

```C
[embetronicx@linux]$ gcc memory-layout-test.c -o memory-layout-test

[embetronicx@linux]$ size memory-layout-test

text       data        bss        dec          hex    filename

960        252         12         1224         4c4    memory-layout-test
```

**Step 5:**

Let us initialize the global variable to non zero which will then be stored in Initialized Data Segment (DS).

```C
#include <stdio.h>

int embetronicx = 100;   //Initialized Global variable stored in Initialized Data Segment

int main(void)
{
    static int i = 10;  //Initialized static variable stored in Initialized Data Segment
    return 0;
}
```

Compile and Check

```C
[embetronicx@linux]$ gcc memory-layout-test.c -o memory-layout-test

[embetronicx@linux]$ size memory-layout-test

text       data        bss        dec          hex    filename

960        256          8         1224         4c4    memory-layout-test
```
