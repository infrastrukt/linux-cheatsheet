## Links

Links in Linux allow you to provide access to files and directories from multiple locations in the filesystem directory tree.

There are two types links: **hard** and **soft**. They both provide multiple references to a single file, but they do it quite differently.

### Hard links

When a new file is created on the filesystem, it gets assigned an [inode](inode.md). The inode is a data structure that describes attributes of the files (permissions, owner, size) and points to the location of the file's content on the disk. The inode doesn't hold the file's name. The file's name holds the directory file (remember that everything in Linux is a file, even a directory). The directory file holds the mapping of file names (which to the user seem to be contained in this directory) to inode numbers (identifiers for inodes):

```bash
# the mapping of file names to inode numbers
$ ls -1 -i
141360 col.sh
141355 file.txt
262175 zabbixapi
```

The **directory entry** is a single entry in the directory file such as `141360 col.sh` or `262175 zabbixapi` which map a file name to inode number.

The directory entry is a **hard link**. It's a mapping of a file's name to an inode number which holds all the information about the file except its name. Thus, every file has at least one hard link.

The fact that the inode holds all the information about the file, but doesn't holds its name, makes it possible to create multiple names which will point to the same file.

Let's create a hard link for the file named `file.txt`:

```bash
$ ln file.txt file2.txt
```

And check the directory file content:

```bash
$ ls -1 -i
141360 col.sh
141355 file2.txt
141355 file.txt
262175 zabbixapi
```

We can see that a new directory entry was created which maps the file name `file2.txt` to the same inode number as the `file.txt` file name. Because all the file information is contained in the inode, `file2.txt` and `file.txt` are entries to the same file:

```bash
# their content is the same
$ cat file.txt
hello world
$ cat file2.txt
hello world
```

The file sizes and all the attributes are the same, because they point to the same inode that holds all this information:

```bash
$ ls -lh
total 16K
-rw-rw-r-- 1 vagrant vagrant  289 Apr  1 06:14 col.sh
-rw-rw-r-- 2 vagrant vagrant   12 Apr  8 01:48 file2.txt
-rw-rw-r-- 2 vagrant vagrant   12 Apr  8 01:48 file.txt
drwxrwxr-x 6 vagrant vagrant 4.0K Apr  2 03:54 zabbixapi
```  

If we change the file using one hard link, we can see this changes using another hard link:

```bash
$ echo "learning hard links is cool" >> file2.txt
$ cat file.txt
hello world
learning hard links is cool
```

Note, that when you make a long listing of a directory, the second column shows you the number of hard links for this file (in other words the number of pointers to the same inode):

```bash
$ ls -lih
total 16K
141360 -rw-rw-r-- 1 vagrant vagrant  289 Apr  1 06:14 col.sh
141355 -rw-rw-r-- 2 vagrant vagrant   40 Apr  8 01:48 file2.txt
141355 -rw-rw-r-- 2 vagrant vagrant   40 Apr  8 01:48 file.txt
262175 drwxrwxr-x 6 vagrant vagrant 4.0K Apr  2 03:54 zabbixapi
```

Here we see that there are `2` hard links or `2` pointers to the same inode identified by inode number `141355`. If I create another hard link for the same file, this number will increment:

```bash
$ ln file2.txt file3.txt
$ ls -lih
total 20K
141360 -rw-rw-r-- 1 vagrant vagrant  289 Apr  1 06:14 col.sh
141355 -rw-rw-r-- 3 vagrant vagrant   40 Apr  8 01:48 file2.txt
141355 -rw-rw-r-- 3 vagrant vagrant   40 Apr  8 01:48 file3.txt
141355 -rw-rw-r-- 3 vagrant vagrant   40 Apr  8 01:48 file.txt
262175 drwxrwxr-x 6 vagrant vagrant 4.0K Apr  2 03:54 zabbixapi
```

Use the `find` command to locate all the hard links for the file:

```bash
$ ln file.txt zabbixapi/file4.txt # create a hard link in another directory
$ find . -samefile file.txt # find all the hard links under the current directory, using one of the hard links names
./file2.txt
./zabbixapi/file4.txt
./file3.txt
./file.txt
$ find . -inum 141355 # the same search but using inode number
./file2.txt
./zabbixapi/file4.txt
./file3.txt
./file.txt
```

### Soft links

The difference between a hard link and a **soft link**, also known as a **symbolic link** (or **symlink**), is that, while hard links point directly to the inode belonging to the file, soft links point to a directory entry, i.e., one of the hard links.

Let's create a symlink named `sfile.txt` which points to the file `file.txt`:

```
$ ln -s file.txt sfile.txt
$ ls -lih
total 20K
141360 -rw-rw-r-- 1 vagrant vagrant  289 Apr  1 06:14 col.sh
141355 -rw-rw-r-- 4 vagrant vagrant   40 Apr  8 01:48 file2.txt
141355 -rw-rw-r-- 4 vagrant vagrant   40 Apr  8 01:48 file3.txt
141355 -rw-rw-r-- 4 vagrant vagrant   40 Apr  8 01:48 file.txt
141353 lrwxrwxrwx 1 vagrant vagrant    8 Apr  8 06:28 sfile.txt -> file.txt
262175 drwxrwxr-x 6 vagrant vagrant 4.0K Apr  8 01:58 zabbixapi
```

Notice, that the inode number for the file `sfile.txt` is different than the inode number for `file.txt` file which indicates that those files are not actually the same file. The directory entry for `sfile.txt` file does not point to an inode, it points to another directory entry (`141355 file.txt` in this case).

Also note that the file mode information for the symlink starts with the letter `l` which indicates that this file is a symbolic link file.

The last column which shows the name of the file in this case shows the file name and the file name of the file it points to.

The size of the `sfile.txt` is also much maller and is only 8 bytes. That is the size of the text `sfile.txt -> file.txt`, which is the actual content of the directory entry.

Because the symlink points to a directory entry, basically referencing the file's name, if the that file name's changes or the location of this file changes, the link gets broken:

```bash
$ cat sfile.txt
hello world
learning hard links is cool
$ mv file.txt FILE.txt # rename the file
# the symlink is broken
$ cat sfile.txt
cat: sfile.txt: No such file or directory
```

### Other notes on differences 

Hard links don't work across different filesystems because the inode numbers are only unique within a filesystem. Because soft links point to a hard link for the file and not the inode, they are not dependent upon the inode number and can work across filesystems.

You cannot create a hard link for a directory to avoid recursive loop, where as you can create a soft link which points to a directory.


### Resources used to create this document

* [Great explanation of links with examples](https://opensource.com/article/17/6/linking-linux-filesystem). Most of the material have been taken from this post.
* https://www.geeksforgeeks.org/soft-hard-links-unixlinux/
 