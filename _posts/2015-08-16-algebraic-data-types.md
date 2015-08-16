---
layout: post
title: Algebraic Data Types
date: 2015-08-16 14:42:59
isOriginal: true
category: Functional Programming
tags:
 - Haskell
 - CIS194
keywords: 
description:  Algebraic Data Types
---

#### Lecture Content

##### Algebraic data types in general

{%highlight haskell%}
data AlgDataType = Constr1 Type11 Type12
                 | Constr2 Type21
                 | Constr3 Type31 Type32 Type33
                 | Constr4
{%endhighlight%}

This is the general `Algebraic data types` definition.

One important note:

- type and data constructor names must always start with a capital letter
- variables(including names of functions) must always start with a lowercase letter

#### Home Work

##### Exercise 1

{%highlight haskell%}
parseMessage :: String -> LogMessage
parseMessage s = case words s of
  ("I":t:xs) -> LogMessage Info (read t) (unwords xs)
  ("W":t:xs) -> LogMessage Warning (read t) (unwords xs)
  ("E":c:t:xs) -> LogMessage (Error $ read c) (read t) (unwords xs)
  _ -> Unknown s

parse :: String -> [LogMessage]
parse = map parseMessage . lines
{%endhighlight%}

##### Exercise 2

{%highlight haskell%}
insert :: LogMessage -> MessageTree -> MessageTree
insert (Unknown _) m = m
insert m Leaf = Node Leaf m Leaf
insert m (Node l lg r)
  | mT < logT = Node (insert m l) lg r
  | otherwise = Node l lg (insert m r)
  where
    getTimeStamp x = case x of (LogMessage _ t _)-> t
                               _ -> 0
    mT = getTimeStamp m
    logT = getTimeStamp lg
{%endhighlight%}

##### Exercise 3

{%highlight haskell%}
build :: [LogMessage] -> MessageTree
build = foldr insert Leaf
{%endhighlight%}

##### Exercise 4

{%highlight haskell%}
inOrder :: MessageTree -> [LogMessage]
inOrder Leaf = []
inOrder (Node l lg r) = inOrder l ++ [lg] ++ inOrder r
{%endhighlight%}


##### Exercise 5

{%highlight haskell%}
whatWentWrong :: [LogMessage] -> [String]
whatWentWrong = map getMsg . filter seri . inOrder . build
  where seri (LogMessage (Error e) _ _) = e >= 50
        seri _ = False
        getMsg (LogMessage _ _ m) = m
{%endhighlight%}

Test code:

    *LogAnalysis> testWhatWentWrong parse whatWentWrong "sample.log" 
    ["Way too many pickles","Bad pickle-flange interaction detected","Flange failed!"]
