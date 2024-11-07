---
title: "Introduction to x64 ASM"
tags: ['C', 'Low level', 'ASM']
date: 2024-09-21
author: 'Ren'
authorlink: 'https://about.0x0060.dev/'
toc: true
---


## 1. Introduction to Assembly and x64 Architecture

### What is Assembly Language?
Assembly language is a low-level programming language that is closely related to machine code. Each instruction corresponds directly to machine instructions executed by the CPU. Unlike high-level languages, assembly is architecture-specific, meaning that assembly code written for one architecture (e.g., x86) won't work on another (e.g., ARM).

In this tutorial, we'll focus on x64 assembly, the 64-bit extension of the x86 architecture. It's used in modern desktop processors (Intel and AMD).


## 2. x64 Registers

In x64, there are 16 general-purpose registers, each 64 bits in size. These registers are used for holding temporary data, addresses, or counters during program execution.

| Register | Description                        | Alias (32-bit) | Alias (16-bit) | Alias (8-bit) |
|----------|------------------------------------|----------------|----------------|---------------|
| RAX      | Accumulator for operations         | EAX            | AX             | AL            |
| RBX      | Base register (used for pointers)  | EBX            | BX             | BL            |
| RCX      | Counter for loops/operations       | ECX            | CX             | CL            |
| RDX      | Data register (I/O operations)     | EDX            | DX             | DL            |
| RSI      | Source index for data copying      | ESI            | SI             | SIL           |
| RDI      | Destination index for data copying | EDI            | DI             | DIL           |
| RBP      | Base pointer for stack frames      | EBP            | BP             | BPL           |
| RSP      | Stack pointer for current stack    | ESP            | SP             | SPL           |
| R8       | General-purpose register           | R8D            | R8W            | R8B           |
| R9       | General-purpose register           | R9D            | R9W            | R9B           |
| R10      | General-purpose register           | R10D           | R10W           | R10B          |
| R11      | General-purpose register           | R11D           | R11W           | R11B          |
| R12      | General-purpose register           | R12D           | R12W           | R12B          |
| R13      | General-purpose register           | R13D           | R13W           | R13B          |
| R14      | General-purpose register           | R14D           | R14W           | R14B          |
| R15      | General-purpose register           | R15D           | R15W           | R15B          |

---

## 3. Basic Assembly Instructions

### Basic Syntax
In Intel syntax, assembly instructions generally follow this structure:

For example:

```
mov rax, rbx  ; Copy value of RBX into RAX
add rax, 5    ; Add 5 to the value in RAX
```

### Basic Instructions

| Instruction | Description                          |
|-------------|--------------------------------------|
| `mov`       | Move data between registers/memory   |
| `add`       | Add two values                       |
| `sub`       | Subtract two values                  |
| `mul`       | Multiply values                      |
| `div`       | Divide values                        |
| `inc`       | Increment a value by 1               |
| `dec`       | Decrement a value by 1               |

#### Example:
```
section .data
    num db 5        ; Declare a byte (8-bit) variable with value 5

section .text
    global _start   ; Entry point for the program

_start:
    mov al, [num]   ; Load the value of 'num' into AL (lower byte of RAX)
    inc al          ; Increment the value in AL (AL = 6 now)
    
    ; Exit the program using a syscall
    mov rax, 60     ; Syscall number for exit
    xor rdi, rdi    ; Return 0 (success)
    syscall
```

---

## 4. Memory Addressing

### Direct and Indirect Addressing
In x64, you can directly access memory by specifying the address or indirectly by using registers.

- **Direct Addressing**: 
  ```
  mov rax, [0x1000]  ; Load value at memory address 0x1000 into RAX
  ```

- **Indirect Addressing**:
  ```
  mov rax, [rbx]     ; Load value at the address stored in RBX into RAX
  ```

- **Indexed Addressing**: Combines base and index registers to calculate addresses.
  ```
  mov rax, [rbx+rcx] ; Load value at address (RBX + RCX) into RAX
  ```

---

## 5. Control Flow (Loops and Branches)

### Conditional Jumps

Assembly provides various conditional jump instructions to control the flow of execution based on comparison results.

| Instruction | Description            |
|-------------|------------------------|
| `jmp`       | Unconditional jump     |
| `je`        | Jump if equal          |
| `jne`       | Jump if not equal      |
| `jg`        | Jump if greater        |
| `jl`        | Jump if less           |

#### Example:
```
section .data
    counter db 10    ; Initialize a counter

section .text
    global _start

_start:
    mov rcx, [counter]  ; Load counter value into RCX

.loop:
    dec rcx             ; Decrement the counter
    cmp rcx, 0          ; Compare counter to 0
    jne .loop           ; If not zero, jump back to .loop
    
    ; Exit the program
    mov rax, 60
    xor rdi, rdi
    syscall
```

---

## 6. Functions and Calling Conventions

In assembly, functions are similar to labels and can be called using the `call` instruction. However, x64 follows a specific calling convention where arguments are passed in registers.

### Linux x64 Calling Convention

| Argument | Register |
|----------|----------|
| 1st      | RDI      |
| 2nd      | RSI      |
| 3rd      | RDX      |
| 4th      | RCX      |
| 5th      | R8       |
| 6th      | R9       |

#### Example Function:

```
section .text
    global _start

_start:
    mov rdi, 5     ; First argument (n = 5)
    call factorial ; Call the factorial function
    
    ; Exit the program
    mov rax, 60
    xor rdi, rdi
    syscall

factorial:
    mov rax, 1     ; Initialize result to 1
    cmp rdi, 1     ; Compare n to 1
    jle .done      ; If n <= 1, we're done

.loop:
    mul rdi        ; Multiply RAX by RDI
    dec rdi        ; Decrement n
    cmp rdi, 1     ; Compare n to 1
    jg .loop       ; If n > 1, repeat loop

.done:
    ret            ; Return to caller
```

---

## 7. System Calls in Assembly

In Linux x64, system calls are made using the `syscall` instruction. The system call number is placed in the `RAX` register, and arguments are passed in the following registers: `RDI`, `RSI`, `RDX`, `R10`, `R8`, and `R9`.

| Syscall  | RAX Value |
|----------|-----------|
| `exit`   | 60        |
| `write`  | 1         |
| `read`   | 0         |

#### Example (Writing to stdout):

```
section .data
    msg db 'Hello, World!', 0xA
    len equ $-msg  ; Calculate length of msg

section .text
    global _start

_start:
    mov rax, 1         ; Syscall number for write
    mov rdi, 1         ; File descriptor 1 (stdout)
    mov rsi, msg       ; Pointer to message
    mov rdx, len       ; Length of message
    syscall

    ; Exit the program
    mov rax, 60
    xor rdi, rdi
    syscall
```

---

## 8. Data Types and Structures

### Data Types

| Type | Definition         | Size  |
|------|--------------------|-------|
| `db` | Define byte         | 1 byte |
| `dw` | Define word         | 2 bytes |
| `dd` | Define double word  | 4 bytes |
| `dq` | Define quad word    | 8 bytes |

---

## 9. Interfacing Assembly with C

You can write assembly functions and call them from C programs, or vice versa. This is useful when you need low-level control or optimization.

### Example (Assembly Function in C):

#### Assembly (sum.asm):

```
section .text
    global sum

sum:
    mov rax, rdi   ; Load first argument into RAX
    add rax, rsi   ; Add second argument
    ret            ; Return result
```

#### C (main.c):

```
#include <stdio.h>

extern long sum(long a, long b);

int main() {
    long result = sum(5, 3);
    printf("Result: %ld\n", result);
    return 0;
}
```

### To compile and link:

```
nasm -f elf64 -o sum.o sum.asm
gcc -o main main.c sum.o
```

---

## 10. Advanced Topics

- **SIMD Instructions (SSE, AVX)**: Vectorized operations for floating-point and integer math.
- **Optimization Techniques**: Use of efficient algorithms and assembly-level optimization for performance.
- **Inline Assembly in C**: Embed assembly code directly in C programs for better integration.
- **Multithreading and Concurrency**: Low-level handling of threads and synchronization.

---

This tutorial provides a comprehensive journey into x64 assembly, from the fundamentals to advanced topics. Feel free to explore each section in depth and practice by writing small assembly programs.
