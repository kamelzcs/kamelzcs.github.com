---
layout: post
title: Lazy evaluation
date: 2015-08-18 11:18:10
isOriginal: true
category: Functional Programming
tags:
 - Haskell
 - CIS194
keywords: 
description: Lazy evaluation
---

#### Lecture Contents

`Lazy evaluation` is beautiful because it makes infinite data structures possible.
But it makes reasoning about the programme harder, especially when mixed with `side effects`.

{%highlight haskell%}
import Data.Array

knapsack01 :: [Double]   -- values 
           -> [Integer]  -- nonnegative weights
           -> Integer    -- knapsack size
           -> Double     -- max possible value
knapsack01 vs ws maxW = m!(numItems-1, maxW)
  where numItems = length vs
        m = array ((-1,0), (numItems-1, maxW)) $
              [((-1,w), 0) | w <- [0 .. maxW]] ++
              [((i,0), 0) | i <- [0 .. numItems-1]] ++
              [((i,w), best) 
                  | i <- [0 .. numItems-1]
                  , w <- [1 .. maxW]
                  , let best
                          | ws!!i > w  = m!(i-1, w)
                          | otherwise = max (m!(i-1, w)) 
                                            (m!(i-1, w - ws!!i) + vs!!i)
              ]

example = knapsack01 [3,4,5,8,10] [2,3,4,5,9] 20
{%endhighlight%}

The functional dynamic programming implementation is kind of tricky in my view.

Monolithic [Arrays][1] is used to memorized the overlapped subprobramme.

#### Home Work

##### Exercise 1

{%highlight haskell%}
fib :: Integer -> Integer
fib n
  | n == 0 = 0
  | n == 1 = 1
  | otherwise = fib (n - 1) + fib (n - 2)

fibs1 :: [Integer]
fibs1 = map fib [0..]
{%endhighlight%}

##### Exercise 2

{%highlight haskell%}
fibs2 :: [Integer]
fibs2 = 0 : 1 : zipWith (+) fibs2 (tail fibs2)
{%endhighlight%}

##### Exercise 3

`cons` is mentioned in the lecture, but use record syntax makes it
much more cleaner.

{%highlight haskell%}
data Stream a = Stream { streamToList :: [a] }

instance (Show a) => Show (Stream a) where
  show =  show . take 20 . streamToList
{%endhighlight%}

##### Exercise 4

{%highlight haskell%}
streamRepeat :: a -> Stream a
streamRepeat = Stream . repeat

streamMap :: (a -> b) -> Stream a -> Stream b
streamMap f = Stream . map f . streamToList

streamFromSeed :: (a -> a) -> a -> Stream a
streamFromSeed f = Stream . iterate f
{%endhighlight%}

##### Exercise 5

[Explanation][2]

{%highlight haskell%}
nats :: Stream Integer
nats = Stream [0..]

interleaveStreams :: Stream a -> Stream a -> Stream a
interleaveStreams (Stream (x:xs)) s =
  Stream $ x : streamToList (interleaveStreams s (Stream xs))


ruler :: Stream Integer
ruler = foldr1 interleaveStreams (map streamRepeat [0..])
{%endhighlight%}

[1]: https://www.haskell.org/tutorial/arrays.html
[2]:http://codereview.stackexchange.com/questions/66700/implementing-function-that-maps-over-stream
