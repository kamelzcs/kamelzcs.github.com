---
layout: post
title: Bash Command Line Processing
date: 2014-01-16 18:46:17
isOriginal: true
category: Computer
tags:
 - Bash
keywords: Bash 
description: The command Line Processing of Bash
---

## Command Line Processing

Each line the shell get is called a *pipeline*, it contains commands which are seperated by the pipe character(|).
For each pipeline it gets, the shell will breaks it into commands, set up the I/O for the pipeline and follows the
following figure to excecute the commands.

![alt text][commandline]

1. Split the command into tokens by the metacharacters set: `SPACE, TAB, NEWLINE, ;, (, ), <, >, |, and &.`
Types of tokens include: `words, keywords, I/O redirectors, and semicolons.`

2. Checks the first token of each command to see if it is a keyword with no quotes or backslashes. If it is an opening keyword, 
such as `if` or orther control-structure openers, then the shell sets things up internally for compound commands, reads the next command,
and start process again.

3. Checks the first word of each command against the list of aliases. If a match is found, it substitudes and go back to Step 1.
This scheme allows recursive aliases.

4. Performs brace expansion. For example, `a{b,c}` becomes `ab` `ac`.

5. Substitudes the user's home directory(`$HOME`) for tilde if it is at the beginning of a word. 

6. Performs parameter(variable) substitution for any expression that starts with a dollar sign(`$`).

7. Does command substitution for any expression of the form $(string).

8. Evaluates arithmetic expressions of the form $((string)).

9. Takes the parts of the line that resulted from parameter, command, and arithmetic substitution and splits them into words again.
This time it uses the characters in `$IFS` which usually is whitespace (space, tab, and newline).

10. Performs pathname expansion, a.k.a. wildcard expansion, for any occurrences of `*` `?` and `[/]` pairs.

11. Uses the first word as a command by looking up its source as a function command, then as a built-in, then as a file in any of the directories
in `$PATH`

12. Runs the command after setting up I/O redirection and other such things.

[commandline]: /images/posts/bash-commandline/bash.jpg

### Reference
[1] [Learning the bash shell](http://shop.oreilly.com/product/9780596009656.do)

