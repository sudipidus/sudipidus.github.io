---
date: "2019-09-10"
layout: post
title: How git stores changes 
tags: [git,linux,zlib]
---

Git stores snapshots of the directory content at the moment. (Contrast older VCS systems which just capture the changelog) If files haven't changed it just points to the previous version.



We can check the content of the commit blob. It's stored in .git/objects. It's stored as a compressed zlib file.

```sh
~/projects/my-project/.git/objects/1a(my-branch) » pigz -d < 58989906551da9cd7f2395c640e0b90667aa27
commit 677tree f1c83252e98f54138359308ef247193bf4e0bef4
parent 1747b1d081167971b9612ad75862943e8a9f344c
author Mister Author <mister-author@host.com> 1567673031 +0000
committer Mister Author <mister-author@host.com> 1567673031 +0000

Merged in feature/somebranch (pull request #426)

blah  blah message

```

pigz is the unix utility to view the content of the file.

Branches are stored inside .git/refs/heads which just contain the commit id.

These objects can belong to either category:

   1. blob — A blob object is used for storing the contents of a single file.

   2. tree — A tree object contains references to other blobs or subtrees.

   3. commit — A commit object contains the reference to another tree object and some other information(author, committer etc.)

   4. tag — A tag or a tag object is just another reference to a commit object and just makes for easier referencing.
