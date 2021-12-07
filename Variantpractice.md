For this lab, some pre-prepared datasets are installed on the VM. Double-click on the module icon, it will open
up a terminal in the directory with the data for the module.
Exercise 1: Making sense of the input data
We will need the aligned sequences in SAM, BAM or CRAM format and the reference genome in fasta format.
To list the files in the current directory, typesamtools stats -r GRCm38_68.19.fa A_J.bam > A_J.stats
```
samtools stats -r GRCm38_68.19.fa NZO.bam > NZO.stats
```
```
plot-bamstats -r GRCm38_68.19.fa.gc -p A_J.graphs/ A_J.stats
```
```
plot-bamstats -r GRCm38_68.19.fa.gc -p NZO.graphs/ NZO.stats
```
## Q: Open the plots A_J.graphs/index.html and NZO.graphs/index.html in your firefox browser (e.g.,firefox A_J.graphs/index.html ). What is the percentage of mapped reads in both strains? Checkthe insert size, GC content, per-base sequence content and quality per cycle graphs. Do all look
reasonable?
 ```
 
firefox A_J.graphs/index.html
```
mapped: 	54,940,802 	(98.7%)

```
NZO.graphs/index.html
```

mapped: 	651,795 	(99.7%)

The command samtools mpileup prints the read bases that align to each position in the reference genome.
On the command line, try this:
```
samtools mpileup -f GRCm38_68.19.fa A_J.bam | less -S
```
Each line corresponds to a position on the genome. The columns are: chromosome, position, reference base,
read depth, read bases ("." or "," indicates match on the forward or reverse strand; ACGTN and acgtn a
mismatch on the forward and the reverse strand) and the final column is the base qualities encoded into
characters. The symbol "^" marks the start of a read, "$" the end of a read, deleted bases are represented as
