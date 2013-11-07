---
layout: post
title:  Storing Data in App Engine
date: 2013-11-05 14:59:29
isOriginal: true
category:   technique
tags:
 - technique
 - Database   
 - App Engine
keywords:  Database App-engine
description: learning summarize in storing data in app engine
---
### Storing Data

The app engine provides several ways to store data for the application:

1. App Engine Datastore: A schemaless object datastore with automatic caching, query engine, and atomic transactions.
2. Google Cloud SQL: A relationship SQL based on the MySQL database.
3. Google Cloud Storage: size can be up to terabytes.

### Python Datastore API

An entity has one or more properties. Each entity is identified by its kind, which categorizes the entity for the purpose of queries, and a key that uniquely identifies it within its kind.

{% highlight python %}

import datetime
from google.appengine.ext import db
from google.appengine.api import users


class Employee(db.Model):
  name = db.StringProperty(required=True)
  role = db.StringProperty(required=True,
                           choices=set(["executive", "manager", "producer"]))
  hire_date = db.DateProperty()
  new_hire_training_completed = db.BooleanProperty(indexed=False)
  email = db.StringProperty()


e = Employee(name="John",
             role="manager",
             email=users.get_current_user().email())
e.hire_date = datetime.datetime.now().date()
e.put()

{% endhighlight  %}

Query Examples:

{% highlight python %}

training_registration_list = ["Alfred.Smith@example.com",
                              "jharrison@example.com",
                              "budnelson@example.com"]
employees_trained = db.GqlQuery("SELECT * FROM Employee WHERE email IN :1",
                                training_registration_list)
for e in employees_trained:
  e.new_hire_training_completed = True
  db.put(e)

{% endhighlight  %}

### Comparison with traditional databases

It differs from them in the way it describes relationships between data objects. Entities of the same kind can have different properties, and different entities can have properties with the same name but different value types.

### Entities

Objects in the Datastore are entities.

#### Kinds,keys, and identifiers

Each entity is a particular kinds, which categorizes the entity for the purpose of queries. In addition, each entity has its own key, which uniquely identifies it. The key consists of the following components:

1. The entity's kind
2. identifier, which can be either
    * a key name string
    * an integer ID
3. An optional ancestor path locating the entity within the Datastore hierarchy

#### Ancestor paths

Entities in the Datastore form a hierarchically structured space similar to the directory structure of a file system.

>
    [Person:GreatGrandpa, Person:Grandpa, Person:Dad, Person:Me]
 

### Queries and indexes

A typical query includes the following:

* An entity kind to which the query applies
* Zero or more filters based on the entities' property values, keys, and ancestors
* Zero or more sort orders to sequence the results

App Engine predefines a simple index on each property of an entity. An App Engine application can define further custom indexes in an index configuration file named index.yaml.

### Transactions

Every attempt to insert, update, or delete an entity takes place in the context of a transaction. A single transaction can include any number of such operations. To maintain the consistency of the data, the transaction ensures that all of the operations it contains are applied to the Datastore as a unit or, if any of the operations fails, that none of them are applied.

You can perform multiple actions on an entity within a single transaction.

#### Transactions and entity groups

Only ancestor queries are allowed within a transaction: that is, each transactional query must be limited to a single entity group. The transaction itself can apply to multiple entities, which can belong either to a single entity group or (in the case of a cross-group transaction) to as many as five different entity groups.

#### Cross-group transactions

The transaction can be applied across a maximum of five entity groups, and will succeed as long as no concurrent transaction touches any of the entity groups to which it applies.

As in a single-group transaction, you cannot perform a non-ancestor query in an XG transaction. You can, however, perform ancestor queries on separate entity groups.

### Datastore writes and data visibility

Data is written to the Datastore in two phases:

1. In the Commit phase, the entity data is recorded in a log.
2. The Apply phase consists of two actions performed in parallel:
    * The entity data is written.
    * The index rows for the entity are written. (Note that this can take longer than writing the data itself.)

The write operation returns immediately after the Commit phase and the Apply phase then takes place asynchronously.




