## How files are stored in the filesystem?

In Linux everything is a file. This includes not only text files, images and executables, but also directories, partitions and hardware device drivers.

The **filesystem** defines how all of these files of data are stored on a physical drive (disk or partition). The filesystem uses special data structures to organize and store files on a physical drive:
* inodes
* data blocks

The **data blocks** store the _contents of the file_. 

An **inode** stores _metadata about the file_. It stores the following information about the file:

* file permissions
* owner and group associated with the file
* file type (whether it is a regualar file, directory, link, etc.)
* [timestamps](file-timestamps.md) (access time, modification name, change time)
* size of the file
* data block pointers (i.e., the location on the disk where the file contents is stored)

The inodes are identified by a unique **inode number** (each filesystem has its own set of inodes, so these numbers are unique within a filesystem). The inode numbers and their corresponding inodes are held in an **inode table**, which is stored in a separate from data blocks part of a disk or partition.

Thus, when a new file is created, it gets assigned an inode and a new entry is created inside the inode table. The files contents get stored on the disk using data blocks.

Where is the file's name stored then?

The file name is stored in a directory. **Directory** is a just another type of file in a filesystem and it holds only the names of the files that appear to the user to be contained in it and their corresponding inode numbers.

As you can see, there is a substantial difference between the way the user sees the Linux filesystem  and the way the kernel (the core of a Linux system) actually stores the files. To the user, the filesystem appears as a hierarchical arrangement of directories that contain files and other directories (i.e., subdirectories). Directories and files are identified by their names.

_To the Linux kernel, however, the filesystem is flat_. That is, it does not:

* have a hierarchical structure
* differentiate between directories, files or programs
* identify files by names

Instead, the kernel uses inodes to represent each file and keeps this information in one table (inodes table).

This detaching of a file's name from its other metadata is what allows the system to implement hard links and thus have multiple names for any file. A hard link is an entry in a directory that contains a pointer directly to the inode bearing the file's metadata. When a new hard link to a file is created, both links share the same inode number because the link is only a pointer, not a copy of the file.

Within any filesystem, the maximum number of inodes, and hence the maximum number of files, is set when the filesystem is created. You can't increase that number after the filesyste is created.

## Working with inodes

Find out file's inode:

```bash
$ ls -i file.txt
262803 file.txt
$ ls -id ./examples # for directory
262812 ./examples
```

---

Find a file by inode:

```bash
$ ls -i file.txt
262803 file.txt
$ find . -inum 262803 # find file in the current directory by inode
./file.txt
$ cat file.txt
hello world
$ cat `find . -inum 262803` # open a file by inode
hello world
$ find . -inum 262803 -delete # remove a file by inode
$ ls file.txt
ls: cannot access file.txt: No such file or directory
```

---

Check inode utilitization:

```bash
$ df -hi
Filesystem     Inodes IUsed IFree IUse% Mounted on
udev              61K   404   60K    1% /dev
tmpfs             62K   325   61K    1% /run
/dev/sda1        2.5M   72K  2.5M    3% /
none              62K     2   62K    1% /sys/fs/cgroup
none              62K     1   62K    1% /run/lock
none              62K     1   62K    1% /run/shm
none              62K     2   62K    1% /run/user
none             1000     0  1000    0% /vagrant
```

### Resources used to create this document:
* http://www.linfo.org/filesystem.html
* http://www.linfo.org/inode.html
* http://www.grymoire.com/Unix/Inodes.html
