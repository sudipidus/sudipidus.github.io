---
layout: post
title: A case of slow loading zsh
tags: [engineering,zsh,linux,automation]
---



I have been using zsh (Z-shell) for a long time. Recently it came to my attention that opening a new terminal was taking about 2-3 seconds which is quite a long time. 

Just to rule out some other reasons, I switched my default shell to bash and saw if it was still slower and turned out that was not the case.

### Changing default shell
```shell
chsh -s /bin/bash
```

Normally all shells load up some configuration, aliases, etc while opening a session, .zshrc for zshell, .bashrc for bash and so on. I thoroughly had a look at my configuration file and nothing major seemed to be the issue. I removed some unused aliases and some functions which I was no longer using but that didn't solve the latency.

Finally I added profiling to my zshell config file, (thanks it existed)

#### profiling zsh configuration

```shell
zmodload zsh/zprof #beginning of the config

alias
export
#usual stuff
zprof #end of config
```

This gave me a nice little breakdown of what functions or initializations inside my config were taking time.

Some notable observations from the profiling lead me to these lines:

```shell

num  calls                time                       self            name
-----------------------------------------------------------------------------------
 1)    1        1508.66  1508.66   92.62%    869.50   869.50   53.38%  nvm_auto
 2)    2         639.16   319.58   39.24%    354.75   177.38   21.78%  nvm
 3)    1         250.18   250.18   15.36%    217.83   217.83   13.37%  nvm_ensure_version_installed

 1        1508.69  1508.69   92.63%      0.03     0.03    0.00%  nvm_process_parameters
       1/1      1508.66  1508.66   92.62%    869.50   869.50             nvm_auto [1]

-----------------------------------------------------------------------------------

       1/1      1508.66  1508.66   92.62%    869.50   869.50             nvm_process_parameters [26]
 1)    1        1508.66  1508.66   92.62%    869.50   869.50   53.38%  nvm_auto
       1/2       639.16   639.16   39.24%     12.68    12.68             nvm [2]

-----------------------------------------------------------------------------------

       1/2       639.16   639.16   39.24%     12.68    12.68             nvm_auto [1]
       1/2       626.48   626.48   38.46%    342.08   342.08             nvm [2]

 ```


Ah the culprit.

So I removed nvm config from my zshell for the time being and my shell loads instantly. 

And I am looking for an alternative node version manager or a way to do it using dotfile. I don't use node that often anyway, so works for me!!
