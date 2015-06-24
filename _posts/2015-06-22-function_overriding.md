---
layout: post
title: Function_overriding in Java vs C++
date: 2015-06-22 08:27:24
isOriginal: true
category: Programming
tags:
 - Java
 - C++
keywords: Polymorphism
description: Function overriding in Java and C++
---

### Similar code, different behaviour

Quesion in [stackoverflow][1]:

#### Java version:

{%highlight java%}
class base{
    public void func1(){
        func2();
    }
    public void func2(){
        System.out.println(" I am in base:func2() \n");
    }

}

class derived extends base{
    public void func1(){
        super.func1();
    }
    public void func2(){
        System.out.println(" I am in derived:func2() \n");
    }
};
public class Test
{
    public static void main(String[] args){
        derived d = new derived();
        d.func1();
    }
}
{%endhighlight%}

Output:
>I am in derived:func2()

#### C++ version

{%highlight c++%}
#include <stdio.h>

class base
{
    public:
        void func1(){
            func2();
        }
        void func2(){
            printf(" I am in base:func2() \n");
        }
};

class derived : public base
{
    public:
        void func1(){
            base::func1();
        }
        void func2(){
            printf(" I am in derived:func2() \n");
        }
};

int main()
{
    derived *d = new derived();
    d->func1();
    return 0;
}
{% endhighlight %}

Output:
>I am in base:func2()

### Why

Firstly, I thought the behaviour of `C++` is reasonable, `Java` version is hard to understand, as I am more used to `static binding`.

For both languages, variable binding is `static`, no matter it is member variable or not.

But for `method binding`, two languages behaviour differently.

In C++, unless this method is virtual, otherwise it is bind staticlly default.

but in Java, all method bindings use late binding unless it is `static` or `final`(`private` is implicly final).

Java version behaves as if you had declared `base::func2` as

{%highlight c++%}
virtual void func2(){
    printf(" I am in base:func2() \n");
}
{%endhighlight%}

### Static Binding vs Late Binding

`static binding` means references are resovled at `compile time`, namely it is possible to resolve it by its `position`.

`late binding` means references are resolved at `run time`, which is implemented through `Vtable` in C++.



[1]:http://stackoverflow.com/questions/30883519/function-overriding-in-java-vs-c
