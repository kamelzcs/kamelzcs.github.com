---
layout: post
title: Life of a Datastore Write
date: 2013-11-06 18:02:45
isOriginal: true
category: Technology
tags:
 - App engine
 - Technology
 - Database
keywords: 
description:  Datastore writing process
---

### An Insert

When we call put or makePersistent, several things happen behind the scenes before the call returns and sets the entity's key:

1. The my\_todo object is converted into a protocol buffer.
2. The appserver makes an RPC call to the datastore server, sending the entity data in a protocol buffer.
3. If a key name is not provided, a unique ID is determined for this entity's key. The entity key is composed of app ID | ancestor keys | kind name | key name or ID
4. The datastore server processes the request in two phases that are executed in order: commit, then apply. In each phase, the datastore server identifies the Bigtable tablet servers that should receive the data.

### The Commit Phase

The datastore performs two actions in the commit phase:

1. It writes the data for the entities to the entity group's log.
2. It marks the new log entry as committed.

### The Apply Phase

In the apply phase, the entity's data and the index rows are written to disk in parallel.

### The Return Value

Using HRD, the Datastore returns after the commit and does the apply asynchronously.


