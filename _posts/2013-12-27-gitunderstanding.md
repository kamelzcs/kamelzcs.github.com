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

#### What is in is snapshots

The most obvious feature in git owns all the snapshots of the system, while most other VCS system 
only stores the differences. 

![alt normal](/images/post/git/VCS.png)
Figure 1-1. VCS usually only stores the differences.

![alt normal](/images/post/git/Git.png)
Figure 1-2. Git stores the snapshots.

In order to get data efficienty, the Git compresses each 'original' file and calculate its SHA-1  
hash, then use a tree structure to store the whole infomition hierarchically.





