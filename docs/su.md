# Switching the user

## Short intro into login sessions

A **login**, **logging in** or **logging on** is the entering of identifier information into a system by a user in order to access that system (e.g., a computer or a website). It is an integral part of computer security procedures.

A login generally requires the user to enter two pieces of information, first a _user name_ and then a _password_. This information is entered into a login window on a GUI (graphical user interface) or on the command line in a console (i.e., an all-text mode screen), depending on the system and situation.

A _user name_, also referred to as an _account name_, is a string that uniquely identifies a user on the system which tried to be accessed.

When someone logs into an **ordinary user account** on an operating system that person receives access to only a limited set of files and programs on the system. When someone logs into the **root account** (i.e., administrative), that person acquires access to all files and programs on the system.

In addition to restricting access, logins also provide an audit trail in the form of data that is automatically entered into system log files. Such data can be studied by the system administrator routinely or if there are indications of suspicious activities or damage to the system.

The process of logging in creates a **session** (i.e., a period of use), also referred to as a **login session**, for the user on the target machine or system. That user is referred to as the **owner of that session**.

When a user finishes a computer session, that user will generally log out. **Logging out** is the act of informing the machine or system that the current user is ending use of it, which results in the system terminating the session. Terminating a session prevents other people from accessing the system without first logging in.

## su command

The **su** (short for substitute user) command makes it possible to change a login session's owner (i.e., the user who originally created that session by logging on to the system) without the owner having to first log out of that session.

Although su can be used to change the ownership of a session to any user, it is most commonly employed to change the ownership from an ordinary user to the root (i.e., administrative) user, thereby providing access to all parts of and all commands on the computer or system. 

A convenient way of eliminating the need for typing su numerous times during a session is to open a terminal window, run su once in that window and then use the window for all root tasks (i.e., tasks for which administrative privileges are required) during the session.

One of the advantages of using su instead of logging in as root is that a record is kept of its usage in system logs, whereas no such record is kept if one logs in directly as root.

### Syntax

A simplified expression of the syntax of the su command is:

```
su [options] [commands] [-] [username]
```

In the absence of a username, the OS assumes the the user wants to change to the owner of the session to root, and thus the user is prompterd for the root password. This makes these two commands equivalent:

```
$ su
$ su root
```

If the correct root password is provided, ownership of the session is changed to root.

Likewise, to transfer the ownership of a session to any other user, the name of that user is typed after su and a space. For example, to change the owner of the current login session to a user named alice, we could use the following command:
```
su alice
```

The default behavior of `su` is to start a **non-login interactive shell**. [Read about different types of shells](./shells). This type of shell inherits the parent shell environment (which particularly means that you'll stay in the current directory from which you called the `su` command), additionally the `~/.bashrc` file of the user you're switching to will be read for startup configuration instructions. 

To launch a **login interactive shell** use `-`, `-l` or `--login` (they all mean the same thing) when swithing the user. This will switch the current directory to the home directory of the new user. On start of a login shell normally `/etc/profile` and its private equivalent `~/.bash_profile` are read to configure the shell enviroment of according to the user normal login settings.

```bash
$ su -
$ su -l
$ su --login
```

This is effectively the same as loging out and loging in as the root user, while use of `su` without of this options is a little different.

To switch to a user other than root and use a login shell:

```
$ su - bob
```

This will ask you for the bob's password. If you provide a correct password , it will launch a login shell for bob and you end up in a bob's home directory with his login environment settings defines in files like `~/.bash_profile` (or `~/.profile` if the default shell for bob is `/bin/sh`).

### Running commands as another user

Among the most commonly used of `su`'s few options is `-c`, which tells `su` to execute the command that directly follows it on the same line.

```bash
$ su -c 'ls /root'
$ su -c 'ls /root' root # equivalent command
```
The command will be run in a new shell and execute a given command as the new user. After the command is executed, the you are returned to the original user account and shell.

And a new user can be explicitly designated along with the command. For example, the following command switches to user bob's account and produces a list of the contents of bob's home directory (whose contents would not generally be viewable by other ordinary users):
```
$ su -c 'ls /home/bob' - bob
```

### Checking the owner of the session

Sometimes when using `su` it might not be obvious who is actually the owner of the current session.

The easiest way to distinguish whether the owner is the root user or an ordinary user is to observe the command prompt. The last character in the prompt is the pound sign `#` for the root user or the dollar sign `$` for an ordinary user. 

The name of the user of the current session can also be confirmed by using the **whoami** command. Simply type:

```
whoami
```

### Exiting from su

To return the ownership of the session to the original user used for loggin in, use `exit` command or press the `CTRL + d`.


## su and Logs

A major benefit of using `su` is that a record is kept of its usage in a system log, typically `/var/log/messages`. `/var/log/messages` records the username and user ID (UID) of the user that opens a `su` session as well as the times that the session is opened and closed. However, `su` does not keep a record of what is actually done as root, and such information must therefore be obtained from other sources.

## Resources used to created this document:
* http://www.linfo.org/login_def.html
* http://www.linfo.org/su.html
* http://www.linuxfromscratch.org/blfs/view/cvs/postlfs/profile.html
