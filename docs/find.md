## find

The **find** command comes in handy when you need to search for files based on some search criteria. It can be used to find files based on permissions, user ownership, modification time, size etc.

The general syntax for the command:

```
find [search directory] [search criteria] [actions]
``` 

the things in `[]` are optional and we'll go over each one of them.

### Search directory

Without any arguments the `find` command will list all the files under the current directory. So if I run `find` in my user's home directory, I will get a list of all the files in my home directory:

```bash
$ find
.
./.bashrc
./test-dir
./test-dir/file2.txt
./test-dir/file1.txt
./.ssh
./.ssh/authorized_keys
./.profile
./.bash_logout
./.cache
./.cache/motd.legal-displayed
```

`find` does the **recursive search**, meaning it searches for files in all subdirectories of the search directory. You can see how it listed `file.txt` and `file2.txt` files under the `test-dir` directory which is a subdirectory in the current search directory.

--- 

Specify a path to a directory if you want to search for files under a specific directory and not the current one:

```bash
$ find ./test-dir
./test-dir
./test-dir/file2.txt
./test-dir/file1.txt
```

When specifying the directory (`./test-dir` in this example), its fine to omit the trailing slash. However, if the directory is actually a symlink to some other location then you must specify the trailing slash for it to work properly (i.e. `find ./test-dir/`).

--- 

You can also search for files in multiple directories:

```
$ find ./test-dir/ ./test-dir2/
./test-dir/
./test-dir/file2.txt
./test-dir/file1.txt
./test-dir2/
./test-dir2/index.html
```

---

To limit search to a certain depth of subdirectories, use the `-maxdepth` command option:

```bash
$ find ./test/
./test/
./test/file2.txt
./test/subdir
./test/subdir/file1.txt
$ find ./test/ -maxdepth 1
./test/
./test/file2.txt
./test/subdir
```

### Search criterias

Search criterias (also called **tests**) are used to define what files to look for. We'll go over the most common searches. For a full list of available options, see `man find`.

#### Find file by name

Search for files with `*.php` extension:

```bash
$ find ./test-dir/
test-dir/
test-dir/file3.php
test-dir/file2.txt
test-dir/file4.php
test-dir/file1.txt
$ find ./test-dir/ -name '*.php'
./test-dir/file3.php
./test-dir/file4.php
$ find ./test-dir/ -iname '*.PHP' # case insensitive search
./test-dir/file3.php
./test-dir/file4.php
```

---

Search for files whose name starts with `file`:

```bash
$ find ./test-dir/ -name 'file*'
./test-dir/file3.php
./test-dir/file2.txt
./test-dir/file4.php
./test-dir/file1.txt
```

#### Invert the search criteria

Use `-not` or `!` before the search criteria to invert it. For example, to find all files that don't have `.php` extension, we could use any of these two commands:

```bash
$ find ./test-dir/ -not -name '*.php'
./test-dir/
./test-dir/file2.txt
./test-dir/file1.txt
$ find ./test-dir/ ! -name '*.php'
./test-dir/
./test-dir/file2.txt
./test-dir/file1.txt
``` 

#### Combine search criterias

It is possible to compine multiple search criterias with an `AND` operator. For example, to find all files whose name starts with `file` and which don't have `.php` extension, we could use the following command:

```bash
$ find ./test-dir/ -name 'file*' ! -name '*.php'
./test-dir/file2.txt
./test-dir/file1.txt
```

To combine the search criteria with an `OR` operator, use the `-o` option. For example, to find files that with `.txt` or `.php` file extension, we could use the following command:

```bash
$ find ./test-dir/ -name '*.php' -o -name '*.txt'
./test-dir/file3.php
./test-dir/file2.txt
./test-dir/file4.php
./test-dir/file1.txt
```

#### Find only files or only directories

Did you notice when we we were searching for files with extension other than `.php`  in `test-dir` directory, in the resulting list of files we also get the `test-dir` folder itself which also matches the search criteria:

```bash
$ find ./test-dir/ ! -name '*.php'
./test-dir/
./test-dir/file2.txt
./test-dir/file1.txt
``` 

But what if you're only interested in files and want to exlude the directories from you list of results. You could do that by specifying the type of files you're looking for with the `-type` option.

```bash
$ find ./test-dir/ ! -name '*.php' -type f # search for regular files
./test-dir/file2.txt
./test-dir/file1.txt
$ find ./test-dir/ ! -name '*.php' -type d # search for directories
./test-dir/
```

#### Find files with certain permissions

Use the `-perm` options. For example, to find all files with permissions set to `0644` in my home directory, I would use the following command:

```bash
$ find ~ -type f -perm 0644
/home/vagrant/.bashrc
/home/vagrant/.profile
/home/vagrant/.bash_logout
/home/vagrant/.cache/motd.legal-displayed
```


#### Find files by owner or group

```bash
$ ls -l test-dir
total 0
-rw-rw-r-- 1 vagrant vagrant 0 Mar 26 01:00 file1.txt
-rw-rw-r-- 1 vagrant vagrant 0 Mar 26 01:00 file2.txt
-rw-rw-r-- 1 root    vagrant 0 Mar 26 01:31 file3.php
-rw-rw-r-- 1 root    vagrant 0 Mar 26 01:31 file4.php
$ find ./test-dir/ -type f -user root # find files owned by root
./test-dir/file3.php
./test-dir/file4.php
$ find ./test-dir/ -type f -group vagrant # find files that belong to vagrant group
./test-dir/file3.php
./test-dir/file2.txt
./test-dir/file4.php
./test-dir/file1.txt
```

#### Find files by timestamp

In Linux in each file has 3 timestamps: access time (`atime`), modification time (`mtime`) and change time (`ctime`). You can read more on this [here](./file-timestamps).

Use `-mtime`, `-ctime`, and `-atime` options to base your search on these timestamps values. For example, to find files in the current working directory which were last modified 10 days ago, use the following command:

```bash
$ find . -type f -mtime 10 # modified exactly 10 days ago
$ find . -type f -mtime +10 # modified more than 10 days ago
$ find . -type f -mtime -10 # modified less than 10 days ago
```

---

Find files that were modified between 50 and 100 days ago:

```bash
$ find / -mtime +50 –mtime -100
``` 

---

Search for files in your home directory which were modified in the last twenty-four hours: 

```bash
$ find ~ -mtime 0
```

---

Find files that were modified in the last hour:

```bash
$ find / -mmin -60
```

#### Find files of a given size

To find all the files which are greater than 50MB and less than 100MB:

```bash
$ find / -size +50M -size -100M
```

#### Find empty files and directories

```
$ find /tmp -type f -empty
$ find ~ -type d -empty
```

### Actions

You can execute a command on a each file from the returned list using `-exec` option. For example, to long list each of the returned files, we would use the following command:

```bash
$ find ./test-dir/ -name 'file*' -exec ls -ld '{}' \;
-rw-rw-r-- 1 root vagrant 0 Mar 26 01:31 ./test-dir/file3.php
-rw-rw-r-- 1 vagrant vagrant 0 Mar 26 01:00 ./test-dir/file2.txt
-rw-rw-r-- 1 root vagrant 0 Mar 26 01:31 ./test-dir/file4.php
-rw-rw-r-- 1 vagrant vagrant 12 Mar 26 02:50 ./test-dir/file1.txt
```

Here `{}` is a placeholder for each file that the find command locates and each command mush also end with `\;`.

---

Remove files which were last modified more than 10 minutes ago:

```bash
$ touch ./test-dir/newfile.txt # create a new file
$ ls -l ./test-dir
total 4
-rw-rw-r-- 1 vagrant vagrant 12 Mar 26 02:50 file1.txt
-rw-rw-r-- 1 vagrant vagrant  0 Mar 26 01:00 file2.txt
-rw-rw-r-- 1 root    vagrant  0 Mar 26 01:31 file3.php
-rw-rw-r-- 1 root    vagrant  0 Mar 26 01:31 file4.php
-rw-rw-r-- 1 vagrant vagrant  0 Mar 26 03:28 newfile.txt
$ date
Mon Mar 26 03:29:11 UTC 2018
$ find ./test-dir/ -type f -mmin +10 -exec rm {} \;
$ ls -l ./test-dir
total 0
-rw-rw-r-- 1 vagrant vagrant 0 Mar 26 03:28 newfile.txt
```


### Resources used to create this document

* https://www.binarytides.com/linux-find-command-examples/
* https://kb.iu.edu/d/admm
