## lsof

**lsof** (pronounced _el-soff_) is short for "list open files". It lists information about files opened by processes. An open file may be a regular file, a directory, a NFS file, a block special file, a character special file, a shared library, a regular pipe, a named pipe, a symbolic link, a socket stream, an Internet socket, a UNIX domain socket, and many others. Since almost everything in Unix is a file, `lsof` can be used for a lot of different things.


NOTE: you need to have root permissions to run `lsof` command, since a non-root user don't have access to all files on the system and thus the output will be very limited. 

#### Who is using the files?

List processes which are using the file in some way. It also gives information about the users under which the processes run:

```
# lsof /bin/bash
COMMAND  PID    USER  FD   TYPE DEVICE SIZE/OFF NODE NAME
bash    1898 vagrant txt    REG    8,1  1021112  120 /bin/bash
```

Specify several files to list all the processes that are using them:

```
# lsof /path/to/file1 /path/to/file2
```

Find all open files in a directory recursively:

```
# lsof | grep '/usr/bin'
```

#### List open files by a user

```
# lsof -u vagrant
# lsof -u vagrant,root # specify multiple users
```

**^** indicates **negation**. For example, you can list files opened by all users other than root:
```
# lsof -u ^root
```

#### List open files by a program's name

List all files opened by processes whose name starts with `apach`:

```
# lsof -c apach
```

Specify the beginning of the names of several processes. The output will be combined:

```
# lsof -c apach -c pyth
```

#### List open files by PID

List files opened by a process whose process ID (PID) is known to you:

```
# lsof -p 1898
# lsof -p 1898, 2001 # use multiple PIDs
```

#### List all files associated with specific file descriptors

This lists all files that have been opened as file descriptor `2`:

```
# lsof -d 2
```

Specify a range of file descriptors:

```
# lsof -d 0-2
```

#### List open network connections

**-i** option lists all processes with open Internet sockets (TCP and UDP).

```
# lsof -i
```

List only processes with open TCP sockets:
```
# lsof -i tcp
```

#### Find which process is using the port

The `:80` option to `-i` makes lsof find processes using TCP or UDP port `80`:

```
# lsof -i :80
COMMAND  PID     USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
nginx   2400     root    6u  IPv4  13096      0t0  TCP *:http (LISTEN)
nginx   2400     root    7u  IPv6  13097      0t0  TCP *:http (LISTEN)
nginx   2401 www-data    6u  IPv4  13096      0t0  TCP *:http (LISTEN)
nginx   2401 www-data    7u  IPv6  13097      0t0  TCP *:http (LISTEN)
```

You can also specify TCP or UDP protocol:

```
# lsof -i udp:53
```

#### Output PIDs of processes

**-t** option outputs only PIDs of processes so that the output could be piped to `kill`:

```
# lsof -t -i :80
2400
2401
2402
2403
2404
# kill -9 `lsof -t -i :80`
```

#### Combining selection options with AND

When you specify multiple selection options `lsof` by default will make a single list of open files which consists of outputs each selection option produces. For example, the following command will combine the output of `-u vagrant` and `-c nginx` producing a listing of all open files by user `vagrant` and processes whose name starts with `nginx`:

```
# lsof -u vagrant -c nginx
``` 

If you want to combine options with the `AND`, for example, to list all network files opened by `vagrant` user, you need to use **-a** option:

```
# lsof -a -u vagrant -i
```

### Resources used to create this document 

* http://www.catonmat.net/blog/unix-utilities-lsof/
* [Description of most common options followed by a practical use case of lsof](https://medium.com/@copyconstruct/lsof-f2b224eee7b5)
