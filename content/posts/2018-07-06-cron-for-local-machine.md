---
date: "2018-07-06"
layout: post
title: Leveraging Cron job to monitor network connection
tags: [linux, cron, automation]
---

#### Scenario:

There are a handful of ways to run some script periodically on a linux machine. [Cron jobs](https://en.wikipedia.org/wiki/Cron), [watch command](http://www.linfo.org/watch.html). Cron jobs are heavily used in server infrastructure for task like regular disk clean up, network monitoring, hard disk storage monitoring, setting up alerts, etc. 

I have been running Ubuntu 18.04 and one common issue that I have observed is, in certain wireless connections, after a certain time network-manager stops working. After verifying that I had correct and the updated network drivers installed (`lshw -class network`), I had no other option rather than resorting to just re-starting the network-manager. As it started happening frequently I thought of just writing a crontab entry for this task and in the process I learned quite a few things.

First, detecting network status can be quite straight forward. You can just ping an IP or DNS IP (8.8.8.8 Google's DNS IP in my case). 

`ping -q -c 1 -W 1 8.8.8.8`

If the ping is not succesful we can then restart the `network-manager`

The final script is:

```bash

set -x
if ping -q -c 1 -W 1 8.8.8.8 >/dev/null; then
   echo "$(date '+%d/%m/%Y %H:%M:%S') ::network is up"
   
else
   echo "$(date '+%d/%m/%Y %H:%M:%S') ::restarting network-manager"
   /usr/sbin/service network-manager restart
   sleep 5
   ssid=$(nmcli -t -f active,ssid dev wifi | egrep '^yes' | cut -d\' -f2)
   if [ "$ssid" =  "yes:company_wifi_ssid" ]; then
   echo "connected to company wifi.. changing namserver to 10.10.4.17"
   echo "nameserver 10.10.4.17">>/etc/resolv.conf
fi 
```

`set -x` is there so that I can see all the executables output. I am echoing timestamp and some information to be logged in crontab entry for inspection.
Instead of using `service`, I had to use /usr/sbin/service which is where service bin in actually located, (`whereis service`). [I had to find out the hard way that using fully executable path is preferred while writing jobs.](https://unix.stackexchange.com/questions/469927/unable-to-restart-network-manager-from-a-script-when-run-as-a-cron-job?noredirect=1#comment857070_469927). Since my workplace uses it's own DNS resolver I am also changing nameserver (10.10.4.17 for example). To accomplish this I am comparing ssid of the connected wifi (after waiting for 5 seconds which turns to be just about right)


#### Adding crontab entry:
Since starting network-manager requires root privileges, I added it in root's crontab entry using: ```sudo crontab -e```

```bash
* * * * * sh /usr/local/bin/check_network.sh >> /var/log/myjob.log
```

I appended the output log to a log file inside /var/log, just to monitor and find out the time when network was down and was restarted.

A small section of this log file as run in my system looks something like:

```
19/09/2018 22:23:01 ::network is up
19/09/2018 22:24:01 ::network is up
19/09/2018 22:25:01 ::network is up
19/09/2018 22:26:01 ::network is up
19/09/2018 22:27:01 ::restarting network-manager
connected to cleartrip wifi.. changing namserver to 10.10.4.17
19/09/2018 22:28:01 ::network is up
19/09/2018 22:29:01 ::network is up
19/09/2018 22:30:01 ::network is up
19/09/2018 22:31:01 ::network is up
```


