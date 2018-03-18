## /proc filesystem

The Linux kernel has two primary functions: to control access to physical devices on the computer and to schedule when and how processes interact with these devices. The **/proc** directory contains a hierarchy of special files which represent the current state of the kernel allowing applications and users to peer into the kernel's view of the system.

Browsing files under the `/proc` directory you can find information about the system hardware and the currently running processes. You're also able to modify some of the files within the `/proc` directory to make configuration changes to the kernel.

**/proc** is an example of a **virtual file system** (also referred to as a **process information pseudo-file system** because it constains information about all currently running processes). Instead of text and binary files that you used to see, it contains another type of files called **virtual files**. 

These virtual files have unique qualities. Most of them are listed as zero bytes in size and yet when one is viewed, it can contain a large amount of information. They are stored in RAM and not on disk. In addition, most of the time and date settings on virtual files reflect the current time and date, indicative of the fact they are constantly updated to provide up-to-the-moment information about the state of the system.

Virtual files such as `/proc/interrupts`, `/proc/meminfo`, `/proc/mounts`, and `/proc/partitions` provide a view of the system’s hardware. Others, such as `/proc/filesystems` and the files under `/proc/sys` provide information about the system's configuration and interfaces to the kernel, meaning you can change these files to communicate changes to the kernel settings.

Files that contain information about related topics are grouped into **virtual directories**. For example, a separate directory exists in `/proc` for each process that is currently running on the system, and the directory's name corresponds to the numeric process ID. For example, `/proc/1` corresponds to the `init` process, which has a PID of 1.

[See more information on what each file and directory mean in the /proc](https://www.tldp.org/LDP/Linux-Filesystem-Hierarchy/html/proc.html)


### Viewing files in /proc

By using the `cat`, `more`, or `less` commands on files within the `/proc` directory, users can immediately access enormous amounts of information about the system. For example, to view information about system's CPU:


```bash
$ cat /proc/cpuinfo
processor         : 0
vendor_id         : GenuineIntel
cpu family        : 6
model             : 42
model name        : Intel(R) Core(TM) i5-2520M CPU @ 2.50GHz
stepping          : 7
cpu MHz           : 2393.714
cache size        : 6144 KB
physical id       : 0
siblings          : 2
core id           : 0
cpu cores         : 2
apicid            : 0
initial apicid    : 0
fpu               : yes
fpu_exception     : yes
cpuid level       : 5
wp                : yes
...
```

Certain files under `/proc` require `root` privileges for access or contain information that is not human-readable. This is why many utilities exist to pull data from virtual files and display it in a useful way. Examples of these utilities include `lspci`, `free`, `top`, `ps`, etc.


### Changing files in /proc

As a general rule, most virtual files within the `/proc` directory are read-only. However, certain files inside `/proc` can be modified to change the behavior of a running kernel. This is especially true for files in the `/proc/sys` subdirectory.

For example, to change the hostname on the fly:

```
echo www.example.com > /proc/sys/kernel/hostname 
```

To immediately turn on forwarding of network packets:

```
echo 1 > /proc/sys/net/ipv4/ip_forward
```

There is also a sysctl command which is used to change files in `/proc/sys` and adjust kernel's configuration.

### Resources used to create this document:
* https://www.centos.org/docs/5/html/Deployment_Guide-en-US/ch-proc.html
* https://docs.oracle.com/cd/E37670_01/E41138/html/ch05s02.html
