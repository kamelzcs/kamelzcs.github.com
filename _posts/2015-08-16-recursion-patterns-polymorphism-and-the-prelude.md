---
layout: post
title: Recursion patterns, polymorphism, and the Prelude
date: 2015-08-16 16:13:17
isOriginal: true
category: Functional Programming
tags:
 - Haskell
 - CIS194
keywords: 
description: Recursion patterns, polymorphism, and the Prelude
---

#### Lecture Content

    In fact, experienced Haskell programmers hardly ever write recursive functions!

This is quite unbelieable for me at first glance. Even though `recursive function` is not
the best practice in languages which does not provide [tail call optimization][1].

`Recursive function` provide more abstract way than `iterative function`, while there exists
more abstract way than `Recursive function`, which leave the low-level details of actually doing recursion
to these functions.

##### Infix Function

Backticks can turn any standard function with two argumetns in an infix operator which is called [section syntax][2]:

    (op e)  =   \ x -> x op e
    (e op)  =   \ x -> e op x

For example,


{%highlight haskell%}
elem 2 [1, 2, 3] == (2 `elem`) [1, 2, 3] == ((`elem` [1, 2, 3]) 2)
{%endhighlight%}

#### Exercise 1 Hopscotch

`zip` or `zipwith` are convenient to do filter in list.

{%highlight haskell%}
skips :: [a] -> [[a]]
skips xs =
        let ns = [1..length xs]
            extractEvery m = map snd . filter (\x -> (fst x `mod` m) == 0) . zip ns
            in map (`extractEvery` xs) ns
{%endhighlight%}

#### Exercise 2 Local maxima

{%highlight haskell%}
localMaxima :: [Integer] -> [Integer]
localMaxima (a:b:c:xs)
    | b > a && b > c = b: p
    | otherwise = p
    where
        p = localMaxima(b:c:xs)
localMaxima _ = []
{%endhighlight%}

#### Exercise 3 Histogram

{%highlight haskell%}
histogram:: [Integer] -> String
histogram xs =
        let count = map (\n -> length $ filter (== n) xs) [0..9]
            maxi = maximum count
            histo m (base, c) = show base ++ "=" ++
                replicate c '*' ++
                replicate (m - c) ' '
        in
           unlines . reverse . transpose . map (histo maxi) $ zip [0..9] count
{%endhighlight%}

Test code,

{%highlight haskell%}
*Golf> putStr $ histogram [1,1,1,5]
 *        
 *        
 *   *    
==========
0123456789
{%endhighlight%}

[1]: http://stackoverflow.com/questions/310974/what-is-tail-call-optimization
[2]: http://www.haskell.org/onlinereport/haskell2010/haskellch3.html#x8-300003.5
