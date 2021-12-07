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
## Q: What is the read depth at position 10001994? (Rather than scrolling to the position, use the substring searching capabilities of less : press / , then type 10001994 and enter to find the position.)
66
## Q: What is the reference base? Are there any non-reference bases?
Yes G
This output can be used for a simple consensus calling. For a more sophisticated variant calling method,
go to the next section.

## Exercise 3: Generating genotype likelihoods and variant calling
The mpileup command (traditionally in samtools , now moved to bcftools ) can be used to generate genotype
likelihoods. Try to run the following command (press " q " to quit the viewing mode)
```
bcftools mpileup -f GRCm38_68.19.fa A_J.bam | less -S
```
This generates an intermediate output which contains genotype likelihoods and other raw information
necessary for calling. This output is usually streamed directly to the caller like this
bcftools mpileup -f GRCm38_68.19.fa A_J.bam | bcftools call -m | less -S
## Q: What option should be added to only print out variant sites? Hint: check the program usage by running bcftools call without any parameters.
```

The INFO and FORMAT fields of each entry tells us something about the data at the position in the genome. It
consists of a set of key-value pairs with the tags being explained in the header of the VCF file (see the ##INFO
and ##FORMAT lines in the header)

