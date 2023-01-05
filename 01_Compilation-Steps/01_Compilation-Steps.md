<https://www.scaler.com/topics/c/compilation-process-in-c/>

## What are the four stages of the compilation process?

Normally the Compiling a C program is a multi-stage process and utilizes different ‘tools’.

1. Preprocessing -> file.i
2. Compilation -> file.s
3. Assembly -> file.o
4. Linking -> file.out

In this post, I’ll walk through each one of the four stages of compiling stages using the following C program:

```C
/*
 * "Hello, World!": A classic.
 */

#include <stdio.h>

int main(void)
{
    puts("Hello, World!");
    return 0;
}
```

### Preprocessing

The first stage of compilation is called preprocessing. In this stage, lines starting with a **`#`** character are interpreted by the **_preprocessor_** as _preprocessor commands_.  Before interpreting the commands, the preprocessor does some initial processing. This includes joining continued lines (lines ending with a **`\`**) and stripping comments.

To print the result of the preprocessing stage, pass the **`-E`** option to **`cc`**:

**`cc -E hello_world.c`**

Given the “Hello, World!” example above, the preprocessor will produce the contents of the **`stdio.h`** header file joined with the contents of the **`hello_world.c`** file, stripped free from its leading comment:

```C
[lines omitted for brevity]

extern int __vsnprintf_chk (char * restrict, size_t,
       int, size_t, const char * restrict, va_list);
# 493 "/usr/include/stdio.h" 2 3 4
# 2 "hello_world.c" 2

int main(void) {
 puts("Hello, World!");
 return 0;
}
```

### Compilation

The second stage of compilation is confusingly enough called compilation. In this stage, the preprocessed code is translated to **_assembly instructions_** specific to the target processor architecture. These form an intermediate human-readable language.

The existence of this step allows for C code to contain inline assembly instructions and for different **_assemblers_** to be used.

Some compilers also support the use of an integrated assembler, in which the compilation stage generates _machine code_ directly, avoiding the overhead of generating the intermediate assembly instructions and invoking the assembler.

To save the result of the compilation stage, pass the **`-S`** option to **`cc`**:

**`cc -S hello_world.c`**

This will create a file named **`hello_world.s`**, containing the generated assembly instructions. On Mac OS 10.10.4, where **`cc`** is an alias for **`clang`**, the following output is generated:

```C
.section    __TEXT,__text,regular,pure_instructions
    .macosx_version_min 10, 10
    .globl  _main
    .align  4, 0x90
_main:                                  ## @main
    .cfi_startproc
## BB#0:
    pushq   %rbp
Ltmp0:
    .cfi_def_cfa_offset 16
Ltmp1:
    .cfi_offset %rbp, -16
    movq    %rsp, %rbp
Ltmp2:
    .cfi_def_cfa_register %rbp
    subq    $16, %rsp
    leaq    L_.str(%rip), %rdi
    movl    $0, -4(%rbp)
    callq   _puts
    xorl    %ecx, %ecx
    movl    %eax, -8(%rbp)          ## 4-byte Spill
    movl    %ecx, %eax
    addq    $16, %rsp
    popq    %rbp
    retq
    .cfi_endproc

    .section    __TEXT,__cstring,cstring_literals
L_.str:                                 ## @.str
    .asciz  "Hello, World!"


.subsections_via_symbols
```

### Assembly

During the assembly stage, an _**assembler**_ is used to translate the assembly instructions to machine code, or **_object code_**. The output consists of actual instructions to be run by the target processor.

To save the result of the assembly stage, pass the **`-c`** option to **`cc`**:

**`cc -c hello_world.c`**

Running the above command will create a file named **`hello_world.o`**, containing the object code of the program. The contents of this file are in a binary format and can be inspected using **`hexdump`** or **`od`** by running either one of the following commands:

```C
hexdump hello_world.o
od -c hello_world.o
```

### Linking

The object code generated in the assembly stage is composed of machine instructions that the processor understands but some pieces of the program are out of order or missing. To produce an executable program, the existing pieces have to be rearranged and the missing ones filled in. This process is called linking.

Let’s assume your project contains two ( **`hello.c`** and **`world.c`)** source files. So, when you compile the project, the assembler will give you **`hello.o`** and **`world.o`** object files. In the end, we need only one binary file that has to be loaded into the target processor or controller. So, the linker will arrange those **`hello.o`** and **`world.o`**, gives a single binary file.

That’s why you will get a error from the linker when you are calling the function which is not defined anywhere. Linker tries to find that function in other source files and throws an error if it couldn’t find that.

The **_linker_** will arrange the pieces of object code so that functions in some pieces can successfully call functions in other pieces. It will also add pieces containing the instructions for library functions used by the program. In the case of the “Hello, World!” program, the linker will add the object code for the **`puts`** function.

The result of this stage is the final executable program. When run without options, `cc` will name this file **`a.out`**. To name the file something else, pass the **`-o`**option to **`cc`**:

**`cc -o hello_world hello_world.c`**

For your quick reference :

[![](https://embetronicx.com/wp-content/uploads/2017/12/Compilation-Steps.png)](https://embetronicx.com/wp-content/uploads/2017/12/Compilation-Steps.png)
