---
layout: post
title: Applicative functors, Part II
date: 2015-08-23 15:25:09
isOriginal: true
category: Functional Programming
tags:
 - Haskell
 - CIS194
keywords: 
description: Functional Programming
---

#### Lecture Contents

The classification about `Levels of Abstraction` is really great.

>With respect to Applicative and Monad in particular, there are just two levels to be concerned with. The first is the level of implementing various Applicative and Monad instances, i.e. the “raw Haskell” level.
>Once we have an Applicative instance for a type like Parser, the point is that we get to “move up a layer” and program with Parsers using the Applicative interface, without thinking about the details of how Parser and its Applicative instance are actually implemented.

The powerful `Type system` of `Haskell` makes it really suitable to create `Levels of Abstraction`,
which is usually very verbose or even impossible in other static typed programming languages.


#### Home Work

##### Exercise 1

{%highlight haskell%}

zeroOrMore :: Parser a -> Parser [a]
zeroOrMore p = oneOrMore p <|> pure []

oneOrMore :: Parser a -> Parser [a]
oneOrMore p = (:) <$> p <*> zeroOrMore p
{%endhighlight%}
##### Exercise 2

{%highlight haskell%}
spaces :: Parser String
spaces = zeroOrMore $ satisfy isSpace

ident :: Parser String
ident = (:) <$> (satisfy isAlpha) <*> zeroOrMore (satisfy isAlphaNum)
{%endhighlight%}

##### Exercise 3

{%highlight haskell%}
-- An "identifier" is represented as just a String; however, only
-- those Strings consisting of a letter followed by any number of
-- letters and digits are valid identifiers.
type Ident = String

-- An "atom" is either an integer value or an identifier.
data Atom = N Integer | I Ident
  deriving Show

-- An S-expression is either an atom, or a list of S-expressions.
data SExpr = A Atom
           | Comb [SExpr]
  deriving Show

parseAtom :: Parser Atom
parseAtom = spaces *> (N <$> posInt <|> I <$> ident) <* spaces

parseComb :: Parser SExpr
parseComb = spaces *> (Comb <$> (char '(' *> oneOrMore parseSExpr <* char ')')) <* spaces

parseSExpr :: Parser SExpr
parseSExpr = (A <$> parseAtom) <|> parseComb
{%endhighlight%}

Test:

{%highlight haskell%}
*SExpr> runParser parseSExpr "(   lots  of   (  spaces   in  )  this ( one ) )"
Just (Comb [A (I "lots"),A (I "of"),Comb [A (I "spaces"),A (I "in")],A (I "this"),Comb [A (I "one")]],"")
{%endhighlight%}
