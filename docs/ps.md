## ps

**ps** command lets you view the currently running processes.

`ps` command is mostly used with different options. Without any options, it will display the processes for the current shell:

```bash
$ sleep 10 &
[1] 2817
$ ps
  PID TTY          TIME CMD
 1898 pts/0    00:00:00 bash
 2817 pts/0    00:00:00 sleep
 2818 pts/0    00:00:00 ps
```
Depending on the options, the ouptput will include different collumns of information about the processes. In this case it includes 4 collumns:
* `PID` - the process ID
* `TTY` - the terminal associated with the process
* `TIME` - the total CPU usage time
* `CMD` - the name of the command that launched the process


### Note on syntax

`ps` comes with a set of 3 syntax styles which define how you pass options to the command and what those options mean:

1. **Unix style** in which options are as usual **preceded by a dash** 
2. **BSD style** in which options are **not preceded by a dash**
3. **GNU** long options which are **preceded by two dashes**


**NOTE:** each style has their own set of options. This means that commands `ps -aux` and `ps aux` are not the same thing. `-u` is used to show processes owned by a given user. Thus the command `ps -aux` will look for processes owned by the user named `x` and if the user doesn't exist and the `ps` command won't try to interpret it as a BSD style (which some versions of `ps` might do), it will result in error:

```bash
$ ps -aux
ps: No user named 'x'
```

The `u` option (BSD style) means showing detailed information about processes, while `x` lists all processes that don't have a TTY associated with it. So it won't give you an error.

### Popular options

#### List all running processes

**Unix syntax.**

`-A` and `-e` options are identical:

```
$ ps -A
$ ps -e | head -3
PID TTY          TIME CMD
  1 ?        00:00:00 init
  2 ?        00:00:00 kthreadd
```

To get more information about the processes, you can use `-f` (full format) and `-F` (extra full format) options:

```bash
$ ps -ef | head -3
UID        PID  PPID  C STIME TTY          TIME CMD
root         1     0  0 00:57 ?        00:00:00 /sbin/init
root         2     0  0 00:57 ?        00:00:00 [kthreadd]
$ ps -eF | head -3
UID        PID  PPID  C    SZ   RSS PSR STIME TTY          TIME CMD
root         1     0  0  8402  2920   0 00:57 ?        00:00:00 /sbin/init
root         2     0  0     0     0   0 00:57 ?        00:00:00 [kthreadd]
```

Short explanation of the output columns:

* `UID` - ID of a user who ran the command that started the process
* `PPID` - PID of the parent process that kicked off the command
* `C` - the number of children a process has
* `STime` - start time for the process

Note: for all usernames if the length is greater than 8 characters then `ps` will fall back to show only the UID instead of a username.

**BSD syntax.**

`a` is used to list all running processes with a TTY. 

`x` includes processes in the list which don't have a controlling terminal. These programs will show a `?` in the TTY field and are most common for daemon processes that launch as part of the system startup:

```bash
$ ps ax | head -3
PID TTY      STAT   TIME COMMAND
  1 ?        Ss     0:00 /sbin/init
  2 ?        S      0:00 [kthreadd]
```

To get a more detailed information, add the `u` option:

```
$ ps aux | head -3
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root         1  0.0  0.5  33608  2920 ?        Ss   00:57   0:00 /sbin/init
root         2  0.0  0.0      0     0 ?        S    00:57   0:00 [kthreadd]
```

#### List processes by a user 

To filter the processes by the owning user, use the `-u` option followed by the username or effective user id. Multiple users can be provided separated by a comma:
```bash
$ ps -fu vagrant
UID        PID  PPID  C STIME TTY          TIME CMD
vagrant   1897  1827  0 01:01 ?        00:00:00 sshd: vagrant@pts/0
vagrant   1898  1897  0 01:01 pts/0    00:00:00 -bash
vagrant   3248  1898  0 05:09 pts/0    00:00:00 ps -fu vagrant
$ ps -u user1,user2 # specify multiple users
```

#### List processes by PID and PPID

Each process when started will be assigned with the unique Process ID (PID).

Use `-p` option followed by a list of PIDs to list specific processes based on their PID:

```
$ sleep 60 &
[1] 3341
$ sleep 60 &
[2] 3342
$ ps -fp 3341
UID        PID  PPID  C STIME TTY          TIME CMD
vagrant   3341  1898  0 05:28 pts/0    00:00:00 sleep 60
$ ps -fp 3341 3342
UID        PID  PPID  C STIME TTY      STAT   TIME CMD
vagrant   3341  1898  0 05:28 pts/0    S      0:00 sleep 60
vagrant   3342  1898  0 05:29 pts/0    S      0:00 sleep 60
```

___

When you start a process (e.g. launch an application), it might fork a number of processes and each child process will have its own PID. Besides its own PID, the forked process will also have information about the PID of a **parent process** (PPID):

```bash
$ ps -ef | grep nginx
root      3896     1  0 05:39 ?        00:00:00 nginx: master process /usr/sbin/nginx
www-data  3897  3896  0 05:39 ?        00:00:00 nginx: worker process
www-data  3898  3896  0 05:39 ?        00:00:00 nginx: worker process
www-data  3899  3896  0 05:39 ?        00:00:00 nginx: worker process
www-data  3900  3896  0 05:39 ?        00:00:00 nginx: worker process
```

Here the third column contains the PPID information.

To base your search on a PPID, use the `--ppid` option:
```
$ ps -f --ppid 3896
UID        PID  PPID  C STIME TTY          TIME CMD
www-data  3897  3896  0 05:39 ?        00:00:00 nginx: worker process
www-data  3898  3896  0 05:39 ?        00:00:00 nginx: worker process
www-data  3899  3896  0 05:39 ?        00:00:00 nginx: worker process
www-data  3900  3896  0 05:39 ?        00:00:00 nginx: worker process
```
#### List processes by a command name

Often `ps` is used with `grep` to get the list of process with the given command:

```bash
$ ps -ef | grep nginx
root      3896     1  0 05:39 ?        00:00:00 nginx: master process /usr/sbin/nginx
www-data  3897  3896  0 05:39 ?        00:00:00 nginx: worker process
www-data  3898  3896  0 05:39 ?        00:00:00 nginx: worker process
www-data  3899  3896  0 05:39 ?        00:00:00 nginx: worker process
www-data  3900  3896  0 05:39 ?        00:00:00 nginx: worker process
vagrant   4262  1898  0 06:29 pts/0    00:00:00 grep --color=auto nginx 
```

But ps command itself has an option to achieve the same. The following example shows all the processes that have 'nginx' in the command field:

```bash
$ ps -fC nginx
UID        PID  PPID  C STIME TTY          TIME CMD
root      3896     1  0 05:39 ?        00:00:00 nginx: master process /usr/sbin/nginx
www-data  3897  3896  0 05:39 ?        00:00:00 nginx: worker process
www-data  3898  3896  0 05:39 ?        00:00:00 nginx: worker process
www-data  3899  3896  0 05:39 ?        00:00:00 nginx: worker process
www-data  3900  3896  0 05:39 ?        00:00:00 nginx: worker process
```


#### Specify output format

Use the `-o <format>` option to define output format for the `ps` command. `<format>` is a blank-separated or comma-separated list, elements of which represent output collumns. The recognized keywords are described in the STANDARD FORMAT SPECIFIERS section of the man pages for ps (run `man ps`).

```bash
$ ps -e -o uid,pid,cmd | head -3
UID   PID CMD
  0     1 /sbin/init
  0     2 [kthreadd]
```

Headers may also be renamed:

```bash
$ ps -e -o uid=USER,pid,cmd=COMMAND | head -3
USER   PID COMMAND
  0     1 /sbin/init
  0     2 [kthreadd]
```

If all column headers are empty then the header line will not be output:

```bash
$ ps -e -o uid=,pid=,cmd= | head -3
0     1 /sbin/init
0     2 [kthreadd]
0     3 [ksoftirqd/0]
```

You can also use the `–-forest` option which displays ASCII art process tree. From this tree, it's easier to identify parent and child process relationship:

```bash
$ ps -f --forest -C nginx
UID        PID  PPID  C STIME TTY          TIME CMD
root      3896     1  0 05:39 ?        00:00:00 nginx: master process /usr/sbin/nginx
www-data  3897  3896  0 05:39 ?        00:00:00  \_ nginx: worker process
www-data  3898  3896  0 05:39 ?        00:00:00  \_ nginx: worker process
www-data  3899  3896  0 05:39 ?        00:00:00  \_ nginx: worker process
www-data  3900  3896  0 05:39 ?        00:00:00  \_ nginx: worker process
```

#### Display elapsed time for the process

Find out how long the process has been running:

```bash
$ ps -o pid,cmd,etime -C nginx
PID CMD                             ELAPSED
3896 nginx: master process /usr/    01:18:20
3897 nginx: worker process          01:18:20
3898 nginx: worker process          01:18:20
3899 nginx: worker process          01:18:20
3900 nginx: worker process          01:18:20
```


### Resources used to create this document
* https://www.binarytides.com/linux-ps-command/
* https://shapeshed.com/unix-ps/
* https://linuxjourney.com/lesson/monitor-processes-ps-command
* https://www.thegeekstuff.com/2011/04/ps-command-examples/
* man ps
