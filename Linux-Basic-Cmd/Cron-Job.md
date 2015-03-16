### Set Cron Jobs in Linux:

Tasks can be configured to run automatically within a specified period of time, on a specified date in Linux. Cron job are used to schedule commands to be executed periodically.Each user can have their own crontab file, and though these are files in /var/spool/cron/crontabs, they are not intended to be edited directly. You need to use crontab command for editing or setting up your own cron jobs.

NOTE: You can make entries in cron file by running following command:

```
crontab -e
```
####Basic Syntax to set  Cron job:
```
# Open Crontab file:
crontab -e

# Now, Make entries:

* * * * * command to be executed
- - - - -
| | | | |
| | | | ----- Day of week (0 - 7) (Sunday=0 or 7)
| | | ------- Month (1 - 12)
| | --------- Day of month (1 - 31)
| ----------- Hour (0 - 23)
------------- Minute (0 - 59) 
```

#### Various Examples:

```
# If you have to run script named /root/execute.sh run every day at 3am:
0 3 * * * /root/execute.sh

# To run /path/to/command five minutes after midnight, every day, enter:
5 0 * * * /path/to/command

#Run /path/to/script.sh at 2:15pm on the first of every month, enter:
15 14 1 * * /path/to/script.sh

#Run /scripts/phpscript.php at 10 pm on weekdays, enter:
0 22 * * 1-5 /scripts/phpscript.php

#Run /root/scripts/perl/perlscript.pl at 23 minutes after midnight, 2am, 4am ..., everyday, enter:
23 0-23/2 * * * /root/scripts/perl/perlscript.pl

#Run /path/to/unixcommand at 5 after 4 every Sunday, enter
5 4 * * sun /path/to/unixcommand
```
#### Use Operators:

#####The asterisk (*) : 
This operator specifies all possible values for a field. For example, an asterisk in the hour time field would be equivalent to every hour or an asterisk in the month field would be equivalent to every month.
#####The comma (,) : 
This operator specifies a list of values, for example: "1,5,10,15,20, 25".
#####The dash (-) : 
This operator specifies a range of values, for example: "5-15" days , which is equivalent to typing "5,6,7,8,9,....,13,14,15" using the comma operator.
#####The separator (/) : 
This operator specifies a step value, for example: "0-23/" can be used in the hours field to specify command execution every other hour. Steps are also permitted after an asterisk, so if you want to say every two hours, just use */2.

####disable email output:
```
0 3 * * * /root/backup.sh >/dev/null 2>&1
```
####To mail output to particular email account
```
MAILTO="vivek@nixcraft.in"
0 3 * * * /root/backup.sh >/dev/null 2>&1
```
####List all your cron job:
```
# crontab -l
# crontab -u username -l
```
####To remove or erase all crontab jobs:
```
crontab -r
crontab -r -u username # delete job of a specific user.
```
####Use special String
```
Special string	Meaning
@reboot	Run once, at startup.
@yearly	Run once a year, "0 0 1 1 *".
@annually	(same as @yearly)
@monthly	Run once a month, "0 0 1 * *".
@weekly	Run once a week, "0 0 * * 0".
@daily	Run once a day, "0 0 * * *".
@midnight	(same as @daily)
@hourly	Run once an hour, "0 * * * *".

Ex:
@hourly /path/to/ntpdate
```
#### Cron Files:
Main configuration file for cron is "/etc/crontab"

#### Controlling Access to Cron:
The /etc/cron.allow and /etc/cron.deny files are used to restrict access to cron. The format of both access control files is one username on each line. Whitespace is not permitted in either file. The cron daemon (crond) does not have to be restarted if the access control files are modified. The access control files are read each time a user tries to add or delete a cron task.

The root user can always use cron, regardless of the usernames listed in the access control files.

If the file cron.allow exists, only users listed in it are allowed to use cron, and the cron.deny file is ignored.

If cron.allow does not exist, users listed in cron.deny are not allowed to use cron.

#### backup installed cron jobs entries:
Simply type the following command to backup your cronjobs to a nas server mounted at /nas01/backup/cron/users.root.bakup directory::
```
crontab -l > /nas01/backup/cron/users.root.bakup
# crontab -u userName -l > /nas01/backup/cron/users.userName.bakup
```
#### By default crontab will use the vi / vim text editor. To use nano as text editor just run following command before running ``crontab -e` :
```
export EDITOR=nano
```


