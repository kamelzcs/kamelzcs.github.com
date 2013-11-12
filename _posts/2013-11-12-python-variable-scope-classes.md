---
layout: post
title: Python variable scope
date: 2013-11-12 15:07:37
isOriginal: true
category: Technique
tags:
 - Python
 - Variable scope
keywords: 
description: about the python variable scope in classes
---
### About variable scope

In python, the variable scope has four kinds:

* global
* class
* instance
* local

In those kinds, the class-based and instance-based may merge in some cases: 

Every time, the instance-based is looked up, which has not been initialized, the class-based result will
be returned if there is any. Once it is initialized, it will be in the field of the instance.

One example from [stackoverflow][1]:
{%highlight python%}
class MyClass(object):
    my_var = 10

    def __init__(self):
        print(self.my_var)


m1 = MyClass()
>>> 10          # class-based 

MyClass.my_var = 20
print(m1.my_var)
>>> 20          # Only class-based available.

m2 = MyClass()
>>> 20          # class-based

m1.my_var = 30
print(MyClass.my_var)
>>> 20          # class-based,but the instance-based variable has been set in m1.

MyClass.my_var = 40 
print(m1.my_var)
>>> 30           
                 # The value WAS shared before m1.my_var = 30 had happened.

print(m2.my_var)
>>> 40           # m2.my_var's value is still shared 

{%endhighlight%}

[1]:http://stackoverflow.com/questions/12941748/python-variable-scope-and-classes




