---
layout: post
title: Modeling-entity-relationships
date: 2013-11-07 19:01:35
isOriginal: true
category:  Technique
tags:
 - App Engine
 - Database
keywords: 
description: Entity  relationships in App Engine
---

### One to Many

{% highlight python %}
class Contact(db.Model):

    # Basic info.
    name = db.StringProperty()
    birth_day = db.DateProperty()

    # Address info.
    address = db.PostalAddressProperty()

    # The original phone_number property has been replaced by
    # an implicitly created property called 'phone_numbers'.

    # Company info.
    company_title = db.StringProperty()
    company_name = db.StringProperty()
    company_description = db.StringProperty()
    company_address = db.PostalAddressProperty()

class PhoneNumber(db.Model):
    contact = db.ReferenceProperty(Contact,
                                   collection_name='phone_numbers')
    phone_type = db.StringProperty(
        choices=('home', 'work', 'fax', 'mobile', 'other'))
    number = db.PhoneNumberProperty()
{% endhighlight  %}

Everytime a reference property is created, an implicit collection property on the referenced class is created. The default name can be over-rode using collection_name. 

Now the relationship can be created in the following way.
{% highlight python %}
scott = Contact(name='Scott')
scott.put()
PhoneNumber(contact=scott,
            phone_type='home',
            number='(650) 555 - 2200').put()
PhoneNumber(contact=scott,
            phone_type='mobile',
            number='(650) 555 - 2201').put()
{% endhighlight  %}

Now  the phone numbers for a give person can be retrieved in this way.

{% highlight python %}
print 'Content-Type: text/html'
print
for phone in scott.phone_numbers:
    print '%s: %s' % (phone.phone_type, phone.number)
{% endhighlight  %}

### Many to many

{% highlight python %}
class Group(db.Model):

  name = db.StringProperty()
  description = db.TextProperty()

class Contact(db.Model):
  # ID of user that owns this entry.
  owner = db.StringProperty()

  # Basic info.
  name = db.StringProperty()
  birth_day = db.DateProperty()

  # Address info.
  address = db.PostalAddressProperty()

  # Company info.
  company_title = db.StringProperty()
  company_name = db.StringProperty()
  company_description = db.StringProperty()
  company_address = db.PostalAddressProperty()

  # Group affiliation
  groups = db.ListProperty(db.Key)

friends = Group.gql("WHERE name = 'friends'").get()
mary = Contact.gql("WHERE name = 'Mary'").get()

if friends.key() not in mary.groups:
   mary.groups.append(friends.key())
   mary.put()

class Group(db.Model):
   name = db.StringProperty()
   description = db.TextProperty()

   @property
   def members(self):
      return Contact.gql("WHERE groups = :1", self.key())
{% endhighlight  %}

The limitations:

1. Must explicitly retrieve the values on the side of the collection where the list is stored since all you have available are Key objects
2. Avoid storing overly large lists of keys in a ListProperty

### Relationship Model

{% highlight python %}
class Contact(db.Model):
    # ID of user that owns this entry.
    owner = db.StringProperty()

    # Basic info.
    name = db.StringProperty()
    birth_day = db.DateProperty()

    # Address info.
    address = db.PostalAddressProperty()

    # The original organization properties have been replaced by
    # an implicitly created property called 'companies'. 

    # Group affiliation
    groups = db.ListProperty(db.Key)

class Company(db.Model):
    name = db.StringProperty()
    description = db.StringProperty()
    company_address = db.PostalAddressProperty()

class ContactCompany(db.Model):
    contact = db.ReferenceProperty(Contact,
                                   required=True,
                                   collection_name='companies')
    company = db.ReferenceProperty(Company,
                                   required=True,
                                   collection_name='contacts')
    title = db.StringProperty()

mary = Contact.gql("name = 'Mary'").get()
google = Company.gql("name = 'Google'").get()
ContactCompany(contact=mary,
               company=google,
               title='Engineer').put()
{% endhighlight  %}

The advantage compared with list-of-keys is can have large collections on either side of the relationship. However, the traversing the connectionso of a collection will require more calls to teh databases.


