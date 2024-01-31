---
date: "2018-06-21"
layout: post
title: SDKMAN, Java9, Jshell, REPL
tags: [java, skdman, java9, jshell, REPL, scripting]
---

**Working with multiple versions**

Most of the times we as developers are working on multiple version of the language or the framework. If not managed properly it can be a nightmare dealing with different projects which use different versions of the same language. Say, project A uses python2 and project B uses python3. In python we have virtual environments which can be managed using, among many others, ```pipenv```.  Likewise for Ruby we have ```rbenv```. 

There are quite a few languages that run on JVM(Java Virtual Machine). Java, Groovy, Scala being the most prominent ones. SDKMAN! is a tool that facilitates maintaining different versions of JVM based languages.


**Installing SDKMAN**

```sh
curl -s "https://get.sdkman.io" | bash
source "$HOME/.sdkman/bin/sdkman-init.sh" #activating initialization shell script
```
After Installation we can see the list of all the supported languages and versions using: 
```sh
sdk list
```
Likewise to view versions of a particular sdk we can use:
```sh
sdk list java
```
Replace java with another sdk such as groovy, scala, ant for related information. As always you can explore options by issuing ```sdk --help```

All of the installations are stored inside ```$SDKMAN_DIR/candidates``` from where we can select the default version ```sdk default $language $version``` or even select one for just the current terminal session ```sdk use $language $version```.

Now that we have a very easy package manager for JVM languages lets go about installing java-9 so that we can use Jshell which is the REPL (Read, evaluate, print, repeat) feature to interact with JVM. No need to write a beautiful class with main object just so you can experiment with some esoteric String method.

**Finding what java versions are supported**

```sh
sdk list java
```
```sh
================================================================================
Available Java Versions
================================================================================
 > * 9.0.7-zulu                                                                    
     9.0.4-openjdk                                                                 
     8.0.172-zulu                                                                  
     8.0.171-oracle                                                                
     7.0.181-zulu                                                                  
     6.0.107-zulu                                                                  
     10.0.1-zulu                                                                   
     10.0.1-oracle                                                                 
     10.0.0-openjdk                                                                
     1.0.0-rc1-graal                                                               
                                                                                   
                                                                                   
                                                                                   
                                                                                   
                                                                                   

================================================================================
+ - local version
* - installed
> - currently in use
```

For our example let's install zulu java-9.

```sh
sdk install java 9.0.7-zulu
```
If you don't want to mess around with your system, don't make it as the default one yet. Now java-9 can be found under ```~/.sdkman/candidates/java.``` Jshell resides inside bin directory along with other java programs like ```java``` ```javac``` ```javadoc```.

Make it executable by issuing: ```sudo chmod +x jshell```

For ease of use we can create a symlink to this executable so that we can invoke it from anywhere in the system.

```sh
sudo ln -s ~/.sdkman/candidates/java/9.0.7-zulu/bin/jshell  /usr/local/bin/jshell
```

Now we are ready to launch jshell and explore all the REPL beauty that it brings along. 

```sh
jshell
```

You can now [get your hands dirty](https://docs.oracle.com/javase/9/tools/jshell.htm#JSWOR-GUID-C337353B-074A-431C-993F-60C226163F00)
