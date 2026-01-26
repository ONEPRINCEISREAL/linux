# linux
Linux learning notes covering architecture, processes, and systemd

## You can say this in Interview
Linux follows a layered architecture where users interact with applications and the shell, which communicates with the kernel. The kernel manages hardware resources like CPU, memory, and storage.

### Linux Architecture Flow
----------------------------------------------------------------
User
 ↓
Applications
 ↓
Shell
 ↓
Kernel
 ↓
Hardware
-----------------------------------------------------------------

## User Space
User space contains all user-level applications and tools.
These programs cannot directly access hardware.

Examples:
- Shell (bash)
- Docker
- Nginx
- System utilities (ls, ps, top)

## Kernel Space
Kernel space is where the Linux kernel runs.
It has full access to system hardware.

Responsibilities:
- Managing processes and CPU scheduling
- Handling memory allocation
- Managing file systems
- Controlling networking
- Interfacing with hardware via device drivers

## System Calls
System calls allow user-space applications to request services from the kernel.

Example:
- "ls" → requests file information from kernel
- "docker run" → kernel creates processes and allocates resources

## Why This Matters for DevOps
- Linux is the base OS for most servers
- Containers depend on the Linux kernel
- Understanding kernel behavior helps debug performance issues
- Improves troubleshooting of CPU, memory, disk, and network problems

## Conclusion
Understanding Linux architecture helps DevOps engineers work more confidently
with servers, containers, and production systems.

