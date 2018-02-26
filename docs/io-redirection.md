## I/0 Redirection

By convention in UNIX and Linux, everything is treated as a file. This includes data streams and peripherals (device files).

Each open file gets assigned a file descriptor. A **file descriptor** is simply a number that the operating system assigns to an open file to keep track of it. Consider it a simplified type of file pointer.

There are always three default files open, **stdin** (the keyboard), **stdout** (the screen), and **stderr** (error messages output to the screen).These open files get assigned the following file descriptors:

* stdin = 0
* stdout = 1
* stderr = 2

Any open files can be redirected. **Redirection** means capturing output from a file, command, program, script, or even code block within a script  and sending it as input to another file, command, program, or script.

### Output redirection

Every Unix-based operating system has a concept of “a default place for output to go”. Since that phrase is a mouthful, everyone calls it “standard output”, or “stdout”, pronounced standard out. Your shell (probably bash or zsh) is constantly watching that default output place. When your shell sees new output there, it prints it out on the screen so that you, the human, can see it. Otherwise `echo 'Hello world!` would send `Hello world!` to that default place and it would stay there forever.

`echo 'Hello world!' 1> file.txt` - redirects standard output which is produced by `echo` command and which is represented by the file descriptor `1` to a file with the name `file.txt`. As a result, `Hello world!` will be written to `file.txt`.

Redirecting stdout is so common that you can ommit the file descriptor `1` in this command and the result will be the same:

```bash
$ echo 'Hello world!' > file.txt
$ cat file.txt
Hello world!
```

So `>` defaults to `1>`.

---

The similar way we can redirect standard error which is represented by file descriptor `2`. Try to read from a non-existent file and redirect the error message to a file `error.txt`:

```bash
$ cat non-existent-file.txt 2> error.txt # we don't see the error in the terminal
$ cat error.txt
cat: non-existent-file.txt: No such file or directory
```

---

Note that `>` will overwrite the content of a file. If you want to append to the end instead of overwriting the whole file use `>>`

### Input redirection

Standard input (stdin) is the default place where commands listen for information. For example, `cat` command listens to stdin and outputs any data written to it to stdout until you send it an `EOF` character (`ctrl+d`):

```bash
$ cat
hello world
hello world
blah blah
blah blah
[ctrl + d]
```

You can redirect to stdin from a file. Redirecting to stdin is done via `0<`. You can also use just `<` without specifying a file descriptor explicitly and this will default to `0<`:

```bash
$ cat 0< file.txt
Hello world!
$ cat < file.txt
Hello world!
```

### Redirection between file descriptors

You can reference a file descriptor in redirecton by using `&`. For example, to redirect stderr to stdout you would use a command like this:

```bash
$ cat non-existent-file.txt > error.txt 2>&1
```

Let's break this command in parts. First, we redirect the output of command `cat` to file `error.txt`:

```
cat non-existent-file.txt > error.txt
```

Then we redirect the stderr to **the place where stdout is currently pointing**, by using referncing the file descriptor of stdout `2>&1`. Because the previous stdout is pointing to file `error.txt`, the stderr will also be redirecting to this file and the error will be written to this `error.txt`.

```bash
$ cat non-existent-file.txt > error.txt 2>&1
$ cat error.txt
cat: non-existent-file.txt: No such file or directory
```

I specifically put the **the place where stdout is currently pointing** in bold to make it clear that when we write this `2>&1` we're referencing the current place the last stdout is pointing to. Compare the previous command to the following example:

```bash
$ cat non-existent-file.txt 2>&1 > error.txt
cat: non-existent-file.txt: No such file or directory
$ cat error.txt
```

Here we're redirecting stderr to stdout before we redirect stdout to the file. As a result we'll see the error on the screen and it won't be written to the `error.txt` file.

If you're still unsure how it works, reading [this](http://wiki.bash-hackers.org/howto/redirection_tutorial#order_of_redirection_ie_file_2_1_vs_2_1_file) might help.

**Remember:** `&1` means `the place that stdout is currently pointing to`

---

A shortcut for redirecting stderr and stdout to the same file is `&>`:

```bash
$ cat non-existent-file.txt &> error.txt
$ cat error.txt
cat: non-existent-file.txt: No such file or director
```

### Redirecting with exec

`exec` allows to manipulate file descriptors. The redirection after exec modifies the file descriptors of the current shell. for example, if you run `exec > file.txt`, then this output redirection applies to the entire shell: any output produced by commands in this shell is written to `file.txt` instead of to your terminal. Try it yourself in your terminal:

```bash
$ bash # start a new shell
$ exec > file.txt # redirect all output in the current shell to file.txt
$ echo "this will be written to file.txt" # output redirected to file.txt
$ exit # exit current sheel
$ cat file.txt
this will be written to file.txt
```

Redirecting with exec is useful if you have a bunch of commands (a script) whose output needs to be redirected. For example run the following script in you terminal.

```bash
#!/bin/bash
 
exec 1>file.txt
 
echo "this will be redirected to file.txt"
echo "this will be redirected too"
date
```

Then check the contents of file.txt:

```bash
$ cat file.txt
this will be redirected to file.txt
this will be redirected too
Вс фев 25 19:16:09 PST 2018
```

All output produced by commands in the script was redirected and saved to `file.txt`.


See [more examples of using exec in bash scripts](https://likegeeks.com/shell-scripting-awesome-guide-part4/)

### Useful resource

[Bash redirection cheetsheat](http://www.catonmat.net/download/bash-redirections-cheat-sheet.pdf)
[Redirection tutorial](http://wiki.bash-hackers.org/howto/redirection_tutorial)

Also the following resources were used to create this document:
* https://www.tldp.org/LDP/abs/html/io-redirection.html#FTN.AEN17894
* https://robots.thoughtbot.com/input-output-redirection-in-the-shell




