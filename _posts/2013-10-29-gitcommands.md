---
layout: post
title: Git Commands 
date: 2013-10-29 11:31:03
isOriginal: true
category: Technique 
tags:
 - Git 
 - Technique
keywords: The useful git commands 
description: 
---

* git status 

    Check the status of the files. 

* gitk & git diff 

    show the branch in graphical mode.

    check the difference.

* git rm

    remove files
    
* git log p -2

    show the changing between the past two submitions

* git commit --amend

    replace the last submit

* git reset HEAD 

    unstage files

* git checkout -- 

    unmodifying a modified file, revert it back to what it looked like when last committed.

* git remote -v

    shows the URL for the shorname to be expaned to

* git remote show origin

    list the more infomation of the remote

* git push origin local-branch-name:remote-branch-name

    push to specilized banch 
    
* git fetch origin & git checkout -b test origin/test

    fetch all the remote branches and checkout the specific branch

* git branch -a

    show the branch info in remote and local

* git checkout --track 

    set the track branch  

* git rebase 

    take all the changes that were commited on one branch and replay on another one.

* git diff branch branch
    
    compare the difference between two branches

