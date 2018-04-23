## Terminating processes. Signals.

The **kill** command is used on Unix-like operating systems to terminate processes without having to logout or reboot the system.

Remember when a new process is created, it gets assigned a unique identifier called process ID or `PID` for short. To terminate a process with the `kill` command you'll need to reference that process by its PID which you will normally find out with the [ps](ps.md) command:

```bash
$ ps aux | grep ping
vagrant   6758  0.0  0.1  14892   976 pts/0    S    00:40   0:00 ping google.com
vagrant   6760  0.0  0.1  10468   924 pts/0    S+   00:40   0:00 grep --color=auto ping
$ kill 6758
[1]+  Terminated              ping google.com > /dev/null
```

The `kill` command has a misleading name because it does not actually kill processes. Rather, it sends **signals** to them. A signal is a notification message to the process. Depending on the type of a singal and how these singals are handled in the program code, the processes can behave differently. For example, a process can:
* ingore certain signals
* catch a specific signal and run some handler function
* process can be terminated immediately

Each signal is identified with a name and a signal number. You can see a list of all available signals by running `kill -l` command:

```bash
$ kill -l
 1) SIGHUP	 2) SIGINT	 3) SIGQUIT	 4) SIGILL	 5) SIGTRAP
 6) SIGABRT	 7) SIGBUS	 8) SIGFPE	 9) SIGKILL	10) SIGUSR1
11) SIGSEGV	12) SIGUSR2	13) SIGPIPE	14) SIGALRM	15) SIGTERM
16) SIGSTKFLT	17) SIGCHLD	18) SIGCONT	19) SIGSTOP	20) SIGTSTP
21) SIGTTIN	22) SIGTTOU	23) SIGURG	24) SIGXCPU	25) SIGXFSZ
26) SIGVTALRM	27) SIGPROF	28) SIGWINCH	29) SIGIO	30) SIGPWR
31) SIGSYS	34) SIGRTMIN	35) SIGRTMIN+1	36) SIGRTMIN+2	37) SIGRTMIN+3
38) SIGRTMIN+4	39) SIGRTMIN+5	40) SIGRTMIN+6	41) SIGRTMIN+7	42) SIGRTMIN+8
43) SIGRTMIN+9	44) SIGRTMIN+10	45) SIGRTMIN+11	46) SIGRTMIN+12	47) SIGRTMIN+13
48) SIGRTMIN+14	49) SIGRTMIN+15	50) SIGRTMAX-14	51) SIGRTMAX-13	52) SIGRTMAX-12
53) SIGRTMAX-11	54) SIGRTMAX-10	55) SIGRTMAX-9	56) SIGRTMAX-8	57) SIGRTMAX-7
58) SIGRTMAX-6	59) SIGRTMAX-5	60) SIGRTMAX-4	61) SIGRTMAX-3	62) SIGRTMAX-2
63) SIGRTMAX-1	64) SIGRTMAX
```

By default, the `kill` command sends a **TERM** (or **SIGTERM**) signal to the process. `TERM` means `termination`. This signal is sent to the process by the operating system asking it to "please terminate". Programs can process this signal and act upon it. For example, this allows a program to perform clean-up operations before actually terminating.

If the program is misbehaving and does not exit when being sent the `TERM` signal, we can use a stronger signal to terminate the process. The **KILL** (or **SIGKILL**) signal identified by the signal number `9` allows to terminate the process immediately. This signal is not sent to the process itself, but instead it is given to the operating system kernel which shuts down the process without asking it:

```bash
$ kill -9 6956 # referencing signal by signal number
[1]-  Killed                  ping google.com > /dev/null
$ kill -SIGKILL # referencing signal by name
[2]+  Killed                  ping google.com > /dev/null
$ kill -KILL 7108 # referencing signal by name
[1]+  Killed                  ping google.com > /dev/null
```

You would want to use the default `TERM` to allow a graceful termination of the process before using the more powerfull `KILL` signal.

### Other common signals

The **HUP** (or **SIGHUP**) is hang-up signal. This signal is used to report that the user’s terminal is disconnected, perhaps because a network connection was broken. It is sent to processes running in a terminal when you close that terminal. Interactive programs normally die in this case, while daemons (programs that don't interact with the user) often reload configuration upon receival of this signal.

For instance, Apache is one program that reloads its configuration when it receives the `HUP` signal:

```
$ sudo kill -HUP <pid_of_apache>
```

So this command, despite the fact that it's a `kill` command, doesn't kill the process, but makes tha Apache server reload.

---

The **INT** (or **SIGINT**) is an interrupt signal. You send this singal when you run a program interactively, and use `Ctrl-C` to interupt it. Non-interactive programs generally treat it like `SIGTERM`:

```bash
$ ping google.com
...
Ctrl-C # sends the INT signal to the program
```

### pkill

The **pkill** command works in almost exactly the same way as `kill`, but it operates on a process name (a partial name can also be used) instead of PID. For example:

```bash
$ ps aux | grep ping
vagrant   3116  0.0  0.1  14892   980 pts/0    S    02:57   0:00 ping google.com
$ pkill ping
[1]+  Terminated              ping google.com > /dev/null
```

Before running `pkill` command you may want to check what processes match the name by which you want to identify the process using the `pgrep` command.  `pgrep` and `pkill` work in a very similar way and share most of the command options, with the only difference that `pgrep` doesn't send a signal to the mathing processes:

```bash
$ pgrep -l apache
2845 apache2
2991 apache2
2992 apache2
$ sudo pkill apache
```

As you can see, this allows you to terminate multiple processes at once.

Note that `pkill` can also kill all processes owned by a particular user

```bash
$ pkill -u <user-name>
```

### killall

The **killall** command is similar to the `pkill` in a way that it also operates on process names instead of PIDs. But, unlike `pkill`, it doesn't work on partial names and requires you to provide the exact process name:

```bash
$ ps aux | grep apache
root      3242  0.0  0.5  73384  2624 ?        Ss   03:06   0:00 /usr/sbin/apache2 -k start
www-data  3245  0.0  0.4 362548  2236 ?        Sl   03:06   0:00 /usr/sbin/apache2 -k start
www-data  3246  0.0  0.4 362548  2236 ?        Sl   03:06   0:00 /usr/sbin/apache2 -k start
$ sudo killall apache
apache: no process found
$ sudo killall /usr/sbin/apache2
```

Use the `-i` (`--interactive`) option to make `killall` command prompt you for confirmation before killing the process:

```bash
$ ps aux | grep apache
root      3493  0.0  0.5  73384  2620 ?        Ss   03:31   0:00 /usr/sbin/apache2 -k start
www-data  3496  0.0  0.4 362548  2232 ?        Sl   03:31   0:00 /usr/sbin/apache2 -k start
www-data  3497  0.0  0.4 362548  2232 ?        Sl   03:31   0:00 /usr/sbin/apache2 -k start
vagrant   3556  0.0  0.1  10468   920 pts/0    S+   03:31   0:00 grep --color=auto apache
$ sudo killall -i /usr/sbin/apache2
Kill apache2(3493) ? (y/N) y
Kill apache2(3496) ? (y/N) y
```

NOTE: the `killall` command matches the first `15` characters of the command name. If the command name is longer than `15` characters, use the `-e` (`--exact`) option to avoid false matches.


### Resources used to create this document:
* http://www.linfo.org/kill.html
* http://www.linfo.org/killall.html
* https://linuxjourney.com/lesson/process-signals
* https://www.digitalocean.com/community/tutorials/how-to-use-ps-kill-and-nice-to-manage-processes-in-linux
* https://unix.stackexchange.com/questions/91527/whats-the-difference-between-pkill-and-killall
