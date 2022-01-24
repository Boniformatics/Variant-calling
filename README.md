# Variant-calling
My variant calling exrercise
For this lab, some pre-prepared datasets are installed on the VM. Double-click on the module icon, it will open
up a terminal in the directory with the data for the module.
Exercise 1: Making sense of the input data
We will need the aligned sequences in SAM, BAM or CRAM format and the reference genome in fasta format.
To list the files in the current directory, type
```
ls -lh
```

## Q: Open the plots A_J.graphs/index.html and NZO.graphs/index.html in your firefox browser (e.g., firefox A_J.graphs/index.html ). What is the percentage of mapped reads in both strains? Check the insert size, GC content, per-base sequence content and quality per cycle graphs. Do all look reasonable?
A_J strain-99.7%
NZO strain-99.7%


The command samtools mpileup prints the read bases that align to each position in the reference genome.
On the command line, try this:
```
samtools mpileup -f GRCm38_68.19.fa A_J.bam | less -S
```
Each line corresponds to a position on the genome. The columns are: **chromosome**, **position**, **reference** **base**,
**read** **depth**, **read** **bases** ("." or "," indicates match on the forward or reverse strand; ACGTN and acgtn a
mismatch on the forward and the reverse strand) and the final column is the base qualities encoded into
characters. The symbol "^" marks the start of a read, "$" the end of a read, deleted bases are represented as "*".

## Q: What is the read depth at position 10001994? (Rather than scrolling to the position, use the substring searching capabilities of less : press / , then type 10001994 and enter to find the position.)
66

## Q: What is the reference base? Are there any non-reference bases?
ref base-A
non-reference bases-Gg

This output can be used for a simple consensus calling. For a more sophisticated variant calling method,
go to the next section.
## using vcftools
## i) Allele frequency 
```
vcftools --vcf A_J.vcf --freq --chr 19 --out chr19_analysis
```


## Exercise 3: Generating genotype likelihoods and variant calling
The mpileup command (traditionally in samtools , now moved to bcftools ) can be used to generate genotype
likelihoods. Try to run the following command (press " q " to quit the viewing mode)
```
bcftools mpileup -f GRCm38_68.19.fa A_J.bam | less -S
```
This generates an intermediate output which contains genotype likelihoods and other raw information
necessary for calling. This output is usually streamed directly to the caller like this
```
bcftools mpileup -f GRCm38_68.19.fa A_J.bam | bcftools call -m | less -S
```
```
## bcftools annotate -x INFO/vep file.vcf.gz | bcftools view --types snps --regions-file bedfile -o output_file - O z
``` 
## view variant positions only
```
bcftools mpileup -f GRCm38_68.19.fa A_J.bam | bcftools call -mv | less -S  #view variant regions only
```
Let mpileup output more information. For example we can ask it to add the FORMAT/AD tag which informs
about the number of high-quality reads that support alleles listed in REF and ALT columns. The list of all
available tags can be printed with " bcftools mpileup -a? .
Now let's run the variant calling again, this time adding the -a AD option. We will also add the -Ou option to
mpileup so that it streams a binary uncompressed BCF into call . This is to avoid the unnecessary CPU
overhead of formatting the internal binary format into plain text VCF only to be immediately formatted back to
the internal binary format again:
## Best command to generate VCF
```
bcftools mpileup -a AD -f GRCm38_68.19.fa A_J.bam -Ou | bcftools call -mv -o out.vcf
```
## command2 for calling variants: mpileup
```
bcftools mpileup -f GRCm38_68.19.fa NZO.bam|bcftools call -mv -Ob -o NZO.vcf #prouces a BCF binary file. use bcftools view
```

## Examine the VCF file output using the unix command less :
less -S out.vcf
## Q: What is the reference and SNP base at position 10001994?
69
Ref =A
SNP=G

## Q: What is the total read depth at position 10001994?
69

## Q: What is the number of high-quality reads supporting the SNP call at position 10001994? How many reads support the reference allele?
66 reads support SNP
0 reads support reference allele
## Q: What sort of event is happening at position 10003649?
Insertion in alternate sequence. 5  insetions(


### Exercise 4: Variant filtering
In the series of commands we will learn how to filter and extract information from VCFs. Most of the bcftools commands accept the -i, --include and -e, --exclude options (documentation) which will come handy when filtering using fixed thresholds. We will estimate the quality of the callset by calculating the transition/transversion ratio.

## printing a simple list of positions from the VCF using the bcftools query

```
bcftools query -f'POS = %POS\n' out.vcf | head
```
## Now add REF and ALT allele to the output, separated by a comma

```
bcftools query -f'%POS %REF,%ALT\n' out.vcf | head
```

````
bcftools query -f'%POS %QUAL [%GT %AD] %REF %ALT\n' out.vcf | head
````
### reads with quqlity >30 and exclude indels
```
bcftools query -f'%POS %QUAL [%GT %AD] %REF %ALT\n' -i'QUAL>=30 && type="snp"' \
out.vcf | head
```
## Filtering indels
```
bcftools query -f'%POS %QUAL [%GT %AD] %REF %ALT\n' -i'QUAL>=30 && type="INDEL"' out.vcf | head
```
## Transition/transversion ratio
```
bcftools stats out.vcf | grep TSTV | cut -f5
```


