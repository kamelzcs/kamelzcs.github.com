---
layout: post
title: Higher-order programming and type inference
date: 2015-08-17 10:46:29
isOriginal: true
category: Functional Programming
tags:
 - Haskell
 - CIS194
keywords: 
description: Higher-order programming and type inference
---

#### Lecture Contents
>There is an art to deciding the order of arguments to a function to make partial applications of it as useful as possible: the arguments should be ordered from from “least to greatest variation”, that is, arguments which will often be the same should be listed first, and arguments which will often be different should come last.

The only reason why this ordering makes sense is the last one could be made point free when it is in need.

One thing confuses me a lot is the difference between `$` and `.` when doing the function composition.

[stackoverflow][1] gives a great answer. To summarize,

- `$` operator is for avoiding parenthesis.Anything appearing after it will take precedence over anything that comes before.
- `.` operator is for chaining functions. It works like the pipeline, the output on the right goes to the input of the left.


{%highlight haskell%}
putStrLn (show (1 + 1)) == putStrLn $ show $ 1 + 1 == putStrLn . show $ 1 + 1
{%endhighlight%}

#### Exercise

##### Exercise 1 Wholemeal programming

{%highlight haskell%}
fun1 :: [Integer] -> Integer
fun1 = foldr (\x y -> if even x then (x - 2) * y else y) 1

fun2:: Integer -> Integer
fun2 = sum . filter even . takeWhile (> 1) . iterate (\n -> if even n then n `div` 2 else 3 * n + 1)
{%endhighlight%}

##### Exercise 2 Folding with trees

Always try to insert into the left subtree while keep it is balanced.

{%highlight haskell%}
data Tree a = Leaf | Node Integer (Tree a) a (Tree a)
            deriving Show

foldTree :: [a] -> Tree a
foldTree xs = foldr insert Leaf xs
    where
        height Leaf = -1
        height (Node h _ _ _) = h
        count Leaf = 0
        count (Node _ l _ r) = 1 + count l + count r
        insert x Leaf = Node 0 Leaf x Leaf
        insert x (Node h l d r)
            | height l > height r = Node h l d $ insert x r
            | count l > count r   = Node h l d (insert x r)
            | otherwise           = let newl = insert x l
                                        newHeight = (1 + (height newl))
                                        in Node newHeight newl d r
{%endhighlight%}

##### Exercise 3 More folds

{%highlight haskell%}
xor :: [Bool] -> Bool
xor = foldr (\x accum -> if x then not accum else accum) False

map' :: (a -> b) -> [a] -> [b]
map' f = foldr (\x accum -> f x : accum) []
{%endhighlight%}

##### Exercise 4 Finding primes

{%highlight haskell%}
sSundDelete :: Int -> [Int]
sSundDelete n = [i+j+2*i*j|i<-[1..n], j<-[i..n]]

sieveSundaram :: Int -> [Int]
sieveSundaram n = let del = sSundDelete n in
     [2*x+1 | x <- [1..n], not (x `elem` del)]
{%endhighlight%}
[1]: http://stackoverflow.com/questions/940382/haskell-difference-between-dot-and-dollar-sign
