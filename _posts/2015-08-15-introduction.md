---
layout: post
title: Introduction
date: 2015-08-15 13:16:12
isOriginal: true
category: Functional Programming
tags:
 - Haskell
 - CIS194
keywords: 
description: Introduction part of CIS194
---

#### Haskell Basics

Among all of them, `Pure` is the most tricky part. In the current computer system model,
`side effects` are inevitable. Haskell uses `Modad` and treats the outside world as static
to implement `Pure`.

#### Home Work

##### Exercise 1

{%highlight haskell%}
toDigits :: Integer -> [Integer]
toDigits n
  | n <= 0 = []
  | otherwise = toDigits (n `div` 10) ++ [n `mod` 10]

toDigitsRev :: Integer -> [Integer]
toDigitsRev = reverse . toDigits
{%endhighlight%}

Test part,
{%highlight haskell%}
ex1Tests :: [Test]
ex1Tests = [ testF1 "toDigits test" toDigits
             [(1234, [1, 2, 3, 4]), (5, [5]), (0, []), (-17, [])]
           ]
{%endhighlight%}

##### Exercise 2

The key lies in generating infinite lists of `[1, 2, 1, 2, ...]`
There are several ways to generate infinite lists.

{%highlight haskell%}
numbers1 = 1 : map (+1) numbers1
numbers2 = 1 : [x+1 | x <- numbers2]
numbers3 = [1..]
{%endhighlight%}

The way to get infinite interchange of `1 2` lists is

{%highlight haskell%}
  oneTwo = 1 : 2 : oneTwo
{%endhighlight%}

{%highlight haskell%}
doubleEveryOther :: [Integer] -> [Integer]
doubleEveryOther = reverse . zipWith (*) oneTwo . reverse where
  oneTwo = 1 : 2 : oneTwo
{%endhighlight%}

Test code

{%highlight haskell%}
ex2Tests :: [Test]
ex2Tests = [testF1 "doubleEveryOther test" doubleEveryOther
             [([8, 7, 6, 5], [16, 7, 12, 5]), ([1, 2, 3], [1, 4, 3])]
           ]
{%endhighlight%}

##### Exercise 3

{%highlight haskell%}
sumDigits :: [Integer] -> Integer
sumDigits = sum . map (sum. toDigits)
{%endhighlight%}

{%highlight haskell%}
ex3Tests :: [Test]
ex3Tests = [testF1 "sumDigits test" sumDigits
             [([16, 7, 12, 5], 22)]
           ]
{%endhighlight%}

##### Exercise 4

{%highlight haskell%}
validate :: Integer -> Bool
validate = (== 0) . (`mod` 10) . sumDigits . doubleEveryOther . toDigits
{%endhighlight%}


{%highlight haskell%}
ex4Tests :: [Test]
ex4Tests = [testF1 "validate test" validate
             [(4012888888881881, True), (4012888888881882, False)]
           ]
{%endhighlight%}

##### Exercise 5

{%highlight haskell%}
type Peg = String
type Move = (Peg, Peg)

hanoi :: Integer -> Peg -> Peg -> Peg -> [Move]
hanoi n a b c
  | n <= 0 = []
  | n == 1 = [(a, b)]
  | otherwise = hanoi (n - 1) a c b ++ [(a, b)] ++ hanoi (n - 1) c b a
{%endhighlight%}
