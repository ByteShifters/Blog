---
title: "Custom linux kernel Hooks with eBPF"
tags: ['System Security', 'Low level', 'Rootkits', 'Malware']
date: 2024-10-02
toc: true
---

# Introduction to eBPF: Developing Custom Hooks in the Linux Kernel

eBPF (Extended Berkeley Packet Filter) is a powerful Linux kernel technology that enables the development of custom tracing, networking, and security tools. By creating highly efficient and flexible hooks into the kernel, eBPF allows for deep insights into system behavior and high-performance packet filtering.

In this guide, we’ll cover the fundamentals of developing eBPF hooks, exploring the tools required, and providing code snippets to help you get started on your journey with eBPF.

## Understanding eBPF

### What is eBPF?

eBPF extends the original BPF (Berkeley Packet Filter) by introducing a versatile in-kernel virtual machine that allows users to run bytecode safely in a sandboxed environment. Originally, BPF was used to filter network packets, but eBPF has evolved into a general-purpose framework for observing and modifying kernel behavior dynamically.

The benefits of eBPF include:

- **Safety**: eBPF programs are verified before execution, ensuring they don't crash the kernel or cause instability.
- **Performance**: eBPF programs are JIT-compiled into native code and executed in kernel space, minimizing the overhead.
- **Flexibility**: eBPF can be used for a variety of use cases, including tracing, networking, and security applications.

### How eBPF Programs Work

eBPF programs are typically written in a restricted C-like language. They are compiled into eBPF bytecode using the LLVM/Clang compiler and then loaded into the kernel. Once in the kernel, eBPF programs can be attached to different types of hooks and executed in response to specific events.

### eBPF Hooks

eBPF hooks are the points in the kernel where eBPF programs are attached. These hooks allow eBPF programs to execute when specific kernel or user-space events occur. Some common eBPF hook types include:

- **Kprobes**: Dynamic kernel probes that trace kernel function entries and exits.
- **Uprobes**: User-space probes that trace function calls in user-space applications.
- **Tracepoints**: Static probes that trace specific kernel events.
- **XDP (eXpress Data Path)**: High-performance hooks for processing network packets at the lowest point in the networking stack.
- **cgroup-bpf**: Hooks that enforce eBPF programs on specific control groups (cgroups), enabling resource management and security policies.

By attaching eBPF programs to these hooks, you can create powerful custom tools to monitor, analyze, and even modify system behavior in real time.

## Tools for Developing eBPF Hooks

To start developing eBPF hooks, you'll need the following tools:

### LLVM/Clang

LLVM and Clang are essential for compiling eBPF programs. Clang is a C language compiler and is part of the LLVM project. It compiles eBPF source code into eBPF bytecode, which can then be loaded into the kernel.

Example command to compile an eBPF program:

```
clang -O2 -target bpf -c my_ebpf_program.c -o my_ebpf_program.o
```

### BCC (BPF Compiler Collection)

BCC is a toolkit that simplifies the process of writing, compiling, and debugging eBPF programs. It includes various Python libraries, helper functions, and tools like `bcc-tools` for managing eBPF programs.

### bpftool

`bpftool` is a command-line utility for managing eBPF programs and maps. It can load, unload, inspect, and query eBPF objects and is invaluable for troubleshooting and verifying your eBPF programs.

### libbpf

`libbpf` is a low-level C library for loading and managing eBPF programs and maps. It provides a straightforward C API for interacting with eBPF and is the recommended way to interface with eBPF when developing in C.

## Example: Counting System Calls with eBPF

The following example demonstrates a simple eBPF program that counts the number of system calls made by different processes. This eBPF program uses a hash map to store the count of system calls per process.

**eBPF Program: syscall_count.c**

```
#include <uapi/linux/ptrace.h>
#include <linux/sched.h>

BPF_HASH(syscall_count, u32); // Define a BPF hash map to store syscall counts

int count_syscalls(struct pt_regs *ctx) {
    u32 pid = bpf_get_current_pid_tgid();  // Get the current process ID
    u64 *count = syscall_count.lookup(&pid);  // Lookup the count for this PID

    if (!count) {
        u64 init_val = 1;
        syscall_count.update(&pid, &init_val);  // Initialize the count if PID is not present
    } else {
        (*count)++;  // Increment the syscall count
        syscall_count.update(&pid, count);  // Update the count in the hash map
    }

    return 0;
}
```

### Compiling the eBPF Program

Compile the eBPF program using Clang with the following command:

```
clang -O2 -target bpf -c syscall_count.c -o syscall_count.o
```

### Attaching the eBPF Program Using BCC

In this example, we use Python with BCC to load and attach the `count_syscalls` eBPF program to a kprobe on the `__x64_sys_execve` kernel function, which is triggered when a process executes a new program.

```
from bcc import BPF
import time

# Load eBPF program from the source file
b = BPF(src_file="syscall_count.c")

# Attach the eBPF program to the kprobe for the `__x64_sys_execve` function
b.attach_kprobe(event="__x64_sys_execve", fn_name="count_syscalls")

# Print syscall counts every 5 seconds
while True:
    print("=== Syscall Counts ===")
    for k, v in b["syscall_count"].items():
        print(f"PID {k.value}: {v.value} syscalls")
    time.sleep(5)
```

### Debugging and Performance Tuning

Debugging eBPF programs can be challenging due to their execution in kernel space. However, the following techniques can assist in troubleshooting and optimizing your programs:

1. **Print Debug Messages**: Use `bpf_trace_printk` to print messages from your eBPF program to the trace buffer. You can view these messages using `cat /sys/kernel/debug/tracing/trace_pipe`.
2. **Verify eBPF Programs**: Use `bpftool` to verify and inspect eBPF programs. This helps identify issues like unsupported instructions or invalid memory access.
3. **Monitor with BCC**: Use BCC's Python API to print debug information and access eBPF maps dynamically.

### Performance Optimization Tips

To ensure your eBPF programs run efficiently, consider these performance optimization tips:

- **Minimize Loops and Branches**: Complex control flows can increase execution time and may be restricted by the eBPF verifier.
- **Optimize Data Structures**: Use appropriate BPF map types, such as hash maps or array maps, depending on your use case.
- **Limit Memory Usage**: Use the Least Recently Used (LRU) eviction policy in BPF maps to prevent excessive memory consumption.
- **Use Hardware Offloading**: For networking-related eBPF programs, hardware offloading can improve performance by processing packets directly on network cards.

## Conclusion

This guide provided an overview of eBPF, its hooks, and the tools necessary to develop and debug eBPF programs. With the example of a syscall counting program, you can see how easy it is to get started with eBPF and build custom observability or performance tools. As you explore further, you’ll find that eBPF opens up endless possibilities for creating efficient, safe, and powerful solutions for tracing, networking, and security in the Linux kernel.
