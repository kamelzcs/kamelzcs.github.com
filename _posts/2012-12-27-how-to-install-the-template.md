---
layout: post
title: 如何使用模板
date: 2012-12-27 21:44:30
isOriginal: true
category: jekyll
tags:
 - Jekyll
 - Git
 - Github
keywords: jekyll git github
description: 使用模板的方法
---

## 1 介绍 ##

在github上搭建一个中小型网站，可以充分利用github提供的托管功能，并且不用担心代码丢失的问题，是一件很惬意的事情。

## 2 安装 ##

基本参照<http://kamelzcs.github.com/2012/08/14/blogging-with-jekyll/>即可。
说明几个需要注意的问题：

1. 建立 username.github.com 这样的repo，这样就可以直接使用这个域名访问网站.
2. \_compiled主要用来中转,将\_site拷贝过来之后，然后将目录切换到 \_compiled，因为 \_compiled 克隆的时候用的是master，所以自动切换到了master分支，然后提交到master分支。这里有个疑问,\_compiled既然克隆出来的时候是在master分支，为什么在source分支下又可以看到这个文件夹，暂时不清楚。
3. git会自动确定切换分支时哪些文件该出现，哪些不应该出现。


2013年10月28日 更新：
jekyll升级到1.2.1 以后，jekyll-pagination会导致jekyll在 build的时候失败，只能将pagination.rb手动更新为最新的代码,同时\__plugin文件夹中不能使用中文。

2015年1月21日 更新：
jekyll升级到2.5.4 以后，jekyll-pagination已经被内置jekyll-paginate代替。



