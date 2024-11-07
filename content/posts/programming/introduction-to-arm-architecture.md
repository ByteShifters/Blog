---
title: "Introduction to ARM Architecture"
tags: ['ASM', 'ARM', 'Architecture', 'Low level']
date: 2024-09-21
author: 'Ren'
authorlink: 'https://about.0x0060.dev/'
toc: true
---

## Introduction to ARM Architecture

ARM is a widely-used architecture in various devices due to its efficiency and power-saving features. Understanding ARM assembly provides you with the ability to write low-level code that directly interacts with hardware.

### ARM Versions

ARM has several versions, including ARMv7 (32-bit) and ARMv8 (64-bit). Each version introduces new instructions and capabilities.

## Basic Concepts

### Registers

Registers are crucial for ARM assembly programming. Each register has specific roles:

| Register | Purpose                           |
|----------|-----------------------------------|
| R0-R12   | General-purpose registers          |
| R13      | Stack pointer (SP)                |
| R14      | Link register (LR)                |
| R15      | Program counter (PC)              |
| CPSR     | Current Program Status Register    |

#### Special Purpose Registers

- **SP (R13)**: Points to the top of the stack, used for storing return addresses and local variables.
- **LR (R14)**: Used to store return addresses when calling functions.

### Instruction Set

ARM provides a rich set of instructions. Here are a few categories with examples:

| Instruction Type        | Example                     | Description                          |
|-------------------------|-----------------------------|--------------------------------------|
| Data Processing         | `ADD R0, R1, R2`           | Adds R1 and R2, stores in R0.       |
| Load/Store              | `LDR R0, [R1]`             | Loads data from address in R1.      |
| Control Flow            | `B label`                  | Branches to the specified label.    |
| Bitwise                 | `EOR R0, R1, R2`           | Exclusive ORs R1 and R2, stores in R0.|

## Assembly Syntax

The syntax for ARM assembly is quite straightforward. Here’s a breakdown:

```
LABEL:  INSTRUCTION  OPERAND
```

### Example with Comments

```
start:  MOV R0, #5        ; Move the value 5 into R0
        MOV R1, #10       ; Move the value 10 into R1
        ADD R2, R0, R1    ; Add R0 and R1, store result in R2
```

## Data Types

ARM deals primarily with the following data types:

| Data Type | Size       | Description                              |
|-----------|------------|------------------------------------------|
| Byte      | 8 bits     | Typically used for characters.          |
| Halfword  | 16 bits    | Used for smaller integers.               |
| Word      | 32 bits    | Common size for integers.                |
| Double    | 64 bits    | Used for larger integers and floats.     |

## Addressing Modes

ARM supports several addressing modes to access memory efficiently.

| Addressing Mode | Example              | Description                           |
|-----------------|----------------------|---------------------------------------|
| Immediate        | `MOV R0, #5`        | Load an immediate constant into a register. |
| Register         | `LDR R1, [R2]`      | Load value from the address in R2.   |
| Base Offset      | `LDR R3, [R4, #8]`  | Load from address R4 plus offset 8.  |
| PC-Relative      | `LDR R0, [PC, #4]`  | Load from address relative to the PC. |

### Example of Addressing Modes

```
LDR R0, =0x1000        ; Immediate addressing
LDR R1, [R2]           ; Register addressing
LDR R3, [R4, #8]       ; Base offset addressing
LDR R4, [PC, #4]       ; PC-relative addressing
```

## Control Flow

Control flow instructions determine how the program executes.

### Branching

The `B` instruction allows for branching to labels based on conditions.

#### Conditional Branching Example

```
    CMP R0, R1              ; Compare R0 and R1
    BEQ equal               ; Branch if equal
    B end                   ; Unconditional branch to end
equal:
    ; Do something if R0 == R1
end:
```

### Loops

Loops are implemented using branching and conditional checks.

```
    MOV R0, #0             ; Initialize counter
loop:
    CMP R0, #10            ; Compare counter with 10
    BGT end_loop           ; Exit if counter > 10
    ; Do something (e.g., print)
    ADD R0, R0, #1         ; Increment counter
    B loop                 ; Repeat loop
end_loop:
```

## Procedures (Functions)

### Calling a Function

Functions can be implemented using the `BL` instruction.

```
    BL my_function          ; Call function
    ; Code continues here after the function
my_function:
    ; Function code
    MOV PC, LR              ; Return from function
```

### Example with Parameters

```
my_function:
    ; Assume R0 contains a parameter
    ADD R0, R0, #1          ; Increment parameter
    MOV PC, LR              ; Return
```

## Advanced Topics

### Bitwise Operations

Bitwise operations manipulate bits directly, which is essential for low-level programming.

```
MOV R0, #0xFF          ; Load R0 with 255
AND R1, R0, #0x0F      ; AND operation (R1 = 15)
ORR R2, R0, #0xF0      ; OR operation (R2 = 255)
EOR R3, R0, R1         ; Exclusive OR (R3 = 240)
```

### Shifting and Rotating

Shifting and rotating bits are common operations.

```
LSR R0, R1, #2         ; Logical Shift Right (R0 = R1 >> 2)
ASR R2, R3, #1         ; Arithmetic Shift Right (R2 = R3 >> 1)
ROR R4, R5, #3         ; Rotate Right (R4 = R5 rotated by 3)
```

### Debugging Techniques

Debugging in ARM assembly can be challenging. Here are some techniques:

- **Use of Comments**: Clearly comment your code to track the flow.
- **Registers Monitoring**: Check register values at various stages.
- **Breakpoint Insertion**: Use a debugger to set breakpoints and step through code.

## Example Program: Factorial Calculation

Here’s a complete program that calculates the factorial of a number.

```
    .data
number:  .word 5              ; Input number for factorial
result:  .word 0              ; To store result

    .text
    .global _start

_start:
    LDR R0, =number          ; Load address of number
    LDR R1, [R0]             ; Load number into R1
    MOV R2, #1               ; Initialize result to 1
    MOV R3, #1               ; Initialize counter

factorial_loop:
    CMP R3, R1               ; Compare counter with number
    BGT end_factorial        ; If counter > number, exit
    MUL R2, R2, R3           ; Multiply result by counter
    ADD R3, R3, #1           ; Increment counter
    B factorial_loop         ; Repeat loop

end_factorial:
    LDR R0, =result          ; Load address to store result
    STR R2, [R0]             ; Store the result
    ; Exit program (implementation-dependent)
```

## Example Program: Fibonacci Sequence

Here’s another example that calculates the Fibonacci sequence.

```
    .data
n:  .word 10                 ; Number of Fibonacci terms
result: .space 40            ; Space for results (10 terms)

    .text
    .global _start

_start:
    LDR R0, =n               ; Load number of terms
    LDR R1, [R0]             ; Load n into R1
    MOV R2, #0               ; First Fibonacci term
    MOV R3, #1               ; Second Fibonacci term
    MOV R4, #0               ; Counter

write_fib:
    CMP R4, R1               ; Compare counter with n
    BGE end_fib              ; Exit if counter >= n
    LDR R5, =result          ; Load result address
    STR R2, [R5, R4, LSL #2] ; Store Fibonacci term (4 bytes each)
    
    ADD R6, R2, R3           ; Calculate next term
    MOV R2, R3               ; Update R2 to next term
    MOV R3, R6               ; Update R3 to current term
    ADD R4, R4, #1           ; Increment counter
    B write_fib              ; Repeat loop

end_fib:
    ; Exit program (implementation-dependent)
```

## Performance Optimization

Optimizing your ARM assembly code can yield significant performance improvements. Here are some strategies:

### Loop Unrolling

Instead of a single loop iteration, process multiple items in one go.

```
loop_unrolled:
    CMP R0, #10
    BGE end_loop
    ; Process two items at once
    ADD R1, R1, R2          ; R1 = R1 + R2 (first item)
    ADD R1, R1, R2          ; R1 = R1 + R2 (second item)
    ADD R0, R0, #2          ; Increment counter by 2
    B loop_unrolled
end_loop:
```

### Reducing Memory Access

Minimize memory access by keeping frequently used variables in registers.

```
MOV R0, R1              ; Use R0 instead of accessing memory
ADD R0, R0, #10         ; Perform operations in registers
```

## Resources for Further Learning

- **ARM Architecture Reference Manual**: Detailed documentation of the ARM architecture.
- **Assembly Language for x86 Processors**: Although focused on x86, many principles apply to ARM.
- **ARM Developer Website**: Offers tutorials, documentation, and tools.
- **Online Courses**: Look for platforms like Coursera and Udemy for structured learning.

## Conclusion

ARM assembly language is a powerful tool for low-level programming and optimization. This extended guide covers foundational concepts, advanced techniques, and practical applications. By practicing and experimenting with the examples provided, you can build a solid understanding of ARM assembly programming.
