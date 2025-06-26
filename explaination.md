# Kernel-mode
For making a Kernel-mode external you generally need a driver. You interact with the driver from an usermode application via IOCTL codes

Example of IOCTL codes from my driver:
```cpp
// used to setup driver
constexpr ULONG attach =
    CTL_CODE(FILE_DEVICE_UNKNOWN, 0x696, METHOD_BUFFERED, FILE_SPECIAL_ACCESS);

// read process memory
constexpr ULONG read =
    CTL_CODE(FILE_DEVICE_UNKNOWN, 0x697, METHOD_BUFFERED, FILE_SPECIAL_ACCESS);

// write process memory
constexpr ULONG write =
    CTL_CODE(FILE_DEVICE_UNKNOWN, 0x698, METHOD_BUFFERED, FILE_SPECIAL_ACCESS);
```
It can access any application without restrictions, and with advanced methods, can be completely undetected to most anti-cheats.

This is one of the best videos for making a kernel driver and knowing how to use it: https://www.youtube.com/watch?v=n463QJ4cjsU&t=4378s

# User-mode
An User-mode external is a program that controls process memory without a driver.
This method is detected on most anti-cheats, but still not on Hyperion (for some reason)

To open a process for reading and writing normally, you do `HANDLE handle = OpenProcess(PROCESS_ALL_ACCESS, FALSE, <process id>);`

Typically, for reading and writing, you can do
```cpp
uint64_t address_to_write = 0x1234124;
uint64_t address_to_read = 0x4567457;

int to_write = 2;
int data_read;

/*
Syntax

BOOL ReadProcessMemory(
  [in]  HANDLE  hProcess,
  [in]  LPCVOID lpBaseAddress,
  [out] LPVOID  lpBuffer,
  [in]  SIZE_T  nSize,
  [out] SIZE_T  *lpNumberOfBytesRead
);

If lpNumberOfBytesRead is NULL, the parameter is ignored.
*/
ReadProcessMemory(<handle from OpenProcess()>, address_to_read, &data_read, sizeof(int), nullptr);


/*
Syntax

BOOL WriteProcessMemory(
  [in]  HANDLE  hProcess,
  [in]  LPVOID  lpBaseAddress,
  [in]  LPCVOID lpBuffer,
  [in]  SIZE_T  nSize,
  [out] SIZE_T  *lpNumberOfBytesWritten
);

If lpNumberOfBytesWritten is NULL, the parameter is ignored.
*/
WriteProcessMemory(<handle from OpenProcess()>, address_to_write, to_write, sizeof(int), nullptr);
```
This is a simple example on how to read and write memory from a process. You'll need the base of the process though.

OpenProcess: https://learn.microsoft.com/en-us/windows/win32/api/processthreadsapi/nf-processthreadsapi-openprocess
ReadProcessMemory: https://learn.microsoft.com/en-us/windows/win32/api/memoryapi/nf-memoryapi-readprocessmemory
WriteProcessMemory: https://learn.microsoft.com/en-us/windows/win32/api/memoryapi/nf-memoryapi-writeprocessmemory

ReadProcessMemory and WriteProcessMemory are just wrappers of 2 functions: NtReadVirtualMemory and NtWriteVirtualMemory, but these two are undocumented. They are also a bit faster but more complex to use.

yeah
