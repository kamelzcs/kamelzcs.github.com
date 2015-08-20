---
layout: post
title: Folds and monoids
date: 2015-08-20 16:32:54
isOriginal: true
category: Functional Programming
tags:
 - Haskell
 - CIS194
keywords: 
description:  Folds and monoids
---

#### Lecture Contents

To understand the different relations between type classes, [Type class][1] is a great reference.

This is a [fantastic picture][2].

#### Home Work

##### Exercise 1

{%highlight haskell%}
(+++) :: Monoid m => JoinList m a -> JoinList m a -> JoinList m a
l1 +++ l2 = Append (tag l1 `mappend` tag l2) l1 l2
{%endhighlight%}

##### Exercise 2

{%highlight haskell%}
-- 1. Implement the function indexJ to find the JoinList element at
-- the specified index; it should satisfy the equivalence:
--     (indexJ i jl) == (jlToList jl !!? i)

indexJ :: (Sized b, Monoid b) => Int -> JoinList b a -> Maybe a
indexJ _ Empty = Nothing
indexJ i _ | i < 0 = Nothing
indexJ i (Single _ _) | i > 0 = Nothing
indexJ _ (Single _ a) = Just a
indexJ i p@(Append _ l r)
  | i >= sz p = Nothing
  | i < lsize = indexJ i l
  | otherwise = indexJ (i - lsize) r
  where lsize = sz l

-- 2. Implement the function dropJ to drop first n elements of a
-- JoinList; it should satisfy the equivalence:
--     jlToList (dropJ n jl) = drop n (jlToList jl)

dropJ :: (Sized b, Monoid b) => Int -> JoinList b a ->JoinList b a
dropJ _ Empty        = Empty
dropJ n l | n < 0 = l
dropJ _ (Single _ _) = Empty
dropJ n (Append _ l r)
  | n < lsize = (dropJ n l) +++ r
  | otherwise = dropJ (n - lsize) r
  where lsize = sz l

-- 3. Implement the function takeJ to return the first n elements of a
-- JoinList, dropping all other elements; it should satisfy the equivalence:
--     jlToList (takeJ n jl) == take n (jlToList jl)

takeJ :: (Sized b, Monoid b) => Int -> JoinList b a ->JoinList b a
takeJ _ Empty          = Empty
takeJ n _  | n < 0    = Empty
takeJ n j | n + 1 >= sz j = j
takeJ n (Append _ l r)
  | n < lsize = takeJ n l
  | otherwise = l +++ takeJ (n - lsize) r
  where lsize = sz l
{%endhighlight%}

##### Exercise 3


{%highlight haskell%}
module Scrabble where

import Data.Monoid

data Score = Score Int
             deriving (Eq, Show)

instance Monoid Score where
  mempty = Score 0
  Score a `mappend` Score b = Score (a+b)

score :: Char -> Score
score c
  | c `elem` "aeilnorstuAEILNORSTU" = Score 1
  | c `elem` "dgDG"                 = Score 2
  | c `elem` "bcmpBCMP"             = Score 3
  | c `elem` "fhvwyFHVWY"           = Score 4
  | c `elem` "kK"                   = Score 5
  | c `elem` "jxJX"                 = Score 8
  | c `elem` "qzQZ"                 = Score 10
  | otherwise                       = Score 0

scoreString :: String -> Score
scoreString = foldr ((<>).score) $ Score 0
{%endhighlight%}

Test code:

{%highlight haskell%}
*JoinList> scoreLine "yay " +++ scoreLine "haskell!"
Append (Score 23) (Single (Score 9) "yay ") (Single (Score 14) "haskell!")
{%endhighlight%}

##### Exercise 4

The magical part is `(Sized b, Monoid b)` restriction to `Sized (a,b)`.

To make it valid, besides the auto implemented

{%highlight haskell%}
instance (Monoid a, Monoid b) => Monoid (a,b) where
mempty = (mempty, mempty)
mappend (a1,b1) (a2,b2) = (mappend a1 a2, mappend b1 b2)
{%endhighlight%}

The functions in `Sized.hs` is also critical.

{%highlight haskell%}
instance Sized Size where
  size = id

-- This instance means that things like
--   (Foo, Size)
--   (Foo, (Bar, Size))
--   ...
-- are all instances of Sized.
instance Sized b => Sized (a,b) where
  size = size . snd

instance Monoid Size where
  mempty  = Size 0
  mappend = (+)
{%endhighlight%}


Thanks to these functions, the previously implemented `indexJ`, `dropJ`, `takeJ` are still valid to the `(Score, Size)`

{%highlight haskell%}
type JLBuffer = JoinList (Score, Size) String

instance Buffer JLBuffer where

  -- toString :: JLBuffer -> String
  toString = concat . jlToList

  -- fromString :: String -> JLBuffer
  fromString = foldr1 (+++) . map(\x -> Single (scoreString x, Size 1) x) . lines

  -- line :: Int -> JLBuffer -> Maybe String
  line = indexJ

  -- replaceLine :: Int -> String -> JLBuffer -> JLBuffer
  replaceLine n str jlb =
    takeJ (n - 1) jlb +++ Single (scoreString str, Size 1) str +++ dropJ n jlb

  -- numLines :: JLBuffer -> Int
  numLines = sz

  -- value :: JLBuffer -> Int
  value = scorev . fst . tag
          where scorev (Score i) = i

-- JLBuffer based editor

main :: IO()
main = runEditor editor jlb
  where jlb = fromString $ unlines
         [ "This buffer is for notes you don't want to save, and for"
         , "evaluation of steam valve coefficients."
         , "To load a different file, type the character L followed"
         , "by the name of the file."
         ] :: JLBuffer
{%endhighlight%}

[1]: https://wiki.haskell.org/Typeclassopedia
[2]: https://wiki.haskell.org/wikiupload/d/df/Typeclassopedia-diagram.png
