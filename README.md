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

## Linux Process States

In Linux, a process is a running instance of a program.
Each process exists in a specific state.

# Running (R)
The process is currently executing on the CPU.

# Sleeping (S / D)
The process is waiting for an event or resource.
S → Interruptible sleep
D → Uninterruptible sleep (usually I/O)

# Stopped (T)
The process has been paused manually (for example using Ctrl + Z).

# Zombie (Z)
- The process has finished execution but still exists in the process table
because the parent process has not collected its exit status.

### Common Linux Commands 
These are some commands commonly used in daily Linux and DevOps work:

- ps – Displays running processes

- top – Shows real-time CPU and memory usage

- systemctl – Manages system services

- df – Checks disk space usage

- free – Displays memory usage

## Why Process Knowledge Matters for DevOps
Understanding process states helps DevOps engineers to:

- Identify stuck or zombie processes

- Debug high CPU or memory usage

- Restart or manage services efficiently

- Handle production incidents quickly


# Linux User Creation & EC2 SSH Connection Guide

This README explains how to create a Linux user, understand related commands, and connect to an AWS EC2 instance using SSH from Windows, step by step.

## Creating a New Linux User
Command
sudo useradd -m -s /bin/bash prince

Explanation

sudo
Runs the command with root (administrator) privileges.
Required because only the root user can create new users.

useradd
Creates a new user.

-m
Creates a home directory for the user.
# Without -m, the user will be created without a home folder.

-s /bin/bash
Sets Bash as the default login shell.

prince
Username (replace it with any name you want).

(Optional) Set Password for the User
sudo passwd prince

# Verify User Creation
getent passwd prince

Explanation

getent = get entries

Fetches user information from system databases (like /etc/passwd).

# Important Note (Windows Users)

You cannot directly connect using PowerShell or CMD without proper permissions and setup.
For now, use Git Bash to avoid permission issues.

# Connecting to an EC2 Instance Using SSH (From Windows)
Step 1: Open Command Prompt

Press Win + R

Type cmd

Press Enter

Step 2: Navigate to PEM File Location
cd path/to/your/pem-file


Confirm your current directory:

pwd

Step 3: Copy SSH Command from EC2

Go to AWS Console

Open EC2

Select your instance

Click Connect

Go to SSH client

Copy the command shown there

Example:

ssh -i myserver.pem ec2-user@<IP-address>

# Connecting One EC2 Instance to Another EC2 (Key-Based SSH)
Step 1: Connect to the First EC2 Instance
ssh -i myserver.pem ec2-user@<IP-address>

Step 2: Go to the SSH Directory
ls -a
cd .ssh


(.ssh is a hidden folder)

Step 3: Generate SSH Key
ssh-keygen


When asked for a password, press Enter three times.

Step 4: Check Generated Keys
ls


You will see:

id_ed25519 (private key)

id_ed25519.pub (public key)

## Do NOT use the .pub file to connect.
Always use the private key.

Step 5: Copy Public Key to the Second EC2

Open AWS Console

Go to the second EC2 instance

Click EC2 Instance Connect

Open terminal

Run:

cd .ssh
ls
vim authorized_keys


Steps inside vim:

Press i (insert mode)

Paste the public key from the first EC2

Press Esc

Type :wq and press Enter

Step 6: Test File Creation (Optional)
echo "Hello, this is Prince" > prince.txt

Step 7: Connect to Second EC2 from First EC2
ssh -i id_ed25519 ec2-user@<second-ec2-ip>


✅ You are now connected.

Step 8: Exit
exit


🎉 Task Complete
