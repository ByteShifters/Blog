---
title: "Introduction to Windows Syscalls"
tags: ['Windows', 'Low level', 'Malware', 'Rootkits']
date: 2024-09-17
toc: true
---

# Introduction to Syscalls in Windows

System calls, or syscalls, are the primary means through which applications interact with the operating system (OS). They provide a controlled interface for programs to request services from the OS kernel, such as file operations, process management, and communication. This introduction will delve into Windows system calls, their structure, advanced usage, performance considerations, and best practices.

## What are System Calls?

System calls act as a bridge between user applications and the kernel, allowing applications to execute privileged operations safely. In Windows, these calls are encapsulated within various libraries, most notably the Windows API (WinAPI).

### Key Points about System Calls:
- **Privileged Access**: Syscalls run in kernel mode, while user applications run in user mode.
- **Error Handling**: Syscalls provide mechanisms for error reporting through return values and `GetLastError`.
- **Performance**: Directly invoking syscalls can be more efficient than going through higher-level APIs, though it is less portable.
- **Security**: Syscalls are critical in maintaining system security by ensuring that user-mode applications cannot perform unauthorized actions.
- **Context Switching**: Transitioning between user mode and kernel mode incurs overhead, making the efficiency of syscall usage crucial.

## Windows System Call Mechanism

In Windows, system calls can be made using various mechanisms, including:

1. **WinAPI Functions**: High-level functions that internally invoke syscalls.
2. **Native API**: Lower-level functions directly implemented in the Windows NT kernel, such as `NtCreateFile`.

### The Role of the Windows API

The Windows API provides a vast array of functions for different operations. Each API function typically maps to one or more syscalls, providing a more user-friendly interface. For example, `CreateFile` abstracts the underlying syscall that interacts with the file system.

### Common Windows Syscalls

| Syscall Category      | Description                                     | Example Function          |
|----------------------|-------------------------------------------------|---------------------------|
| **File Operations**   | Access and manipulate files and directories     | `CreateFile`, `ReadFile`, `WriteFile`, `DeleteFile`, `SetFilePointer` |
| **Process Management**| Control and manage processes and threads        | `CreateProcess`, `TerminateProcess`, `GetExitCodeProcess`, `WaitForSingleObject`, `OpenProcess` |
| **Memory Management** | Allocate and manage memory resources            | `VirtualAlloc`, `VirtualFree`, `MapViewOfFile`, `VirtualQuery`, `HeapAlloc` |
| **Synchronization**   | Coordinate activities between threads           | `CreateMutex`, `WaitForSingleObject`, `SetEvent`, `CreateSemaphore`, `WaitForMultipleObjects` |
| **Networking**       | Manage network connections and data transfer     | `WSASocket`, `send`, `recv`, `bind`, `listen`, `WSAStartup`, `WSAAsyncSelect` |
| **Registry Access**   | Access and manipulate the Windows registry      | `RegOpenKeyEx`, `RegSetValueEx`, `RegCloseKey`, `RegQueryValueEx` |

## How to Make System Calls in Windows

### Using WinAPI

Most developers utilize the Windows API for system calls, as it abstracts the complexity involved in direct syscall interaction. For instance, to create a file with error handling:

```
#include <windows.h>
#include <stdio.h>

int main() {
    HANDLE hFile = CreateFile(
        "example.txt",             // File name
        GENERIC_WRITE,             // Desired access
        0,                         // Share mode
        NULL,                      // Security attributes
        CREATE_NEW,               // Creation disposition
        FILE_ATTRIBUTE_NORMAL,     // File attributes
        NULL                       // Template file
    );

    if (hFile == INVALID_HANDLE_VALUE) {
        printf("Error creating file: %lu\n", GetLastError());
        return 1;
    }

    // Perform file operations...
    CloseHandle(hFile);
    return 0;
}
```

### Advanced File Operations

To perform advanced file operations, such as reading from a file, you can use the `ReadFile` function. Here's an example that reads data from a file and handles various errors:

```
#include <windows.h>
#include <stdio.h>

int main() {
    HANDLE hFile = CreateFile("example.txt", GENERIC_READ, 0, NULL, OPEN_EXISTING, FILE_ATTRIBUTE_NORMAL, NULL);
    
    if (hFile == INVALID_HANDLE_VALUE) {
        printf("Error opening file: %lu\n", GetLastError());
        return 1;
    }

    char buffer[100];
    DWORD bytesRead;
    if (!ReadFile(hFile, buffer, sizeof(buffer) - 1, &bytesRead, NULL)) {
        printf("Error reading file: %lu\n", GetLastError());
        CloseHandle(hFile);
        return 1;
    }
    
    buffer[bytesRead] = '\0'; // Null-terminate the string
    printf("Read from file: %s\n", buffer);
    CloseHandle(hFile);
    return 0;
}
```

### Writing to a File

You can also write to a file using `WriteFile`. Here’s an example:

```
#include <windows.h>
#include <stdio.h>

int main() {
    HANDLE hFile = CreateFile("example.txt", GENERIC_WRITE, 0, NULL, OPEN_EXISTING, FILE_ATTRIBUTE_NORMAL, NULL);
    
    if (hFile == INVALID_HANDLE_VALUE) {
        printf("Error opening file for writing: %lu\n", GetLastError());
        return 1;
    }

    const char *data = "Hello, World!";
    DWORD bytesWritten;
    if (!WriteFile(hFile, data, strlen(data), &bytesWritten, NULL)) {
        printf("Error writing to file: %lu\n", GetLastError());
        CloseHandle(hFile);
        return 1;
    }
    
    printf("Successfully wrote %lu bytes.\n", bytesWritten);
    CloseHandle(hFile);
    return 0;
}
```

### Using Native API

For advanced use cases, you might need to interact with the Native API, which requires a deeper understanding of the Windows kernel. Here’s an example of using the `NtCreateFile` syscall:

```
#include <windows.h>
#include <winternl.h>

// Define the NtCreateFile prototype
typedef NTSTATUS (NTAPI *NtCreateFilePtr)(
    PHANDLE FileHandle,
    ACCESS_MASK DesiredAccess,
    POBJECT_ATTRIBUTES ObjectAttributes,
    PIO_STATUS_BLOCK IoStatusBlock,
    PLARGE_INTEGER AllocationSize,
    ULONG FileAttributes,
    ULONG ShareAccess,
    ULONG CreateDisposition,
    ULONG CreateOptions,
    PVOID EaBuffer,
    ULONG EaLength
);

int main() {
    HANDLE hFile;
    IO_STATUS_BLOCK ioStatusBlock;
    OBJECT_ATTRIBUTES objAttr;
    UNICODE_STRING fileName;

    RtlInitUnicodeString(&fileName, L"example.txt");
    InitializeObjectAttributes(&objAttr, &fileName, OBJ_CASE_INSENSITIVE, NULL, NULL);

    // Load the Native API DLL and get the function pointer
    HMODULE ntdll = GetModuleHandleA("ntdll.dll");
    NtCreateFilePtr NtCreateFile = (NtCreateFilePtr)GetProcAddress(ntdll, "NtCreateFile");

    NTSTATUS status = NtCreateFile(
        &hFile,
        GENERIC_WRITE,
        &objAttr,
        &ioStatusBlock,
        NULL,
        FILE_ATTRIBUTE_NORMAL,
        0,
        FILE_SUPERSEDE,
        0,
        NULL,
        0
    );

    if (status < 0) {
        printf("Error creating file: %lx\n", status);
        return 1;
    }

    // Perform file operations...
    CloseHandle(hFile);
    return 0;
}
```

### Advanced Process Management

Managing processes is a common task that often requires multiple syscalls. For example, to create a new process and wait for it to complete:

```
#include <windows.h>
#include <stdio.h>

int main() {
    STARTUPINFO si;
    PROCESS_INFORMATION pi;

    ZeroMemory(&si, sizeof(si));
    si.cb = sizeof(si);
    ZeroMemory(&pi, sizeof(pi));

    // Create a new process
    if (!CreateProcess(NULL,   // No module name (use command line)
                       "notepad.exe", // Command line
                       NULL,        // Process handle not inheritable
                       NULL,        // Thread handle not inheritable
                       FALSE,       // Set handle inheritance to FALSE
                       0,           // No creation flags
                       NULL,        // Use parent's environment block
                       NULL,        // Use parent's starting directory 
                       &si,         // Pointer to STARTUPINFO structure
                       &pi)         // Pointer to PROCESS_INFORMATION structure
    ) {
        printf("CreateProcess failed (%lu).\n", GetLastError());
        return 1;
    }

    // Wait until child process exits
    WaitForSingleObject(pi.hProcess, INFINITE);

    // Close process and thread handles. 
    CloseHandle(pi.hProcess);
    CloseHandle(pi.hThread);
    return 0;
}
```

### Memory Management

When allocating memory, use `VirtualAlloc` and `VirtualFree` for custom memory management:

```
#include <windows.h>
#include <stdio.h>

int main() {
    SIZE_T size = 1024; // 1 KB
    LPVOID pMemory = VirtualAlloc(NULL, size, MEM_COMMIT | MEM_RESERVE, PAGE_READWRITE);
    
    if (pMemory == NULL) {
        printf("Error allocating memory: %lu\n", GetLastError());
        return 1;
    }

    // Use the allocated memory...
    ZeroMemory(pMemory, size); // Initialize the memory

    // Free the allocated memory
    if (!VirtualFree(pMemory, 0, MEM_RELEASE)) {
        printf("Error freeing memory: %lu\n", GetLastError());
        return 1;
    }

    return 0;
}
```

### Synchronization Mechanisms

Using synchronization objects is crucial when dealing with multithreading to avoid race conditions. Here’s how to use a mutex:

```
#include <windows.h>
#include <stdio.h>

HANDLE hMutex;

DWORD WINAPI ThreadFunc(LPVOID lpParam) {
    WaitForSingleObject(hMutex, INFINITE); // Wait for mutex

    // Critical section
    printf("Thread %d is in the critical section.\n", GetCurrentThreadId());
    Sleep(1000); // Simulate work

    ReleaseMutex(hMutex); // Release mutex
    return 0;
}

int main() {
    hMutex = CreateMutex(NULL, FALSE, NULL);

    HANDLE threads[3];
    for (int i = 0; i < 3; i++) {
        threads[i] = CreateThread(NULL, 0, ThreadFunc, NULL, 0, NULL);
    }

    WaitForMultipleObjects(3, threads, TRUE, INFINITE); // Wait for all threads to finish

    CloseHandle(hMutex);
    for (int i = 0; i < 3; i++) {
        CloseHandle(threads[i]);
    }

    return 0;
}
```

### Networking Example

Using syscalls for network communication involves setting up sockets. Here’s a simple TCP server:

```
#include <winsock2.h>
#include <ws2tcpip.h>
#include <stdio.h>

#pragma comment(lib, "ws2_32.lib")

int main() {
    WSADATA wsaData;
    SOCKET listenSocket, clientSocket;
    struct sockaddr_in serverAddr, clientAddr;
    int clientAddrSize = sizeof(clientAddr);

    // Initialize Winsock
    WSAStartup(MAKEWORD(2, 2), &wsaData);

    // Create a socket
    listenSocket = socket(AF_INET, SOCK_STREAM, IPPROTO_TCP);
    
    serverAddr.sin_family = AF_INET;
    serverAddr.sin_addr.s_addr = INADDR_ANY;
    serverAddr.sin_port = htons(8080);

    // Bind the socket
    bind(listenSocket, (struct sockaddr*)&serverAddr, sizeof(serverAddr));
    
    // Listen for incoming connections
    listen(listenSocket, SOMAXCONN);
    printf("Listening on port 8080...\n");

    // Accept a client socket
    clientSocket = accept(listenSocket, (struct sockaddr*)&clientAddr, &clientAddrSize);
    printf("Client connected.\n");

    // Clean up
    closesocket(clientSocket);
    closesocket(listenSocket);
    WSACleanup();
    return 0;
}
```

### Error Handling and Best Practices

When using syscalls, error handling is crucial. Use `GetLastError()` for WinAPI calls and check the return value for Native API functions. Always ensure that resources (like file handles) and synchronization objects are properly released to avoid leaks.

#### Memory Management

When allocating memory, use `VirtualAlloc` and `VirtualFree` for custom memory management:

```
#include <windows.h>
#include <stdio.h>

int main() {
    SIZE_T size = 1024; // 1 KB
    LPVOID pMemory = VirtualAlloc(NULL, size, MEM_COMMIT | MEM_RESERVE, PAGE_READWRITE);
    
    if (pMemory == NULL) {
        printf("Error allocating memory: %lu\n", GetLastError());
        return 1;
    }

    // Use the allocated memory...
    ZeroMemory(pMemory, size); // Initialize the memory

    // Free the allocated memory
    if (!VirtualFree(pMemory, 0, MEM_RELEASE)) {
        printf("Error freeing memory: %lu\n", GetLastError());
        return 1;
    }

    return 0;
}
```

### Performance Considerations

- **Batch Operations**: When performing multiple file operations, consider batching them to reduce syscall overhead.
- **Asynchronous I/O**: Use asynchronous I/O to improve performance, especially in applications that require high responsiveness. Functions like `ReadFileEx` and `WriteFileEx` can be used for this purpose.
- **Memory-Mapped Files**: Utilize memory-mapped files for efficient file I/O operations, allowing files to be mapped directly into memory.
- **Thread Pooling**: Instead of creating and destroying threads frequently, use a thread pool to manage worker threads efficiently.

## Resources for Further Learning

- [Microsoft Documentation on Windows API](https://learn.microsoft.com/en-us/windows/win32/apiindex/windows-api-list)
- [Windows Syscalls Overview](https://learn.microsoft.com/en-us/windows-hardware/drivers/develop/)
- [Advanced Windows Debugging Techniques](https://learn.microsoft.com/en-us/windows-hardware/drivers/debugger/)
- [Undocumented Windows Internals](http://undocumented.ntinternals.net/)
## Conclusion

Understanding system calls is crucial for developing efficient and robust applications on Windows. By utilizing the Windows API, developers can leverage the power of syscalls while maintaining system stability and security. Whether you are managing files, processes, or memory, mastering syscalls will enhance your programming capabilities on the Windows platform. Additionally, adopting best practices for performance and error handling will lead to more resilient applications.

In summary, syscalls are not just a means to access operating system resources but also a critical aspect of system security and performance. A thorough understanding of these concepts will serve as a strong foundation for advanced Windows development.
