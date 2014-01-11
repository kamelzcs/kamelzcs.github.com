---
layout: post
title: Transaction Isolation in App Engine
date: 2013-11-06 18:26:26
isOriginal: true
category: Technology 
tags:
 - App Engine
 - Database
keywords: 
description:  Transaction in App Engine
---

### Inside Transactions: Serializable

From strongest to weakest the four isolation levels are  Serializable, Repeatable Read, Read Committed, and Read Uncommitted. Transactions are separated within each other. Transactions on a given entity group are executed serially.

Within the transaction, the datastore is consistent.

### The Commit Process

The process is as below:

![alt normal](/images/posts/transaction-isolation-in-app-engine/1.1.png )


