---
layout: post
title: Tracking File permissions in git
tag: [git, tags, software, linux]
---

## Git tracks file permissions too

Although I have been extensively using git for quite some time now I never happened to come across this situation where you change the permission of the file which is tracked by git. Yes, by default git is configured to track the changes in file permission mode too.

Just to experiment with the idea, I created a dummy repo and 'touched' an empty file. The initial default permission being: 775

I added the file to staging and changed the permission bit to 777. Git diffing at this point yields an output of following sort:

```bash
old mode 100644
new mode 100755
```

That perfectly reflects the fact that for this particular file, octal permission codes have changed and git has detected that as a change.

You can commit this changed file and it will be tracked by git for the change in permission. You can roll back to previous commit and the previous permissions will be restored.


However, if you don't want git to detect file permission changes, which is most likely the case (IMO) as you probably don't want to scroll through all the files to find out which ones were actually edited/changed, you can disable the default config by issuing:

```git config core.filemode false
```


Here's a [link](https://stackoverflow.com/questions/1257592/how-do-i-remove-files-saying-old-mode-100755-new-mode-100644-from-unstaged-cha) to a Stackoverflow question discussing about this issue. To quote one of the comments to understand why git is designed that way:

>> This means that git thinks that it can correctly set the executable bit on checked out files, but when it attempts to do so it doesn't work (or at least not in a way that it can read). When it then reads back the status of those files it looks like the executable bit has been deliberately unset. Setting core.filemode to false tells git to ignore any executable bit changes on the filesystem so it won't view this as a change. If you do need to stage an executable bit change it does mean that you have to manually do ```git update-index --chmod=(+|-)x <path>```
>>>




