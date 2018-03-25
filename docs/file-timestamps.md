## File Timestamps

Unix stores three **timestamps** for each file:

* `atime` - access time. It shows **the last time the file has been read**. Whenever you or some running process reads from a file its `atime` timestamp gets updated. Note that `atime` doesn't get updated when the file's contents or attributes are changed.
* `mtime` - modification time. It shows **the last time the contents of a file has been changed**. This means that whenever you or some process updates the contents of the file (write to it), its `mtime` timestamp gets updated. Note, that `mtime` gets updated only when the contents of a file is changed, it doesn't get updated when attributes of a file (e.g. file permissions, owner) are changed.
* `ctime` - change time. It shows **the last time the file (contents, attributes) has been changed**. The difference between `mtime` is that the `ctime` timestamp gets updated in case of any file changes including file attributes.

### Example

Let's create a new file called `test.txt` and check its timestamps:

```bash
$ echo "hello world" > test.txt
$ stat --printf '%n\nmtime: %y\nctime: %z\natime: %x\n' test.txt
test.txt
mtime: 2018-03-25 06:23:32.865110285 +0000
ctime: 2018-03-25 06:23:32.865110285 +0000
atime: 2018-03-25 06:23:32.865110285 +0000
```

When a new file is created all the timestamps are set to the same value.

---

Let's read the file's contents and see how it affects the timestamps:

```bash
$ cat test.txt
hello world
$ stat --printf '%n\nmtime: %y\nctime: %z\natime: %x\n' test.txt
test.txt
mtime: 2018-03-25 06:23:32.865110285 +0000
ctime: 2018-03-25 06:23:32.865110285 +0000
atime: 2018-03-25 06:25:31.821110758 +0000
```

Since we read the file, `atime` timestamp was updated. The `ctime` and `mtime` didn't change because we haven't changed anything about the file.

---

Let's write to a file and see what timestamps will change:

```bash
$ echo "unix rocks" >> test.txt
$ stat --printf '%n\nmtime: %y\nctime: %z\natime: %x\n' test.txt
test.txt
mtime: 2018-03-25 06:29:00.177111587 +0000
ctime: 2018-03-25 06:29:00.177111587 +0000
atime: 2018-03-25 06:25:31.821110758 +0000
```

Since the file contents has been changed, the `mtime` timestamp was updated as well as `ctime` (which tracks all file changes including its contents).

---

Let's update files attributes. For example, let's change file permissions:

```bash
$ chmod 755 test.txt
$ stat --printf '%n\nmtime: %y\nctime: %z\natime: %x\n' test.txt
test.txt
mtime: 2018-03-25 06:29:00.177111587 +0000
ctime: 2018-03-25 06:42:44.093114865 +0000
atime: 2018-03-25 06:25:31.821110758 +0000
```

The `ctime` attribute was updated since the file was changed. `mtime`  didn't change since we didn't the file's contents didn't change, and `atime` didn't changed since we didn't read from the file.

### Commands to check file timestamps

**stat** command includes a lot of information about the file including its timestampes:

```bash
$ stat test.txt
  File: ‘test.txt’
  Size: 23        	Blocks: 8          IO Block: 4096   regular file
Device: 801h/2049d	Inode: 131082      Links: 1
Access: (0755/-rwxr-xr-x)  Uid: ( 1000/ vagrant)   Gid: ( 1000/ vagrant)
Access: 2018-03-25 06:25:31.821110758 +0000
Modify: 2018-03-25 06:29:00.177111587 +0000
Change: 2018-03-25 06:42:44.093114865 +0000
```


___

**ls** command:

```bash
$ ls -l test.txt # check mtime
-rwxr-xr-x 1 vagrant vagrant 23 Mar 25 06:29 test.txt
$ ls -lc test.txt # check ctime
-rwxr-xr-x 1 vagrant vagrant 23 Mar 25 06:42 test.txt
$ ls -lu test.txt # check atime
-rwxr-xr-x 1 vagrant vagrant 23 Mar 25 06:25 test.txt
```

The useful option to `ls` command is `t` which allows you to sort files in a directory based on their timestamp. For example, to sort the output of `ls` command based on the files modification time (`mtime`), you can use the following command:

```bash
$ ls -l
total 4
-rw-rw-r-- 1 vagrant vagrant  0 Mar 25 06:56 file1.txt
-rw-rw-r-- 1 vagrant vagrant  0 Mar 25 06:57 file2.txt
-rwxr-xr-x 1 vagrant vagrant 23 Mar 25 06:29 test.txt
$ ls -lt
total 4
-rw-rw-r-- 1 vagrant vagrant  0 Mar 25 06:57 file2.txt
-rw-rw-r-- 1 vagrant vagrant  0 Mar 25 06:56 file1.txt
-rwxr-xr-x 1 vagrant vagrant 23 Mar 25 06:29 test.txt
```

The newest file will come first.

### Resources used to create this document
* https://www.quora.com/What-is-the-difference-between-mtime-atime-and-ctime
* https://www.unixtutorial.org/2008/04/atime-ctime-mtime-in-unix-filesystems/
