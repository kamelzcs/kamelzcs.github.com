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

Here is a useful tutorial for Git:[Visual git guide][1]

* git status 

    Check the status of the files. 

* gitk & git diff 

    show the branch in graphical mode.

    check the difference.

* git rm

    remove files
    
* git log -p -2

    show the changing between the past two submitions

* git commit --amend

    replace the last submit

* git reset HEAD 

    unstage files

* git checkout \<file\>- 

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

   * git rebase -i \<the SHA-1 of unchanged commit\>

      merge multiple commits
      pick = use the commit
      squash = use commit, but merge into previous commit

* git diff \<branch\> \<branch\>
    
    compare the difference between two branches

* git stash save 'comment'

    save the changes temporary

    * git stash list
        
        view the stash list

    * git stash apply stash@{2}

        apply the specified stash.    

    * git stash apply --index

        reapply the staged changes

    * git stash drop 

        remote from the stash list

    * git stash pop

        apply the stash and immediately drop it from the stack. Be causious.

    * git stash branch

        creating a branch from a stash

[1]:http://marklodato.github.io/visual-git-guide/index-en.html#reset
