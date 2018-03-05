## Cron

**Cron** is a system daemon used to execute desired tasks (in the background) at designated times. 

A **crontab file** is a simple text file containing a list of commands meant to be run at specified times. It is edited using the **crontab command**. The commands in the crontab file (also known as **cron jobs**) are checked by the cron daemon, which executes them in the system background.

### User crontabs

Each user (including root) has their own `crontab`, and although these are actual files commonly located in `/var/spool/cron/crontabs/<user>`, they are not intended to be edited directly. You use a `crontab` command to edit cron jobs in those files.

To list your current user's cron jobs run the following:

```
$ crontab -l
```

To edit your crontab file, run:

```
$ crontab -e
```

If you decided to write you own crontab files and wish to use it instead of the existing one, use the following command:

```
$ crontab my.cron.file
```

To remote your current crontab, run:

```
$ crontab -r
```

To edit/list/remove another user's crontab, you can specify the user via `-u` option:

```
$ crontab -l -u test-user
```


### Crontab syntax

A cron job description follows the following format:

``` 
MIN HOUR DOM MON DOW CMD
```

It could be broken down into 2 main parts:

* the first part is the timing section. It includes the fist five sections of the job description and specifies the time when the task should be executed:

** `MIN` - minutes field (0-59)
** `HOUR`- hour field (0-23)
** `DOM` - day of month
** `MON` - month field (1-12)
** `DOW` - day of the week (0-6) Sunday is 0.

* the second part (and the last section) `CMD` specifies which command to run.

#### Specify time

Quite often, you will see an asterisk (`*`) instead of a number in the timing section. This represents all possible numbers for that position. For example, asterisk in the minute position would make it run every minute, in the hour position would make it run every hour, etc.

Examples:

This cron job will run at minute zero, every hour (i.e. an hourly cron job):
```
0 * * * * [command] 
```

This will run once a day, at 2:30am:
```
30 2 * * * [command]
```

You can use multiple numbers separated by commas. This will run three times every hour, at minutes `0`, `10` and `20`:

```
0,10,20 * * * * [command]
```

Dash can be used to specify a range. This will run once every hour between 5:00am and 10:00am:

```
0 5-10 * * * [command]
```

Slash operator is also used to specify step values. This will run every 5 minutes:

```
*/5 * * * * 
```

Also there is a special keyword that will let you run a cron job every time the server is rebooted:

```
@reboot [command]
```

Use [crontab guru](https://crontab.guru/) to get the timing right.

#### Specify command

Example:

```
* * * * * echo "hello world" >> /home/ubuntu/hello.txt
```

Note: if the cron job produces output it will be emailed to the owner of the
process, or the person specified in the MAILTO variable. If you want to disable emailing the results of a cron job, you need to handle the output. There are a few ways you can do that:

Redirect all output to a logfile.
```
cmd >> logfile 2>&1
```

Disable all output completely:
```
cmd >/dev/null 2>&1
```

### System wide crontabs

**/etc/crontab** and files in **/etc/cron.d** are a system wide crontabs.

Unlike any user crontab's you don't have to run the `crontab` command to edit these files. These files also follow a slightly different format, i.e. they have username fields in the job definition which specifies which user should run the task:

```
# m h dom mon dow user      command
*   *  *   *   *  someuser  echo 'foo'
```

It is not recommended that you add anything to `/etc/crontab`. This could cause a problem if the `/etc/crontab` file is affected by system updates.

Put your crontab file in `/etc/cron.d` instead. This directory is often used by packages.

There are also directories called **/etc/cron.hourly/**, **/etc/cron.daily/**, **/etc/cron.weekly/**, and **/etc/cron.monthly/**.
You can place an executable in any of those directories and it will be run according to the time in the name of the directory you choose. Executables will be run as root.

#### When to use system wide crontabs instead of user crontabs? 

In cases when several people might look after a server, the directory `/etc/cron.d` is probably the best place to install crontabs - it's a central point and saves searching for them.

### Resources used to create this document:
* https://help.ubuntu.com/community/CronHowto
