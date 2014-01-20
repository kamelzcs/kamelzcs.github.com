---
layout: post
title: Quoting and eval in Bash
date: 2014-01-20 10:15:24
isOriginal: true
category: Linux
tags:
 - Bash
keywords: Bash Quoting Linux Eval
description: 
---

### Quoting and eval in Bash

#### Quoting

Quoting in Bash actually skips some steps in the previous post:

1. Sinle quotes(`''`) bypass through the Step 10. Everything inside the Single quotes remains untouched.
There can't be single quotes inside single quotes, even if bashslashes are used.

2. Double quotes(`""`) bypass Step 1 through 4, plus steps 9 and 10. Single quotes inside double quotes
has no effect. But doulbe quotes allow for parameter substitution, command substitution and arithmetic expression 
evaluation. A double quote can be inside another double quote by a backslash.

#### Eval

Eval lets the precess being precessed once more. It tells the shell to take the **eval**'s arguments and run them through the command-line
precessing steps all over again.


