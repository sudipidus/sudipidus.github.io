---
layout: post
title: Reminder for task-warrior CLI
tags: [linux, task-warrior, productivity]
---

**TaskWarrior CLI**

[TaskWarrior](https://taskwarrior.org/) is a CLI based task manager which I find pretty handy to use. When you are working on something (where your linux terminal is always with you), you can just create an interactive todo from your command line. 

```bash
~/sudip/sudipbhandari126.github.io(master*) » task add write a blog about task warrior reminder     sudipbhandari@sudipbhandari-Latitude-5480
Created task 1.
----------------------------------------------------------------------------------------------------------------------------------------------
~/sudip/sudipbhandari126.github.io(master*) » task list                                             sudipbhandari@sudipbhandari-Latitude-5480

ID Age Description                                  Urg 
 1 3s  write a blog about task warrior reminder        0

1 task
----------------------------------------------------------------------------------------------------------------------------------------------
~/sudip/sudipbhandari126.github.io(master*) » task 1 done                                           sudipbhandari@sudipbhandari-Latitude-5480
Completed task 1 'write a blog about task warrior reminder'.
Completed 1 task.
----------------------------------------------------------------------------------------------------------------------------------------------
~/sudip/sudipbhandari126.github.io(master*) » task list                                             sudipbhandari@sudipbhandari-Latitude-5480
No matches.
```

This is very useful in it's own but being a lazy and forgetful person as I am I decided to write a simple script which lists out the tasks every 1 hour or so (I keep changing time interval) and takes the top task and displays a message on the notification panel of my PC.

Following is a sample of a such notifcation:

![taskwarrior](https://sudipbhandari126.github.io/resources/task-warrior.png "taskwarrior")


***Script***

Following is the script (runs on Ubuntu: should work on most nix with minor mod)

```bash
username=$(/usr/bin/whoami)
pid=$(pgrep -u $username nautilus)
dbus=$(grep -z DBUS_SESSION_BUS_ADDRESS /proc/$pid/environ | sed 's/DBUS_SESSION_BUS_ADDRESS=//' )
export DBUS_SESSION_BUS_ADDRESS=$dbus
/usr/bin/notify-send "$(task list | awk 'FNR==4 {$1=$2=""; print $0}')"
```

***Cron entry***

```bash
*/60 * * * * /usr/local/bin/taskstatus
-------
file /usr/local/bin/taskstatus                        sudipbhandari@sudipbhandari-Latitude-5480
/usr/local/bin/taskstatus: symbolic link to /home/sudipbhandari/status.sh
```


***For macos***

package used for notifying: ```terminal-notifier```

Script

```bash
/usr/local/bin/terminal-notifier -title "time to work" -message "$(/usr/local/bin/task list  | awk 'FNR==4 {$1=$2=""; print $0}')"
```


