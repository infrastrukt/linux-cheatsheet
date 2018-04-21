## xargs

There are often cases when you want to use the output from one command as an argument to another command.

You will most certainly see examples of how we use `ps` command which displays information about all the running processes and then pipe that output as an input to the `grep` command that allows to filter that output and print only the information about the processes that we're interested in.

```bash
$ ps -ef # print a long list of all running processes
UID        PID  PPID  C STIME TTY          TIME CMD
root         1     0  0 19:58 ?        00:00:00 /sbin/init
root         2     0  0 19:58 ?        00:00:00 [kthreadd]
root         3     2  0 19:58 ?        00:00:00 [ksoftirqd/0]
root         4     2  0 19:58 ?        00:00:00 [kworker/0:0]
...
$ ps -ef | grep nginx # prints only information about proccesses with a specific name
root      2327     1  0 19:59 ?        00:00:00 nginx: master process /usr/sbin/nginx
www-data  2328  2327  0 19:59 ?        00:00:00 nginx: worker process
www-data  2329  2327  0 19:59 ?        00:00:00 nginx: worker process
www-data  2330  2327  0 19:59 ?        00:00:00 nginx: worker process
www-data  2331  2327  0 19:59 ?        00:00:00 nginx: worker process
```

But many commands don't allow to pipe their arguments from another command's output. For example, we can't create a folder by piping the output of an `echo` command to `mkdir`:

```bash
$ echo "new-folder" | mkdir
mkdir: missing operand
Try 'mkdir --help' for more information.
```

This is where **xargs** command comes in handy. It reads data from the standard input (the result of a pipe `|`), and executes the command (supplied to it as argument) with the data read from the standard input as arguments to that command:

```bash
$ echo "new-folder" | xargs mkdir
$ ls
new-folder
```

In this example, we see how `xargs` read `new-folder` from the standard input and used it as an argument to `mkdir` command which it then executed.

Moreover, when `xargs` reads the data from standard input, it treats spaces as argument delimiters:

```bash
$ echo "folder1 folder2" | xargs mkdir
$ ls -1
folder1
folder2
new-folder
```

In this example, `xargs` treats `folder1` and `folder2` as two different arguments for `mkdir` command, because they are separated by a space. So it executes the `mkdir` command with 2 arguments.

You can see exactly what commands are executed by `xargs` command by passing the **-t** option:

```bash
$ echo "folder3 folder4" | xargs -t mkdir
mkdir folder3 folder4
$ ls -1
folder1
folder2
folder3
folder4
new-folder
$ ls | xargs -t rm -r
rm -r folder1 folder2 folder3 folder4 new-folder
```

Now that we've grasped the idea of how `xargs` works, let's see some common options and examples of this command.

### Common options and examples

#### Prompt before executing commands

The **-p** option will print the command to be executed and prompt you for the input on whether to run this command or not:

```bash
$ echo "file1.txt file2.txt" | xargs -t touch
touch file1.txt file2.txt
$ echo "file1.txt file2.txt" | xargs -p rm
rm file1.txt file2.txt ?...yes
```

#### How to execute a command per each argument instead of all of them at once

The **-n** option allows to specify the maximum number of arguments that will be used when executing the command:

```bash
$ echo "file1.txt file2.txt" | xargs -t touch
touch file1.txt file2.txt
$ echo "file3.txt file4.txt" | xargs -t -n 1 touch
touch file3.txt
touch file4.txt
$ ls | xargs -t -n 2 rm
rm file1.txt file2.txt
rm file3.txt file4.txt
```

#### xargs with find command

`xargs` is oftern used together with the `find` command to perform various actions on a set of files.

In the following example files, older than two weeks in the `/tmp` folder are found and then piped to the xargs command which runs the rm command on each file and removes them:

```bash
$ find /tmp -type f -mtime +14 | xargs rm
```

The [find](find.md) command has the `-exec` option which allows you to run commands on the found set of files without using `xargs`. This command is equivalent to the one above:

```bash
$ find /tmp -type f -mtime +14 -exec rm {} \;
```

But [some benchmarks](https://danielmiessler.com/blog/linux-xargs-vs-exec/) suggest that using `xargs` over `exec {}` is much more efficient.

#### Read input from the file

The **-a** option allows to read input from the file instead of the stdin. For example:

```bash
$ echo "file1" > input.txt
$ echo "file2" >> input.txt
$ cat input.txt
file1
file2
$ xargs -a input.txt -t touch
touch file1 file2
$ ls
file1  file2  input.txt
```

### Resources used to creat this document:

* https://shapeshed.com/unix-xargs/
* https://www.thegeekstuff.com/2013/12/xargs-examples/
* https://www.howtoforge.com/tutorial/linux-xargs-command/
* https://www.howtoforge.com/tutorial/linux-xargs-command/
* man xargs
