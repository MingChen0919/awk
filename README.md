# AWK
AWK is a very powerful tool for text processing. This tutorial will teach you how to use AWK in just a few minutes! 

The **example.txt[./example.txt]** file will be the input file used in examples in this tutorial.

## The basic AWK Grammar

> `awk` '**lines_selector** `{expression 1; expression 2; ...}`' **file(s)**

Awk processes input files on a line-by-line basis. You can think of awk as a `map` function, which **applies a list of
expressions to selected lines**. Therefore, you first need to specify a **line_selector** statement to select some lines
from the input file, then you can write expressions to sequentially manipulate those lines.

**Select fasta file URIs in the example.txt**

We will extract the following text content from our example.txt file.

```
file:/data/local/ref/GATK/human_g1k_v37.fasta
file:/data/local/ref/GATK/human_g1k_v37.fasta
file:/data/local/ref/GATK/human_g1k_v37.fasta
```

* Command: `awk '/UR.+fasta/ {gsub("UR:file:", "", $5); print $5}' example.txt`
* Explain:
    + **lines_select**: `/UR.+fasta/`, selecting lines using regex pattern `UR.+fasta`
    + `{gsub("UR:file:", "", $5); print $5}` - a list of expressions sequentially operating on the above selected lines
        - **expression 1**: `gsub("UR:file:", "", $5)` - replacing `UR:file:` in 5th field of each line with empty
        - **expression 2**: `print $5` - only print the 5th field of each manipulated line in **expression 1**.


  