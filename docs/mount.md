## Brief intro into filesystems and partitions

The term **filesystem** has two somewhat different meanings, both of which are commonly used. 

**_The first meaning_** is the entire hierarchy of directories (also referred to as the `directory tree`) that is used to organize files on a computer system. This hierarchy starts from a single directory called **root**, which is represented by a "/" (forward slash).

A variant of this definition is the part of the entire hierarchy of directories (directory tree) that is located on a single partition or disk.

(A **partition** is a slice of disk space that's devoted to a particular purpose.
One of the goals of dividing a disk into partitions is to achieve higher data security in case of disaster. By dividing the hard disk in partitions, data can be grouped and separated. When an accident occurs, only the data in the partition that got the hit will be damaged, while the data on the other partitions will most likely survive.)

The **_second meaning_** is the type of filesystem, that is, how the storage of data (i.e., files, folders, etc.) is organized on a computer disk (hard disk, floppy disk, CDROM, etc.) or on a partition on a hard disk. Each type of filesystem has its own set of rules for controlling the allocation of disk space to files and for associating data about each file (referred to as meta data) with that file, such as its filename, the directory in which it is located, its permissions and its creation date.

By every mention of the word `filesystem` below will be meant the first definition, i.e. an entire hirerachy of directories (directory tree).

### Mounting a filesystem

**Mounting a filesystem** simply means making a particular filesystem accessible at a certain point in the Linux directory tree.

By mounting a filesystem, you associate the root directory of that filesystem with an existing directory (called **mount point**) in the currently accessible filesystem.

Don't get confused by the name `mount point`, it just means a directory in the current filesystem to which another filesystem is mounted (logically attached).

The mount point becomes the root directory of the newly attached filesystem, and that filesystem becomes accessible in the current filesystem from that directory. Any original contents of that directory become invisible and inaccessible until the filesystem is unmounted (i.e., detached from the main filesystem).

The **default mount points** for a system are the directories in which filesystems will be automatically mounted unless told by the user to do otherwise. They are listed in **/etc/fstab**, which is a plain text configuration file that contains information about the major filesystems on a computer. The first column in /etc/fstab shows the device (i.e., the partition or disk), and the second column shows its default mount point.

For example, in /etc/fstab you can find that the root filesystem is mounted at boot time to the `/` directory.

### Mounting a filesystem example

For example, I've added a new disk to my system. Now I how do I make it accessible for storing files?

Before I can use a new disk, I need to first find out by which block device file the disk is represented on the system.

In Linux terminology, a disk is called a **block device**.  A block device is a piece of hardware that provides data access in blocks (contiguous groups of bytes) as opposed to character devices which provides access to individual data bytes. These devices support random access and generally use buffered I/O. In addition to disk, other commonly known block devices include CD-ROM drives, and flash drives.

As everything in Linux, a block device is represented by a file. These special files (called `block device files`) and are stored under the `/dev` directory.
You can recognize these files by the first letter **b** in the file's permissions string:

```bash
$ ls -l /dev/xvda1
brw-rw---- 1 root disk 202, 1 Mar  5 03:42 /dev/xvda1
```

There is also the **lsblk** command designed specifically to list available block devices.

```bash
$ lsblk
NAME    MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
xvda    202:0    0   8G  0 disk
`-xvda1 202:1    0   8G  0 part /
xvdf    202:80   0  10G  0 disk
```

The output of lsblk removes the `/dev` prefix from full device paths. In this example, `/dev/xvda1` which is a single partition on `/dev/xvda` disk  is mounted to the root of the Linux file system hierarchy (representd by `/`). There is also a disk represented by a block device file `/dev/xvdf` which has not been mounted yet. This is the new disk that I added.

Now that I've found the block device file path which represents my disk on the system, I need to make sure the disk has a filesystem. I cannot mount and use a disk that doesn't have a filesystem, the OS system will simply don't know how to manage the data on that disk (remember that the filesystem type determines how the data should be organized on that disk).

The `-f` option provided to `lsblk` command will provide me with information about the filesystems which block devices have.

```bash
$ lsblk -f
NAME    FSTYPE LABEL           UUID                                 MOUNTPOINT
xvda
`-xvda1 ext4   cloudimg-rootfs b6adc449-5e3d-4331-ba6b-6e99a75fa48e /
xvdf
```

From the output I can see that my new disk doesn't have a filesystem, so I have to create it. I can create a filesystem with a **mkfs** command:

```bash
$ sudo mkfs -t ext4 /dev/xvdf
```
Check again:

```
$ lsblk -f
NAME    FSTYPE LABEL           UUID                                 MOUNTPOINT
xvda                                                                
`-xvda1 ext4   cloudimg-rootfs b6adc449-5e3d-4331-ba6b-6e99a75fa48e /
xvdf    ext4                   7d9ca8e2-5c15-4622-b044-388fa0f59b6f 
```

Now I'm ready to mount my disk's filesystem and start using it, but first I will create a `mount point` (directory to which attach a new filesystem).

```
$ mkdir /home/ubuntu/mynewdisk
```

Finally, I'll mount the filesystem using the **mount** command which has the following syntax:
```
$ mount filesystem mountpoint
```

Here I'm referencing the filesystem by a block device on which this filesystem exists:

```bash
$ sudo mount /dev/xvdf /home/ubuntu/mynewdisk
```

Run `lsblk` again:

```bash
lsblk
NAME    MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
xvda    202:0    0   8G  0 disk 
`-xvda1 202:1    0   8G  0 part /
xvdf    202:80   0  10G  0 disk /home/ubuntu/mynewdisk
```

The filesystem on my disk was mounted and I have extra disk space available in my `/home/ubuntu/mynewdisk` directory.

If you want to check how much of this 10G used, you can run **df** (stands for disk filesystem) to get a summary of available and used disk space of file systems that you have.

```bash
$ df -h
Filesystem      Size  Used Avail Use% Mounted on
udev            488M     0  488M   0% /dev
tmpfs           100M  3.1M   97M   4% /run
/dev/xvda1      7.7G  846M  6.9G  11% /
tmpfs           496M     0  496M   0% /dev/shm
tmpfs           5.0M     0  5.0M   0% /run/lock
tmpfs           496M     0  496M   0% /sys/fs/cgroup
tmpfs           100M     0  100M   0% /run/user/1000
/dev/xvdf       9.8G   23M  9.2G   1% /home/ubuntu/mynewdisk
```

**Note:** if you reboot your system the filesystem will be unmounted. To make the filesystem mounting automatic on system boot, you need to add an entry to `/etc/fstab` file in the following format:

```
device_name  mount_point  file_system_type  fs_mntops  fs_freq  fs_passno
```

In my example, I will add the following line to end of `/etc/fstab` file to enable mounting of the filesystem on system boot:

```
UUID=7d9ca8e2-5c15-4622-b044-388fa0f59b6f       /home/ubuntu/mynewdisk   ext4    defaults,nofail        0       2
```

Here instead of using the block device name (`/dev/xvdf`), I use the device's 128-bit universally unique identifier (UUID) instead, which is recommended. System-declared block device names may change under a variety of circumstances, but the UUID is assigned to a disk when it is formatted and persists throughout the disk's service life. By using the UUID, you reduce the chances of the block-device mapping in /etc/fstab leaving the system unbootable after a hardware reconfiguration.

You can get the UUID of a disk or partition, by using `lsblk -f` command:

```bash
$ lsblk -f
NAME    FSTYPE LABEL           UUID                                 MOUNTPOINT
xvda                                                                
`-xvda1 ext4   cloudimg-rootfs b6adc449-5e3d-4331-ba6b-6e99a75fa48e /
xvdf    ext4                   7d9ca8e2-5c15-4622-b044-388fa0f59b6f 
```

To check that configuration of the `/etc/fstab` file is correct, I can run the following command which mounts all filesystems in your `/etc/fstab` file:

```
$ sudo mount -a
```

If the previous command does not produce an error, then your `/etc/fstab` file is OK and your file system will mount automatically at the next boot. If the command does produce any errors, examine the errors and try to correct your /etc/fstab. Errors in the /etc/fstab file can render a system unbootable. Do not shut down a system that has errors in the /etc/fstab file.


### Unmounting a filesystem

**umount** command allows to unmount a mounted filesystem. Run umount command with a block device name or mount point name to unmount a filesystem:

```bash
$ umount /dev/xvdf
# or
$ umount /home/ubuntu/mynewdisk
```

### List all mounts

`mount` command without any arguments will list all the mounts on the system.

### Bind mounts

Using **bind mounts**, we can mount all, or even part of an already-mounted filesystem to another location, and have the filesystem accessible from both mount points at the same time.

To create a bind mount, you mount one directory (which is already a mount point or part of a mount point for filesystem 'X') into another directory from which you want to have the same access to filesystem 'X'.
A bind mount operation simply takes an existing directory tree and replicates it under another directory. The directories and files in the bind mount directoy are the same as the original. Any modification on one side is immediately reflected on the other side, since the two views share the same data.

For example, I will bind mount a mount point `/home/ubuntu/mynewdisk` from example above into `/opt/bindmount` which is another directory in my current filesystem:

```bash
$ mkdir /tmp/bindmount
$ sudo mount -B /home/ubuntu/mynewdisk /tmp/bindmount
```

Now the filesystem on my disk `/dev/xvdf` is mounted into 2 different directories, i.e. `/home/ubuntu/mynewdisk` and `/tmp/bindmount`:

```bash
$ mount | grep xvdf
/dev/xvdf on /home/ubuntu/mynewdisk type ext4 (rw,relatime,data=ordered)
/dev/xvdf on /tmp/bindmount type ext4 (rw,relatime,data=ordered)
```

This means that I have the same directory view at those directories:

```bash
~$ sudo chown ubuntu:ubuntu /home/ubuntu/mynewdisk/
~$ cd /home/ubuntu/mynewdisk
~/mynewdisk$ echo "hello world" > file.txt
~/mynewdisk$ cd /tmp/bindmount/
/tmp/bindmount$ ls 
file.txt  lost+found
/tmp/bindmount$ cat file.txt 
hello world
/tmp/bindmount$ mkdir test_dir
/tmp/bindmount$ cd /home/ubuntu/mynewdisk/
~/mynewdisk$ ls 
file.txt  lost+found  test_dir
```

* [More detailed explanation of bind mounts](https://docs.1h.com/Bind_mounts)
* [A use case for using bind mounts](https://backdrift.org/how-to-use-bind-mounts-in-linux).

### Resources used to create this document:
* [Filesystems: A Brief Introduction](http://www.linfo.org/filesystem.html)
* [General overview of the Linux file system](https://www.tldp.org/LDP/intro-linux/html/sect_03_01.html)
* [Mount Point definition](http://www.linfo.org/mount_point.html)
* [Making an Amazon EBS Volume Available for Use](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-using-volumes.html)

