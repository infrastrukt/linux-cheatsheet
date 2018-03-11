# Types of shells

**shell** - is a program that provides text-only user interface for Unix-like operating systems (it allows you to interact with the system). Its primary function is to read commands that are typed into a console or terminal window and then execute them.

## Interactive vs Non-interactive

**Interactive shell** as the name implies allows a user to inteact with it. This type of shell displays a prompt for user to enter commands and can ask you to enter additional input if it's required by the command you run. 

You work with interactive shells most of your time. For example, you get an interactive shell when you're logging in to a system via SSH or just open a terminal on your local machine. Rule of thumb: if you see a command prompt - it's an interactive shell.

A **non-interactive shell** doesn't allow user interaction. You get this type of shell when you run a script. All scripts run in their own subshell (a child process of the parent shell) and this shell closes immediately once the script is executed.

## Login vs Non-login

A **login shell** is started after a successful login to the system. _Login_ is a process of getting access to a system which usually requires you to specify a user name whose permissions you want to assume and some credentials such as a password or a secret key. Thus, whenever you have to login into a system (e.g., when you try to login to a remote system via SSH) you'll get a login shell after successful login.

There are also ways to start a login shell when already logged in. Each of the following commands will launch a login shell:

```
$ su - [username]
$ su -l [username]
$ su --login [username]
$ sudo -i
```

You can check if the current shell you're running is a login shell by using the following command:

```bash
$ echo $0
-bash
```

If the output is prepended with a dash (e.g. `-bash`, `-su`), it indicates that the current shell is a login shell. A non-login shell won't have a `-` prefix.

A **non-login shell**  is a shell that you can start without login. For example, opening a new terminal window on you local machine launches a non-login shell, since it doesn't require you to type a user name or password because you're already logged into the system.

You can start a non-login shell, when you're already logged into a system, by calling a shell executable, for example:

```bash
$ ssh ubuntu@10.0.0.1 # login to a system
$ echo $0             # check my current shell
-bash                 # it's a login shell
$ bash                # start a non-login shell
$ echo $0             # check my current shell
bash                  # it's a non-login shell
$ exit                # exit a subshell
$ echo $0             # check my current shell
-bash                 # I returned to my login shell
```

All scripts run in their own subshell which are most of the times non-login shells (check `-l` option for `bash` for login shells).

`su` command without `-`, `-l` or `--login` also launches a non-login shell.


### Importance of login shells. Shell startup files.

The real difference between login and non-login shells lies in what **startup files** they call to configure the shell environment.

#### Login shell

When `bash` is invoked as a login shell it first reads and executes commands from the file `/etc/profile`, if that file exists. After reading that file, it looks for `~/.bash_profile`, `~/.bash_login`, and `~/.profile`, in that order, and reads and executes commands from the first one that exists and is readable. 

Creating a login shell with `su` or `sudo -i` command will also move you to the home directory of a new user.

When a login shell exits, `bash` reads and executes commands from the file `~/.bash_logout`, if it exists.

#### Interactive non-login shell

When a non-login shell is started, `bash` reads and executes commands from `~/.bashrc`, if that file exists.

Typically, your ~/.bash_profile contains the line
```
if [ -f ~/.bashrc ]; then . ~/.bashrc; fi
```
to source `~/.bashrc` for login shells too. 

#### Non-interactive no-login shells

A non-interactive shell is usually present when a shell script is running. It is non-interactive because it is processing a script and not waiting for user input between commands. For these shell invocations, only the environment inherited from the parent shell is used.


#### A few notes for common startup files

* `/etc/profile`    - System wide environment variables and startup programs.
* `/etc/bashrc`     - System wide aliases and functions
* `~/.bash_profile` - User personal environment variables and startup programs
* `~/.bashrc`       - User personal aliases and functions



## Resources used to create this document

* http://www.linuxfromscratch.org/blfs/view/cvs/postlfs/profile.html
* http://howtolamp.com/articles/difference-between-login-and-non-login-shell/
* man bash
