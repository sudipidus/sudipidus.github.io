---
date: "2019-06-19"
layout: post
title: Curious Case of super huge syslog
tags: [linux,syslog]
---


**The Problem:**

Insufficient memory left on /root partition.

On inspection I found that it was happening because ```/var/log``` was growing till 35 GB (I have a root partition of 50 GB). 

On further inspection I found that ```/var/log/syslog``` was huge


Following was the repeating line (error log that would quickly fill up my disk space)

```
Jun 18 12:09:23 sudipbhandari-Latitude-5480 org.gnome.Shell.desktop[1399]: Error: /bin/java not found
Jun 18 12:09:23 sudipbhandari-Latitude-5480 org.gnome.Shell.desktop[1399]: Error: /bin/java not found
Jun 18 12:09:23 sudipbhandari-Latitude-5480 org.gnome.Shell.desktop[1399]: Type the full pathname of a J2SE installation (or Ctrl-C to quit), the path will be stored in ~/.sqldeveloper/jdk
```


**Reason**

I use sdkman to manage my java/gradle (and other JVM based languages) installation and management.

```sdkman-init.sh #source script to initialize sdkman``` 

Sourcing of this script had to be inside .zshrc (was in .bashrc but I was using zsh)

https://github.com/sdkman/sdkman-cli/issues/568






