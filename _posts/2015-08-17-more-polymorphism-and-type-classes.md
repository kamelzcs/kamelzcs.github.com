---
layout: post
title: More polymorphism and type classes
date: 2015-08-17 16:01:33
isOriginal: true
category: Functional Programming
tags:
 - Haskell
 - CIS194
keywords: 
description: More polymorphism and type classes
---

#### Lecture Contents

{%highlight haskell%}
f :: a -> a -> a
f x y = x && y
{%endhighlight%}

>The reason this doesn’t work is that the caller of a polymorphic function gets to choose the type. Here we, the implementors, have tried to choose a specific type


{%highlight haskell%}
a -> a -> a
{%endhighlight%}
>could be understood as a promise that a function with this type will work no matter what type the caller chooses

##### Type classes
>Intuitively, type classes correspond to sets of types which have certain operations defined for them, and type class polymorphic functions work only for types which are instances of the type class(es) in question

#### Home Work

##### Exercise 1

{%highlight haskell%}
eval :: ExprT -> Integer
eval (Lit i) = i
eval (Add e1 e2) = eval e1 + eval e2
eval (Mul e1 e2) = eval e1 * eval e2
{%endhighlight%}

##### Exercise 2

{%highlight haskell%}
evalStr :: String -> Maybe Integer
evalStr = fmap eval . parseExp Lit Add Mul
{%endhighlight%}

##### Exercise 3

Defination for `Expr`
{%highlight haskell%}
module Expr where

class Expr a where
  lit :: Integer -> a
  add :: a -> a -> a
  mul :: a -> a -> a
{%endhighlight%}

{%highlight haskell%}
instance Expr ExprT where
  lit = Lit
  add = Add
  mul = Mul

reify :: ExprT -> ExprT
reify = id
{%endhighlight%}

##### Exercise 4

{%highlight haskell%}
instance Expr Integer where
  lit = id
  add = (+)
  mul = (*)

instance Expr Bool where
  lit i
    | i <= 0 = False
    | i > 0  = True
  add = (||)
  mul = (&&)

newtype MinMax = MinMax Integer
                 deriving (Eq, Show)

instance Expr MinMax where
  lit = MinMax
  add (MinMax x) (MinMax y) = MinMax (max x y)
  mul (MinMax x) (MinMax y) = MinMax (min x y)

newtype Mod7 = Mod7 Integer
                 deriving (Eq, Show)

instance Expr Mod7 where
  lit x = Mod7 (x `mod` 7)
  add (Mod7 x) (Mod7 y) = Mod7 ((x+y) `mod` 7)
  mul (Mod7 x) (Mod7 y) = Mod7 ((x*y) `mod` 7)
{%endhighlight%}

##### Exercise 5

{%highlight haskell%}
instance Expr Program where
  lit x = [PushI x]
  add x y = x ++ y ++ [Add]
  mul x y = x ++ y ++ [Mul]
{%endhighlight%}

##### Exercise 6

There are two tricky parts from my view:

1. Same expression can belong to multiple types,
both `add (lit 3) (var "x") :: (M.Map String Integer -> Maybe Integer)` and `add (lit 3) (var "x") :: VarExprT` are valid

2. `add e1 e2 m` has the type of `(M.Map String Integer -> Maybe Integer) -> (M.Map String Integer -> Maybe Integer) -> (M.Map String Integer -> Maybe Integer)`

{%highlight haskell%}
data VarExprT = Lit Integer
              | Add VarExprT VarExprT
              | Mul VarExprT VarExprT
              | Var String
                deriving (Eq, Show)

instance Expr VarExprT where
  lit = Lit
  add = Add
  mul = Mul

class HasVars a where
  var :: String -> a

instance HasVars VarExprT where
  var = Var

instance HasVars (M.Map String Integer -> Maybe Integer) where
  var = M.lookup

instance Expr (M.Map String Integer -> Maybe Integer) where
    lit =  const . Just
    add e1 e2 m = (+) <$> e1 m <*> e2 m
    mul e1 e2 m = (*) <$> e1 m <*> e2 m

withVars :: [(String, Integer)]
         -> (M.Map String Integer -> Maybe Integer)
         -> Maybe Integer
withVars vs exp = exp $ M.fromList vs
{%endhighlight%}
