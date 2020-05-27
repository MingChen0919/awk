# AWK
AWK is a very powerful tool for text processing. This tutorial will teach you how to use AWK in just a few minutes.

## The basic AWK Grammar

> `awk` **lines selector** `{expression 1; expression 2; ...}` **file(s)**

Awk processes input files on a line-by-line basis. You can think of awk as a `map` function, which **applies a list of
expressions to selected lines**. Therefore, you first need to specify a **line_selector** statement to select some lines
from the input file, then you can write expressions to sequentially manipulate those lines.


  