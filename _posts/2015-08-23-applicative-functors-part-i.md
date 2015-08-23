---
layout: post
title: Applicative functors, Part I
date: 2015-08-23 10:40:32
isOriginal: true
category: Functional Programming
tags:
 - Haskell
 - CIS194
keywords: 
description: Applicative functors, Part I
---

#### Lecture Contents

The induction from `Functor` to `Applicative` is quite beautiful.

Recalled that there exists `fmap :: (a -> b) -> (f a -> f b)` in `Functor`, it would be great if this mapping
function is still valid if the input consists of more than one variable, namely `(a -> b -> c) -> (f a -> f b -> f c)`

We have

{%highlight haskell%}
h  :: a -> b -> c
fa :: f a
fb :: f b
fc :: f c
{%endhighlight%}

Use `fmap` to lift functions,
{%highlight haskell%}
h         :: a -> (b -> c)
fmap h    :: f a -> f (b -> c)
fmap h fa :: f (b -> c)
{%endhighlight%}

But there is no way to make

{%highlight haskell%}
f (b -> c) -> fb -> fc
{%endhighlight%}

`Applicative` has the `(<*>)` to implement it, namely

{%highlight haskell%}
(<*>) :: f (a -> b) -> f a -> f b
{%endhighlight%}

The final induction is

{%highlight haskell%}
liftA2 :: Applicative f => (a -> b -> c) -> f a -> f b -> f c
liftA2 = h <$> fa <*> fb
{%endhighlight%}

#### Home Work

##### Exercise 1

{%highlight haskell%}
-- Ex. 1 - implement a Functor instance for Parser
--
-- You may find it useful to implement:
-- first :: (a -> b) -> (a,c) -> (b,c)

first :: (a -> b) -> (a, c) -> (b, c)
first f (x, y) = (f x, y)

instance Functor Parser where
  fmap f parser = Parser $ \s -> first f <$> runParser parser s
{%endhighlight%}

##### Exercise 2

{%highlight haskell%}
-- Ex. 2 - implement an Applicative instance for Parser
--
instance Applicative Parser where
  pure a = Parser (\s -> Just (a, s))

  p1 <*> p2 = Parser parse
    where parse s = do
          r1 <- runParser p1 s
          r2 <- runParser p2 (snd r1)
          return (fst r1 $ fst r2, snd r2)
{%endhighlight%}

##### Exercise 3

{%highlight haskell%}
-- Ex. 3a - Create a parser:
--
--   abParser :: Parser (Char, Char)
--
-- which expects to see the characters ’a’ and ’b’ and returns them as a pair

abParser :: Parser (Char, Char)
abParser = (,) <$> char 'a' <*> char 'b'


-- Ex. 3b - Create a parser:
--
--   abParser_ :: Parser ()
--
-- which acts in the same way as abParser but returns () instead of 'a' and 'b'

abParser_ :: Parser ()
abParser_ = const . const () <$> char 'a' <*> char 'b'

-- Ex. 3c - Create a parser:
--
--   intPair 
--
-- which reads two integer values separated by a space and returns the integer 
-- values in a list. You should use the provided posInt to parse the integer values.

intPair :: Parser [Integer]
intPair = (\x  y -> [x, y]) <$> posInt <* char ' ' <*> posInt
{%endhighlight%}

##### Exercise 4

{%highlight haskell%}
instance Alternative Parser where
  empty = Parser $ const Nothing
  Parser p1 <|> Parser p2 = Parser $ liftA2 (<|>) p1 p2
{%endhighlight%}

##### Exercise 5

{%highlight haskell%}
-- Ex. 5 - Implement a parser:
--
--  intOrUppercase :: Parser ()
-- 
-- which parses either an integer value or an uppercase character, and fails otherwise.

intOrUppercase :: Parser ()
intOrUppercase = const () <$> posInt <|> const () <$> satisfy isUpper
{%endhighlight%}
