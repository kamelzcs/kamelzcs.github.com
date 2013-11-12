---
layout: post
title: Modeling-Relationship-in-App-Engine
date: 2013-11-11 12:15:27
isOriginal: true
category: Technique
tags:
 - Technique
 - Datastore
 - Relationships
 - App Engine
 - Study note
keywords: 
description:  relationships in app engine
---
The origin article is in [relationship][1].

### The nature of relationships

There are many relationships, such as one to one, one to many and many to many. But the they are all built on the similar idea: reference.
A reference is a field of an entity that contains the key of another entity.


### Relationships in App Engine

#### One to many

In app engine, it is accomplished by storing the key of one side in the entity of many side.

{% highlight python %}
class Owner(db.Model):
  name = db.StringProperty()

class Pet(db.Model):
  name = db.StringProperty()
  owner = db.ReferenceProperty(Owner)
pets = Pet.all().filter('owner =', owner).fetch(100)
{% endhighlight  %}

To fetch all the pets owned by the owner, the query can be in this way.

{%highlight python %}
pets = Pet.all().filter('owner =', owner).fetch(100)
pets = owner.pet_set.fetch(100)
{%endhighlight%}

To change the default name to our own name, code can be in the following way.

{% highlight python %}
class Pet(db.Model):
  name = db.StringProperty()
  owner = db.ReferenceProperty(Owner, collection_name='pets')

pets = owner.pets.fetch(100)
{%endhighlight%}

Another way to model the one to many relationships is to use parent entities.
Every entity can specify a parent when it is created. The key of the parent forms part of the key of the created entity, and cannot be modified. The code is like the following:
{%highlight python%}
class Owner(db.Model):
  name = db.StringProperty()

class Pet(db.Model):
  name = db.StringProperty()

bob = Owner(name='Bob')
felix = Pet(name='Felix', parent=bob)

owner_of_felix = felix.parent
{%endhighlight%}

The relationship is specified implicitly. It is the most import case for transactions: on app engine, transactions can only be operate on the same entity group. A entity group is the set with same parent.

### One to one
A special case of one to many.

### Many to many

The most obvious approach is the same as used in relationship databases: a join table. 
{%highlight python%}
class Owner(db.Model):
  name = db.StringProperty()

class Pet(db.Model):
  name = db.StringProperty()

class PetOwner(db.Model):
  pet = db.ReferenceProperty(Pet, collection_name='owners')
  owner = db.ReferenceProperty(Owner, collection_name='pets')
{%endhighlight%}

Using the referenceproperty, the code is like the following:

{%highlight python%}
petowners = felix.owners.fetch(100)
prefetch_refprops(owners, 'owner')
owners = [x.owner for x in petowners]
{%endhighlight%}
Another approach is to have one side of the relationship store a list of the keys of entities on the other side of the relationship.
This makes sense when the cardinality on one side is limited.
{%highlight python%}
class Pet(db.Model):
  name = db.StringProperty()

class Owner(db.Model):
  name = db.StringProperty()
  pets = db.ListProperty(db.Key)

pets = db.get(bob.pets)
owners = Owner.all().filter('pets =', felix).fetch(100)
{%endhighlight%}

[1]: http://blog.notdot.net/2010/10/Modeling-relationships-in-App-Engine  "relationship"

