# AWK
AWK is a very powerful tool for text processing. This tutorial will teach you how to use AWK in just a few minutes! 

The **example.txt[./example.txt]** file will be the input file used in examples in this tutorial.

## The basic AWK Grammar

> `awk` '**lines_selector** `{expression 1; expression 2; ...}`' **file(s)**

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
    + **lines_select**: `/UR.+fasta/`, selecting lines using regex pattern `UR.+fasta`
    + `{gsub("UR:file:", "", $5); print $5}` - a list of expressions sequentially operating on the above selected lines
        - **expression 1**: `gsub("UR:file:", "", $5)` - replacing `UR:file:` in 5th field of each line with empty
        - **expression 2**: `print $5` - only print the 5th field of each manipulated line in **expression 1**.


## Lines Selecting Examples

* select between line 3 and line 5: 
    + `awk 'NR==3, NR==5 {print}' example.txt`, or
    + `awk 'NR>=3 && NR<=5 {print}' example.txt`

``` 
@HD	VN:1.0	SO:coordinate
@SQ	SN:1	LN:249250621	AS:NCBI37	UR:file:/data/local/ref/GATK/human_g1k_v37.fasta	M5:1b22b98cdeb4a9304cb5d48026a85128
@SQ	SN:2	LN:243199373	AS:NCBI37	UR:file:/data/local/ref/GATK/human_g1k_v37.fasta	M5:a0d9851da00400dec1098a9255ac712e
```

* select lines 3, 5, and 7
    + `awk 'NR==3 || NR==6 || NR==9 {print}' example.txt`
    
``` 
@HD	VN:1.0	SO:coordinate
@SQ	SN:3	LN:198022430	AS:NCBI37	UR:file:/data/local/ref/GATK/human_g1k_v37.fasta	M5:fdfd811849cc2fadebc929bb925902e5
@PG	ID:bwa	VN:0.5.4
```

* select between two lines using regex patterns:
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


