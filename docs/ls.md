## ls

The **ls** command is used to list directory contents and find out a lot of useful information about the files and directories such as size, permissions, timestamps, owner, etc.

### Examples

#### Get a list of directory files

Without any arguments `ls` will list files and directories in the current directory:

```bash
$ ls
examples  Gemfile  Gemfile.lock  lib  LICENSE.md  Rakefile  spec
```

---

To get a list of files in some specific directory, provide a path to it as an argument to the `ls` command:

```bash
$ ls ./lib/  # using relative path
zabbixapi  zabbixapi.rb
$ ls /home/vagrant/zabbixapi/lib # using full path
zabbixapi  zabbixapi.rb
```

You can also list files in multiple directories with one command:

```bash
$ ls lib/ spec/
lib/:
zabbixapi  zabbixapi.rb

spec/:
valuemap.rb
```

---

You can **-1** option to list one file per line:

```bash
$ ls -1
examples
Gemfile
Gemfile.lock
lib
LICENSE.md
Rakefile
spec
```

or **-m** option to get a list of files in one line separated by commas:

```bash
$ ls -m
examples, Gemfile, Gemfile.lock, lib, LICENSE.md, Rakefile, spec
```

---

To include **hidden files** (files which names starts with a dot) in the listing, use the **-A** or **-a** options:

```bash
$ ls
examples  Gemfile  Gemfile.lock  lib  LICENSE.md  Rakefile  spec
$ ls -A
examples  Gemfile.lock  .gitignore  LICENSE.md  spec
Gemfile   .git          lib         Rakefile    .travis.yml
$ ls -a
.   examples  Gemfile.lock  .gitignore  LICENSE.md  spec
..  Gemfile   .git          lib         Rakefile    .travis.yml
```

As you can see the only difference between these options is that **-a** will also list the current (indicated by `.`) and parent directories (indicated by `..`).

---

To also get a list of files in all subdirectories, use the **-R** option:

```bash
$ ls -R
.:
examples  Gemfile  Gemfile.lock  lib  LICENSE.md  Rakefile  spec

./examples:
Actions.md  Applications.md  Configurations.md  Graphs.md

./lib:
zabbixapi  zabbixapi.rb

./lib/zabbixapi:
client.rb  version.rb

./spec:
valuemap.rb
```

As you can see, this recursively lists files in all subdirectories.

---

If you only want to list files in the top level subdirectories, you can use the following wildcard

```bash
$ ls *
Gemfile  Gemfile.lock  LICENSE.md  Rakefile

examples:
Actions.md  Applications.md  Configurations.md  Graphs.md

lib:
zabbixapi  zabbixapi.rb

spec:
valuemap.rb
```

If you compare it to the command with **-R** options, you'll find that this command doesn't list files in `lib/zabbixapi` directory.

---

To get only a list of subdirectories without listing the files, you can use the following command:

```bash
$ ls -d */ # using relative path
examples/  lib/  spec/
$ ls -d /home/vagrant/zabbixapi/*/ # using the full path
/home/vagrant/zabbixapi/examples/  /home/vagrant/zabbixapi/spec/
/home/vagrant/zabbixapi/lib/
```

#### Get information about files and directories

Use the **-l** option to display more information about the files than just their names:

```bash
ls -l
total 28
drwxrwxr-x 2 vagrant vagrant 4096 Apr  1 03:52 examples
lrwxrwxrwx 1 vagrant vagrant   14 Apr  1 04:14 file1.txt -> /tmp/file1.txt
-rw-rw-r-- 1 vagrant vagrant  342 Apr  1 02:08 Gemfile
-rw-rw-r-- 1 vagrant vagrant 2422 Apr  1 02:08 Gemfile.lock
drwxrwxr-x 3 vagrant vagrant 4096 Apr  1 02:08 lib
-rw-rw-r-- 1 vagrant vagrant 1082 Apr  1 02:08 LICENSE.md
-rw-rw-r-- 1 vagrant vagrant  526 Apr  1 02:08 Rakefile
-rwxrwxr-x 1 vagrant vagrant    0 Apr  1 04:11 script.sh
drwxrwxr-x 2 vagrant vagrant 4096 Apr  1 02:37 spec
```

Output field explanation:

1. The `first field`. The first character shows the type of the file. Most of the times, it will one of following symbols:
    * `-` - regular file
    * `d` - directory
    * `l` - link

    The following 9 characters show the file permissions. The first three characters indicate user permissions (what the owner of the file can do, see filed 3), the next 3 characters show group permissions (what users who belong to the file's group can do, see field 4), and the last three show permissions for other users. The charcters you'll see here:
   * `r` - read permission
   * `w` - write permission
   * `x` - execute permission

2. The `second field` shows the number of links to for this file.
3. The `third field` shows the owner of the file.
4. The `forth field` shows to which group this file belongs.
5. The `fith field` shows the file size.
6. The `sixth field` shows one of the [timestamps](./file-timestamps.md) for the file. Without any additional options to `ls` command, it shows the last modification date and time. If you add `-c` option to `ls -l` command, it will show the **change time**. If `-u` option is provided, it will show **access time**.
7. The `seventh field` shows the name of the file. If a file is a link, it will show which file it points to.

---

To get infromation only about some specific file, provide the path to that file to `ls` command:

```bash
$ ls -l LICENSE.md
-rw-rw-r-- 1 vagrant vagrant 1082 Apr  1 02:08 LICENSE.md
```

---

Use **-h** option to print the file size in **human-readable** format, i.e. to display it in kilobytes (`K`), megabytes (`M`) and gigabytes (`G`), rather than in default kilobytes:

```bash
$ ls -lh LICENSE.md
-rw-rw-r-- 1 vagrant vagrant 1.1K Apr  1 02:08 LICENSE.md
```

---

By default, if you provide a path to a directory to `ls` command, it will list its contents. But if you want to get the same type of detailed information about a directory as we've seen with the files, use the **-d** option:

```bash
$ ls -lhd examples/
drwxrwxr-x 2 vagrant vagrant 4.0K Apr  1 03:52 examples/
```

**Note:** the size of a directory file that you will see in the output is not the total size of the directory. Use the [du](du.md) command instead, if you want to see the total size of the directory:

```
$ ls -lhd examples/
drwxrwxr-x 2 vagrant vagrant 4.0K Apr  1 03:52 examples/
$ du -sh examples/
25M	examples/
```

---

Use the wildcard to limit the `ls` command output to only specific files. For example, to get information about the files which name starts with `Gemfile`, I can use the following command:

```bash
$ ls -lh Gemfile*
-rw-rw-r-- 1 vagrant vagrant  342 Apr  1 02:08 Gemfile
-rw-rw-r-- 1 vagrant vagrant 2.4K Apr  1 02:08 Gemfile.lock
```

#### Sort the output

To sort files by their sizes, use the **-S** option. The largest file will come first:

```bash
$ ls -lhS examples/
total 25M
-rw-rw-r-- 1 vagrant vagrant  24M Apr  1 03:52 blob.txt
-rw-rw-r-- 1 vagrant vagrant 3.6K Apr  1 02:08 Configurations.md
-rw-rw-r-- 1 vagrant vagrant 2.3K Apr  1 02:08 Actions.md
-rw-rw-r-- 1 vagrant vagrant 1.4K Apr  1 02:08 Graphs.md
-rw-rw-r-- 1 vagrant vagrant  497 Apr  1 02:08 Applications.md
```
---

To sort files based on their last modification time, use the **-t** option. The newest file will be listed first:

```bash
$ ls -lht examples/
total 25M
-rw-rw-r-- 1 vagrant vagrant 2.3K Apr  1 04:23 Actions.md
-rw-rw-r-- 1 vagrant vagrant  24M Apr  1 03:52 blob.txt
-rw-rw-r-- 1 vagrant vagrant  497 Apr  1 02:08 Applications.md
-rw-rw-r-- 1 vagrant vagrant 3.6K Apr  1 02:08 Configurations.md
-rw-rw-r-- 1 vagrant vagrant 1.4K Apr  1 02:08 Graphs.md
```

To reverse this order and list the older file first, use the **-r** option:

```
$ ls -lhtr examples/
total 25M
-rw-rw-r-- 1 vagrant vagrant 1.4K Apr  1 02:08 Graphs.md
-rw-rw-r-- 1 vagrant vagrant 3.6K Apr  1 02:08 Configurations.md
-rw-rw-r-- 1 vagrant vagrant  497 Apr  1 02:08 Applications.md
-rw-rw-r-- 1 vagrant vagrant  24M Apr  1 03:52 blob.txt
-rw-rw-r-- 1 vagrant vagrant 2.3K Apr  1 04:23 Actions.md
```

---

To sort by extention name, use the **-X** option:

```bash
$ ls -lX examples/
total 24592
-rw-rw-r-- 1 vagrant vagrant     2339 Apr  1 04:23 Actions.md
-rw-rw-r-- 1 vagrant vagrant      497 Apr  1 02:08 Applications.md
-rw-rw-r-- 1 vagrant vagrant     3617 Apr  1 02:08 Configurations.md
-rw-rw-r-- 1 vagrant vagrant     1382 Apr  1 02:08 Graphs.md
-rw-rw-r-- 1 vagrant vagrant 25165824 Apr  1 03:52 blob.txt
```

#### Other options

If you're interested to see the type of files and don't need all the other information the long listing option (`-l`) provides, use the **-F** (**--classify**) to classify the listed files:

```bash
$ ls -1F
examples/
file1.txt@
Gemfile
Gemfile.lock
lib/
LICENSE.md
Rakefile
script.sh*
spec/
```

In the output directories have a slash `/` at the end of their names, link files will have `@` sign at the end of the name, executable files will have `*` symbol appended to their names. Files without special characters at the end of their names are just regular files.


### Resources used to create this document:
* https://www.rapidtables.com/code/linux/ls.html
* https://www.thegeekstuff.com/2009/07/linux-ls-command-examples/
* man ls

