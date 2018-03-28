## du (disk usage)

The **du** (disk usage) command is used for checking the sizes of files and directories (in other words, how much disk space they use).

### Check disk usage in the directory

Without any arguments the `du` command will _recursively_ list the space that each subdirectory in the current working directory and the current directory itself. Use the **-h** option to make the `du` output **human-readable**, that is to display it in kilobytes (`K`), megabytes (`M)` and gigabytes (`G`) rather than  in the default kilobytes.:

```bash
$ find . -type d # list directories in the current directory
.
./test-dir
./test-dir/subdir1
./test-dir/subdir1/subdir2
./.ssh
./.cache
$ du -h # list sizes of directories in the current directory
25M	./test-dir/subdir1/subdir2
25M	./test-dir/subdir1
49M	./test-dir
8.0K	./.ssh
4.0K	./.cache
49M
```

Note that both command `find` and `du` also list the current working directory itself indicated by dot `.`.

---

By default, `du` command recursively lists all subdirectories, to restrics how deep in directory tree the `du` command should go, you can use the **-d** (**--max-depth**) option:

```bash
$ du -hd 1 # display sizes of directoris only in the current directory
49M	./test-dir
8.0K	./.ssh
4.0K	./.cache
49M	.
$ du -hd 2 # go 1 level deeper than the current directory
25M	./test-dir/subdir1
49M	./test-dir
8.0K	./.ssh
4.0K	./.cache
49M	.
$ du -hd 3 # go 2 levels deeper than the current directory
du -hd 3
25M	./test-dir/subdir1/subdir2
25M	./test-dir/subdir1
49M	./test-dir
8.0K	./.ssh
4.0K	./.cache
49M	.
```

---

To list directory sizes in some specific directory, provide the path to that directory as an argument to the `du` command:

```bash
$ du -h ./test-dir/
25M	./test-dir/subdir1/subdir2
25M	./test-dir/subdir1
49M	./test-dir/
```

### Get total size of a directory

The `du -h` command reports the total size of a directory at the end, but it also list the sizes of all subdirectories it contains. If you're only interested in the total size of a directory, use the `-s` option to only get the total size:

```bash
$ du -hs ./test-dir
49M	./test-dir
```

### Check size of files

The **-a** (**--all**) option tells `du` to report not just the total disk usage for each directory at every level in a directory tree but also to report the size for each individual file:

```bash
$ du -h ./test-dir/ # lists only directories
25M	./test-dir/subdir1/subdir2
25M	./test-dir/subdir1
49M	./test-dir/
$ du -ha ./test-dir/ # also lists files
24M	./test-dir/file2.txt
4.0K	./test-dir/file1.txt
24M	./test-dir/subdir1/subdir2/blob.txt
25M	./test-dir/subdir1/subdir2
25M	./test-dir/subdir1
49M	./test-dir/
```

---

Show only files that are larger than a specific value by setting the size filter with the **-t** option:

```
# du -hat 100M  /
131M	/var/lib
194M	/var
160M	/usr/lib/x86_64-linux-gnu
114M	/usr/lib/juju-1.25.6/bin/jujud
109M	/usr/lib/juju-1.25.6/bin/juju
380M	/usr/lib/juju-1.25.6/bin
380M	/usr/lib/juju-1.25.6
738M	/usr/lib
201M	/usr/share
113M	/usr/src
1.2G	/usr
```

---

Include the last modification time information in the output using the **--time** option:

```
$ du -ha --time ./test-dir/
24M	2018-03-28 05:20	./test-dir/file2.txt
4.0K	2018-03-28 07:16	./test-dir/.test
4.0K	2018-03-28 05:19	./test-dir/file1.txt
24M	2018-03-28 05:41	./test-dir/subdir1/subdir2/blob.txt
25M	2018-03-28 05:41	./test-dir/subdir1/subdir2
25M	2018-03-28 05:41	./test-dir/subdir1
49M	2018-03-28 07:16	./test-dir/
```

### Star wildcard

Using the star **star wildcard** (which matches any characters) in directory path we can achieve a result similar to the `-a` option:

```bash
$ du -h ./test-dir # lists all subdirectories
25M	./test-dir/subdir1/subdir2
25M	./test-dir/subdir1
49M	./test-dir
$ du -ha ./test-dir # lists all files and subdirectories
24M	./test-dir/file2.txt
4.0K	./test-dir/.test
4.0K	./test-dir/file1.txt
24M	./test-dir/subdir1/subdir2/blob.txt
25M	./test-dir/subdir1/subdir2
25M	./test-dir/subdir1
49M	./test-dir
$ du -h  ./test-dir/* # lists subdirectories and files in the current directory only
4.0K	./test-dir/file1.txt
24M	./test-dir/file2.txt
25M	./test-dir/subdir1/subdir2
25M	./test-dir/subdir1
```

Note, the only files listed are those in the the parent directory, not those in its subdirectories. This also doesn't list subdirectories that start with a dot `.` and no total for the directory tree is provided as a whole.

---

The wildcard can also be used to filter the output to list only those items whose names begin with, contain or end with certain characters. For example, the following would report the names and sizes of all of the directories and files in the `test-dir` directory whose names begin with the word `file`:

```bash
$ du -h ./test-dir/file*
4.0K	./test-dir/file1.txt
24M	./test-dir/file2.txt
```

To provide total size for the files and directories use the **-c**  option:

```bash
$ du -hc ./test-dir/file*
4.0K	./test-dir/file1.txt
24M	./test-dir/file2.txt
25M	total
```

---

As another example of the use of the wildcard, the following command would report the name and size of each `.txt`  file in the current directory as well as a total for all of the `.txt` files:

```bash
$ du -hc ./test-dir/*.txt
4.0K	./test-dir/file1.txt
24M	./test-dir/file2.txt
25M	total
```


### Filtering the output

Sort output items by size by piping the output to the `sort` command:

```bash
$ du -h
25M	./test-dir/subdir1/subdir2
25M	./test-dir/subdir1
49M	./test-dir
8.0K	./.ssh
4.0K	./.cache
49M	.
$ du -h | sort -n
4.0K	./.cache
8.0K	./.ssh
25M	./test-dir/subdir1
25M	./test-dir/subdir1/subdir2
49M	.
49M	./test-dir
```

---

As `du` will often generate more output than can fit on the monitor screen at one time, the output will fly by at high speed and be virtually unreadable, in this case it makes sense to pipe the output to the less command:

```bash
$ du -h | less
```

---

The grep filter can be used to search through du's output for any desired string. For example, the following will provide a list of the names and sizes of directories and files in the current directory that contain the word `file`:
```
$ du -ah | grep file
24M	./test-dir/file2.txt
4.0K	./test-dir/file1.txt
4.0K	./.profile
```

### Resources used to create this document:

* https://www.rootusers.com/13-du-disk-usage-command-examples-in-linux/
* https://www.tecmint.com/check-linux-disk-usage-of-files-and-directories/
* http://www.linfo.org/du.html

