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


 You are now connected.

Step 8: Exit
exit


 Task Complete



# SCP (Secure Copy Protocol) – File Transfer Explanation

SCP is used to securely copy files between two systems over SSH.
It works on the source → destination model.

Source → the system where the file currently exists

Destination → the system where you want to transfer the file

SCP uses SSH, so SSH access must be enabled

Works best with private IPs when systems are in the same network (like EC2 instances in the same VPC)

# Prerequisites (Ubuntu)
1️1 Set a Password for the Ubuntu User

You must set a password if password-based SSH/SCP is required.

sudo passwd username


Replace username with your actual Ubuntu username.

2 Update the System
sudo apt-get update

3️ Install Required Packages

(Usually SSH is already installed, but just in case)

sudo apt-get install openssh-server -y

4️ Find the IP Address
ifconfig


or (recommended on modern systems):

ip a


Look for:

Private IP (used for internal/server-to-server communication)

Understanding Source and Destination

You go to the system FROM WHERE you want to transfer the file

That system is the source

The other system is the destination

# “Jahan se tum file transfer kar rahe ho, wahi source hota hai.”

SCP Command Syntax
scp <source_file> <username>@<destination_ip>:<destination_path>

Example: Transfer a File to Ubuntu Server
Situation

File name: test.txt

Destination username: ubuntu

Destination IP: 192.168.1.10

Destination directory: /tmp

Command
scp test.txt ubuntu@192.168.1.10:/tmp


After running the command:

You will be asked for the Ubuntu user password

File transfer will start

If successful, the file will appear in /tmp

Confirm File Transfer on Destination

Login to the destination system and run:

ls /tmp


You should see:

test.txt



ls -l → Shows file permissions, owner, group, size, and last modified time (Linux does not show creation time by default).

File permissions → Decide who can read, write, or execute a file (owner, group, others).

sudo → Gives admin/root privileges required for system-level changes.

sudo useradd -m anyusername → Creates a new user with a home directory and automatically creates a group with the same name.

sudo userdel anyusername → Deletes the user but keeps the home directory.

sudo userdel -r anyusername → Deletes the user along with the home directory.

cat /etc/passwd → Displays all users present in the system (system + normal users).

Default user group → When a user is created, Linux also creates a group with the same name and assigns the user to it.

sudo groupadd groupname → Creates a group but does not add any user by default.

cat /etc/group → Displays all groups present in the system.

sudo usermod -aG groupname username → Adds a user to a group without removing existing group memberships.

Multiple users in a group → Linux does not support adding multiple users at once; run the command separately for each user.

sudo gpasswd -d username groupname → Removes a user from a specific group.

Group permissions → Allow shared access to files among multiple users.

Why sudo is required → User and group management affects system security, so root access is mandatory.


## chown

Changes the ownership of a file or directory.

## chown username filename

Assigns the file to the specified user as the owner.

---

## grep

Searches for a specific word or pattern inside a file.

## grep -i authentication app.log

Searches for the word `authentication` in `app.log` ignoring case sensitivity.

## Authentication failure

Indicates a failed login attempt due to incorrect credentials or permission issues.


## app.log

A log file that stores application or authentication-related events.


## Pipe ( | )

Passes the output of one command as input to another command.


## awk

Used to extract and print specific columns or patterns from files.

## awk '{print}' app.log

Prints all log entries from the file.

## awk '/authentication/ {print}' app.log

Prints only the lines containing the word `authentication`.

## awk '/authentication/ {print $1}' app.log

Prints the first column of lines that contain `authentication`.

## awk '{print $1}' app.log

Prints only the first field from every log entry.


## sed

Stream editor used to search, replace, or modify text output.

## sed -n '/user/ p' log_file

Prints only lines containing the word `user`.

## sed 's/username/USER/g' log_file

Replaces `username` with `USER` only on the screen output.

## Log output modification

`sed` changes the displayed output without modifying the original file.

---

## Duplicate Data in Logs

Logs often contain repeated values like IPs or hostnames.

## sed 's/rhost/IP/g' log_file

Replaces the word `rhost` with `IP` in the output.

## sort

Sorts log entries alphabetically or numerically.

## uniq

Removes duplicate consecutive entries.

## wc

Counts lines, words, or characters.

## sed 's/rhost/IP/g' log_file | sort | uniq | wc -l

Replaces text, removes duplicate entries, and counts unique results.

### Networking Commands

## nslookup

Used to find the IP address of a domain name using DNS.


## tracert (Windows) / traceroute (Linux)

Shows the path packets take from your system to the destination server.


## dig

Provides detailed DNS information such as records, TTL, and name servers.


## wget

Downloads files from the internet using a URL.


## link

Displays or modifies network interface links (used with `ip link` in Linux).


## curl

Transfers data between client and server using URLs.


## curl -X POST

Sends data to a server using the HTTP POST method.


## API (Application Programming Interface)

Allows different applications or services to communicate with each other.

## curl -X POST API Example

Used to send data to an API endpoint for creating or updating resources.


# Linux Volume Management & AWS EBS 


## Linux Volume Management

Linux volume management is used to manage disk storage by creating, resizing, and organizing disks efficiently.


## Introduction to Linux Volumes & AWS EBS

Linux volumes are storage devices attached to the system, while AWS EBS provides block storage volumes for EC2 instances.


## Physical Volume (PV)

A physical volume is the actual disk or partition (for example `/dev/xvdf`) used for storage.


## Volume Group (VG)

A volume group is a pool created by combining one or more physical volumes.


## Logical Volume (LV)

A logical volume is a virtual partition created from a volume group and used to store data.


## Physical vs Logical vs Volume Group

Physical Volume is the real disk, Volume Group is a collection of disks, and Logical Volume is usable storage created from the group.


## Volume Groups in Linux

Volume groups allow flexible storage management by combining multiple disks into one storage pool.


## Mounting Volumes in Linux

Mounting connects a disk or logical volume to a directory so the system can access it.


## Managing AWS EBS on EC2

AWS EBS volumes can be attached, detached, resized, and mounted on EC2 instances for persistent storage.


## Introduction to LVM

LVM (Logical Volume Manager) allows dynamic management of disk space without downtime.


## Logical Volume Manager (LVM)

LVM helps create, resize, and manage logical volumes easily compared to traditional partitions.


## Using LVM with EBS for Dynamic Storage

Using LVM with EBS allows you to expand storage on EC2 instances without restarting the server.


## lsblk

Displays all block devices and shows how disks, partitions, and volumes are connected.

## df -h

Shows disk space usage in a human-readable format.


## sudo

Allows a normal user to run commands with administrator (root) privileges.

## su

Switches the current user to another user, usually the root user.


## LVM (Summary)

LVM provides flexibility, scalability, and efficient storage management in Linux systems.



# Linux LVM Explained with a Real Example (Step by Step)

This example explains how extra disks (like AWS EBS) are converted into **usable storage using LVM**, how data behaves after **mount / unmount**, and why LVM is powerful.


## Step 1: Check Attached Disks

```bash
lsblk
```

This command shows all disks attached to the system, for example:

* `/dev/xvdf`
* `/dev/xvdg`
* `/dev/xvdh`

These are **raw disks** (EBS volumes) and not usable yet.


## Step 2: Check Disk Space Usage

```bash
df -h
```

Shows mounted filesystems and their used/free space in human-readable format.


## Step 3: Create Physical Volumes (PV)

```bash
pvcreate /dev/xvdf /dev/xvdg /dev/xvdh
```

Now these disks become **Physical Volumes**, which LVM can manage.


## Step 4: Verify Physical Volumes

```bash
pvs
```

Shows all physical volumes created in the system.


## Step 5: Create a Volume Group (VG)

```bash
vgcreate PRINCE_VG /dev/xvdf /dev/xvdg /dev/xvdh
```

This combines all disks into **one storage pool** called `PRINCE_VG`.


## Step 6: List Volume Groups

```bash
vgs
```

Confirms that `PRINCE_VG` exists.


## Step 7: Create a Logical Volume (LV)

```bash
lvcreate -L 10G -n PRINCE_ROOT_LV PRINCE_VG
```

This creates a **10GB logical disk** from the volume group.


## Step 8: Verify LVM Structure

```bash
pvdisplay
vgdisplay
lvdisplay
```

Shows detailed information about physical volumes, volume groups, and logical volumes.


## Step 9: Format the Logical Volume

```bash
mkfs.ext4 /dev/PRINCE_VG/PRINCE_ROOT_LV
```

Formats the logical volume so Linux can store files on it.



## Step 10: Create Mount Directory

```bash
mkdir /mnt/prince_root_lv_mount
```

This directory will act as the **access point** for the logical volume.


## Step 11: Mount the Logical Volume

```bash
mount /dev/PRINCE_VG/PRINCE_ROOT_LV /mnt/prince_root_lv_mount
```

Now the storage is **active and usable**.

## Step 12: Verify Mount

```bash
lsblk
df -h
```

You will now see `/mnt/prince_root_lv_mount` listed.


## Step 13: Create Files Inside the Mounted Volume

```bash
cd /mnt/prince_root_lv_mount
mkdir devops
cd devops
vim hello_dostu.txt
```

You just stored data **inside the logical volume**.


## Step 14: Read the File

```bash
cat /mnt/prince_root_lv_mount/devops/hello_dostu.txt
```

File content is displayed correctly.


## Step 15: Unmount the Volume

```bash
umount /mnt/prince_root_lv_mount
```

Now the storage is **detached from the filesystem**.

## Step 16: Try to Access Data After Unmount

```bash
cat /mnt/prince_root_lv_mount/devops/hello_dostu.txt
```

X Output:

```
No such file or directory
```

This happens because the volume is **not mounted**, not because data is deleted.

## Step 17: Mount Again

```bash
mount /dev/PRINCE_VG/PRINCE_ROOT_LV /mnt/prince_root_lv_mount
```


## Step 18: Verify Data Is Still There

```bash
cat /mnt/prince_root_lv_mount/devops/hello_dostu.txt
```

 Same data appears again.


### Linux & DevOps Commands 

---

## `/dev/null`

Used to discard output when you don’t want to see it.

**Example**

```bash
ls /random 2>/dev/null
```

 Error output is discarded, nothing is shown.

**Real use**

```bash
command > /dev/null 2>&1
```

 Hides both output and errors (used in cron jobs).

---

## CronJob

Used to schedule tasks automatically.

**Example**

```bash
crontab -e
```

Add:

```bash
* * * * * echo "Hello DevOps" >> /tmp/cron.log
```

 Writes text every minute.

---

## systemctl & Docker Status

Used to manage services.

**Example**

```bash
sudo systemctl status docker
```

 Shows if Docker is running or stopped.

Other actions:

```bash
sudo systemctl start docker
sudo systemctl stop docker
sudo systemctl restart docker
```

---

## `ls /sbin`

Lists system-level admin commands.

**Example**

```bash
ls /sbin
```

 Shows binaries used mainly by root (reboot, fsck, etc.).

---

## `ls -ltr`

Lists files by time (oldest first).

**Example**

```bash
ls -ltr
```

 Useful to find which file was created first.

---

## useradd vs adduser (PRACTICAL)

### useradd (low-level)

```bash
sudo useradd prince
ls /home
```

 No home directory created.

### adduser (high-level)

```bash
sudo adduser prince
ls /home
```

 Home directory + password + defaults created.

**Interview line**

> `adduser` is interactive, `useradd` is manual.

---

## `/etc/passwd` & `/etc/shadow`

```bash
cat /etc/passwd | head
```

 Shows users (NOT passwords).

```bash
sudo cat /etc/shadow | head
```

 Shows encrypted passwords (root only).

**Interview**
 You cannot restore a password
 You can only reset it

---

## groupadd & usermod

```bash
sudo groupadd devops
sudo usermod -aG devops prince
```

 Adds user to devops group.

Check:

```bash
groups prince
```

---

## free (Memory)

```bash
free -m
free -h
```

 Memory usage in MB / human-readable.

---

## nice (Process Priority)

```bash
nice -n 10 sleep 100
```

 Starts process with lower priority.

---

## PID & ps -ef

```bash
ps -ef | grep ssh
```

 Shows running ssh processes + PID.

---

## kill vs kill -9

```bash
kill PID
```

 Graceful stop.

```bash
kill -9 PID
```

 Force kill (no cleanup).

**Interview**

> kill -9 should be last option.

---

## vmstat

Shows CPU, memory, IO stats.

```bash
vmstat 2
```

 Updates every 2 seconds.

---

## Networking (netstat / ss)

```bash
ss -tuln
```

 Shows listening ports.

---

## mkfs (Format Disk)

```bash
sudo mkfs -t ext4 /dev/xvdf
```

 Formats disk with ext4 filesystem.

 Data will be erased.

---

## CIDR (Networking)

```text
192.168.1.0/24
```

* `/24` → 256 IPs
* `/16` → 65,536 IPs









