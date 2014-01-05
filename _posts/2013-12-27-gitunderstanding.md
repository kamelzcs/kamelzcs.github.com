---
layout: post
title: Understanding Git
date: 2013-12-27 19:20:24
isOriginal: true
category: Technology
tags:
 - Git
keywords: Git 
description: Understanding Git
---

#### What is in Git is snapshots

The most obvious feature in Git owns all the snapshots of the system, while most other VCS system 
only stores the differences. 

![alt normal](/images/posts/git/VCS.png)

Figure 1-1. VCS usually only stores the differences.

![alt normal](/images/posts/git/Git.png)

Figure 1-2. Git stores the snapshots.

In order to get data efficienty, the Git compresses each 'original' file and calculate its SHA-1  
hash, then use a tree structure to store the whole infomition hierarchically.

#### The three states in Git

In Git, there are three states: the working directory, staging area and Git directory. 


![alt normal](/images/posts/git/gitstates.png)

Figure 1-3. Git states

The basic working flows is as follows:

* Making changes in working directory.

* Stage the useful changes and add their snapshots to the staging area.

* Commit the staging area to the Git directory, which will store the snapshots of the staging area permunantly.

#### How the Git store the snapshots

##### The Git tree object

The Git store thing similar to the Unix system: the tree object corresponds to the directory entity while the blog corresponds to the 
inode or file.

![alt normal](/images/posts/git/object.png)

Figure 1-4. Tree object

![alt normal](/images/posts/git/contentobject.png)

Figure 1-5. Content structure of the tree object.

![alt normal](/images/posts/git/singlecommit.png)

Figure 1-6. Single commit in a compatible way.

##### The Git directory

The Git tree structure is as follows:

![alt normal](/images/posts/git/submit.png)

Figure 1-7. Git directory

![alt normal](/images/posts/git/multiplecommit.png)

Figure 1-8. Git directory in a compatible way.

#### The Git branch

The Git branch is a pointer to a commit, the HEAD is the pointer to your current branch.

![alt normal](/images/posts/git/branch.png)

Figure 1-9. Git branch

![alt normal](/images/posts/git/head.png)

Figure 1-10. Git head.

#### Reference

[1] [Visual git tutorial](http://marklodato.github.io/visual-git-guide/index-en.html)

[2]  [Git tutorial](https://github.s3.amazonaws.com/media/progit.en.pdf)











