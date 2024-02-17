---
layout: post
title: Using taskwarrior to stay disciplined with side projects
tags: [productivity,engineering,bash,shell-scripting]
date:  "2024-02-17"
---

## Taskwarrior
Taskwarrior is an OSS CLI (to make it fancier, let's call it TUI - terminal UI) based task manager. I have been using it for sometime and it works great for my use case.

[My Earlier Post on setting GUI notifications](https://sudipidus.github.io/posts/2020-02-04-task-warrior-reminder/)


I wanted some discipline in progressing with my side projects most of which are on github and here's what I did:

- set up a script which overrides task warrior's data directory to current directory if it's a git repository, this is so that I can segregate my todo tasks

- set up another script (alias rather) which spits the pending task whenever I cd into it


### What it looks like

![task-warrior-demo](https://raw.githubusercontent.com/sudipidus/sudipidus.github.io/main/resources/taskwarrior.png)


### Here is the script and alias
```bash
#task warrior
task_git() {
    if [ -d .git ]; then
        export TASKDATA="$(pwd)"
    else
        export TASKDATA="~/.task"
    fi
    task "$@"
    unset TASKDATA
}


# invoke task list in valid git repo
cd() {
    builtin cd "$@"
    
    if [ -d .git ]; then
        task list
    fi
}

```

PS: As a dutiful programmer I spend more time tinkering around tooling and productivity instead of actually doing something. lol