---
layout: post
title: One C++ polymorphism quesion
date: 2015-05-24 10:58:27
isOriginal: true
category: C/C++
tags:
 - C++
 - Polymorphism
keywords: C++, Polymorphism, compiler
description: The polymorphism in C++
---

### Confusing subtype polymorphism

When I first began to learn the polymorphism of C++, the `Subtype Polymorphism` similar to the following confused me a lot.

{%highlight c++%}

//file cats.h
class Felid {
public:
 virtual void meow() = 0;
 void getName(){ std::cout << "This is the Felid\n\n"; }
};

class Cat : public Felid {
public:
 void meow() { std::cout << "Meowing like a regular cat! meow!\n"; }
 void getName(){ std::cout << "This is the Cat\n\n"; }
};

class Tiger : public Felid {
public:
 void meow() { std::cout << "Meowing like a tiger! MREOWWW!\n"; }
 void getName(){ std::cout << "This is the Tiger\n\n"; }
};

class Ocelot : public Felid {
public:
 void meow() { std::cout << "Meowing like an ocelot! mews!\n"; }
 void getName(){ std::cout << "This is the Ocelot\n\n"; }
};

{%endhighlight%}

Now the programme can uses different `subtypes` to call `do_meowing`.

{%highlight c++%}

#include <iostream>
#include "cats.h"

void do_meowing(Felid *cat) {
 cat->meow();
 cat->getName();
}

int main() {
 Cat cat;
 Tiger tiger;
 Ocelot ocelot;

 do_meowing(&cat);
 do_meowing(&tiger);
 do_meowing(&ocelot);
}

{%endhighlight%}

The output is:

    Meowing like a regular cat! meow!
    This is the Felid
    
    Meowing like a tiger! MREOWWW!
    This is the Felid
    
    Meowing like an ocelot! mews!
    This is the Felid

So, only the `virtual function` is polymorphism, but why and how?

### polymorphisms in C++

There are [four polymorphisms in C++][1], namely

1. Subtype Polymorphism (Runtime Polymorphism)
2. Parametric Polymorphism (Compile-Time Polymorphism)
3. Ad-hoc Polymorphism (Overloading)
4. Coercion Polymorphism (Casting)

`virtual function` belongs to the `subtype polymorphism` category.

### subtype polymorphism implementation

Each object owns [several fields][2], including

1. data member
2. inherited data member
3. virtual table

`subtype polymorphism` in `C++` is implemented by `virtual table` variable.

Also note that, member function is not stored in the object level, but in the class info level.
So normal member function is only determined by its declared type, not polymorphismed.


[1]:http://www.catonmat.net/blog/cpp-polymorphism/
[2]:http://web.stanford.edu/class/archive/cs/cs143/cs143.1128/lectures/12/Slides12.pdf
