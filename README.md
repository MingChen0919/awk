# AWK
AWK is a very powerful tool for text processing. This tutorial will teach you how to use AWK in just a few minutes! 

The **example.txt[./example.txt]** file will be the input file used in examples in this tutorial.

## The AWK command template

> `awk` '**lines_selector** `{expression 1; expression 2; ...}`' **file(s)**

The above awk command template shows you the very basic components of an awk command line: lines_selector + a list of `;`
separated expressions within a pair of `{}`.  

Awk processes input files on a line-by-line basis. You can think of awk as a `map` function, which **applies a list of
expressions to selected lines**. Therefore, you first need to specify a **line_selector** statement to select some lines
from the input file, then you can write expressions to sequentially manipulate those lines.

### Example

**Select fasta file URIs in the example.txt**

We will extract the following text content from our example.txt file.

```
file:/data/local/ref/GATK/human_g1k_v37.fasta
file:/data/local/ref/GATK/human_g1k_v37.fasta
file:/data/local/ref/GATK/human_g1k_v37.fasta
```

* Command: `awk '/UR.+fasta/ {gsub("UR:file:", "", $5); print $5}' example.txt`
* Explain:
    + **lines_selector**: `/UR.+fasta/`, selecting lines using regex pattern `UR.+fasta`
    + `{gsub("UR:file:", "", $5); print $5}` - a list of expressions sequentially operating on the above selected lines
        - **expression 1**: `gsub("UR:file:", "", $5)` - replacing `UR:file:` in 5th field of each line with empty
        - **expression 2**: `print $5` - only print the 5th field of each manipulated line in **expression 1**.


## Lines Selecting Examples

* **select all lines**
    + The default **lines_selector** is to select all lines. If this is what you want, you can remove **lines_selector**
    from your command line
    + The command `awk {print} examples.txt` will select all lines and print

* **select between line 3 and line 5:** 
    + `awk 'NR==3, NR==5 {print}' example.txt`, or
    + `awk 'NR>=3 && NR<=5 {print}' example.txt`

``` 
@HD	VN:1.0	SO:coordinate
@SQ	SN:1	LN:249250621	AS:NCBI37	UR:file:/data/local/ref/GATK/human_g1k_v37.fasta	M5:1b22b98cdeb4a9304cb5d48026a85128
@SQ	SN:2	LN:243199373	AS:NCBI37	UR:file:/data/local/ref/GATK/human_g1k_v37.fasta	M5:a0d9851da00400dec1098a9255ac712e
```

* **select lines 3, 5, and 7**
    + `awk 'NR==3 || NR==6 || NR==9 {print}' example.txt`
    
``` 
@HD	VN:1.0	SO:coordinate
@SQ	SN:3	LN:198022430	AS:NCBI37	UR:file:/data/local/ref/GATK/human_g1k_v37.fasta	M5:fdfd811849cc2fadebc929bb925902e5
@PG	ID:bwa	VN:0.5.4
```

* **select between two lines using regex patterns:**
    + `awk '/Example Header Lines/, /In the alignment examples/ {print}' example.txt`
    
``` 
Example Header Lines
@HD	VN:1.0	SO:coordinate
@SQ	SN:1	LN:249250621	AS:NCBI37	UR:file:/data/local/ref/GATK/human_g1k_v37.fasta	M5:1b22b98cdeb4a9304cb5d48026a85128
@SQ	SN:2	LN:243199373	AS:NCBI37	UR:file:/data/local/ref/GATK/human_g1k_v37.fasta	M5:a0d9851da00400dec1098a9255ac712e
@SQ	SN:3	LN:198022430	AS:NCBI37	UR:file:/data/local/ref/GATK/human_g1k_v37.fasta	M5:fdfd811849cc2fadebc929bb925902e5
@RG	ID:UM0098:1	PL:ILLUMINA	PU:HWUSI-EAS1707-615LHAAXX-L001	LB:80	DT:2010-05-05T20:00:00-0400	SM:SD37743	CN:UMCORE
@RG	ID:UM0098:2	PL:ILLUMINA	PU:HWUSI-EAS1707-615LHAAXX-L002	LB:80	DT:2010-05-05T20:00:00-0400	SM:SD37743	CN:UMCORE
@PG	ID:bwa	VN:0.5.4
@PG	ID:GATK TableRecalibration	VN:1.0.3471	CL:Covariates=[ReadGroupCovariate, QualityScoreCovariate, CycleCovariate, DinucCovariate, TileCovariate], default_read_group=null, default_platform=null, force_read_group=null, force_platform=null, solid_recal_mode=SET_Q_ZERO, window_size_nqs=5, homopolymer_nback=7, exception_if_no_tile=false, ignore_nocall_colorspace=false, pQ=5, maxQ=40, smoothing=1
In the alignment examples below, you will see that the 2nd alignment maps back to the RG line with ID UM0098.1, and all of the alignments point back to the SQ line with SN:1 because their RNAME is 1.
```

## Multiple `lines_selector + {}`s

We can use multiple `lines_selector + {}`s to process lines differently and print them all together. See an example:

> `awk '/^@SQ/{gsub("UR:", "", $5); print $5} /^@SQ/{print $6}' example.txt`

Output from the above command line is:

``` 
file:/data/local/ref/GATK/human_g1k_v37.fasta
M5:1b22b98cdeb4a9304cb5d48026a85128
file:/data/local/ref/GATK/human_g1k_v37.fasta
M5:a0d9851da00400dec1098a9255ac712e
file:/data/local/ref/GATK/human_g1k_v37.fasta
M5:fdfd811849cc2fadebc929bb925902e5
```

In this command line, we used two `lines_selectors + {}`s:

* 1st `lines_selectors + {}`: `/^@SQ/{gsub("UR:", "", $5); print $5}`
    + select rows starting with `@SQ`; replace `UR:` with empty in field 5; print field 5
* 2st `lines_selectors + {}`: `/^@SQ/{print $6}`
    + select rows starting with `@SQ`; print field 6
    
## Special `lines_selectors`: `BEGIN` and `END`

You can think of `BEGIN` and `END` as special `lines_selector`s:

+ `BEGIN`: select line 0, which is a line that does not exist, which also means you can add texts to this line and it 
will show at the beginning of your output.
+ `END`: select the line after the last line of your input file. If you add some text to this line, it will show at the
end of your final output.

`BEGIN` and `END` are also the places where you modify built-in variable values. 

### Example

>`awk 'BEGIN{print "SN\tLN\tAS\tUR\tM5"}   /^@SQ/{for (i=1; i<=NF; i++) gsub(".+\:", "", $i); gsub($1, ""); print}' example.txt`

``` 
SN	LN	AS	UR	M5
 1 249250621 NCBI37 /data/local/ref/GATK/human_g1k_v37.fasta 1b22b98cdeb4a9304cb5d48026a85128
 2 243199373 NCBI37 /data/local/ref/GATK/human_g1k_v37.fasta a0d9851da00400dec1098a9255ac712e
 3 198022430 NCBI37 /data/local/ref/GATK/human_g1k_v37.fasta fdfd811849cc2fadebc929bb925902e5
```

## What's next?

Now you know the lego building skills, the next step would be to find your lego piecies used by awk. 
[Here](awk_cheatsheets.pdf) is an awk cheatsheet that I use often.



