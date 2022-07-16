# Burrow-Wheeler-Aligner
Using BWA to  align sequenced reads to reference genome 

BWA maps DNA sequences to a large reference genome, like human or plant genomes. BWA-backtrack, BWA-SW, and BWA-MEM are its algorithms. BWA-backtrack can be used to align illumina reads of up to 100bp, while BWA-mem and BWA-SW can be use for longer reads of 70bp-Megabases.

BWA and Picard tools can be downloaded from the following link
## BWA
https://sourceforge.net/projects/bio-bwa/files/

OR

https://github.com/lh3/bwa

## PICARD tools

https://github.com/broadinstitute/picard/releases/tag/2.27.4

Make three directories
```
mkdir Ref_genome # for reference genome

mkdir FastqFiles # for raw fastq files

mkdir BamFiles # for output of BWA
```

### Processing the reference genome

Indexing of reference genome is required for BWA alignment, to index the reference genome run the following command
```
bwa index -p Ref_genome/your_ref.genome
```

After indexing several files will be generated in the Ref_genome directory
### Alignment of reads to reference genome

> Note: if we have multiple paired end reads such SRR1.1.fastq.gz  SRR.1.2.fastq.gz  SRR.2.1.fastq.gz  SRR.2.2.fastq.gz  SRR.3.1.fastq.gz  SRR.3.2.fastq.gz  SRR.4.1.fastq.gz  SRR.4.2.fastq.gz

### then run the following command
```
for INDEX in 1 2 3 4;
do
bwa mem -M -t 8 -R "@RG\tID:COL_${INDEX}\tSM:COL_${INDEX}" Ref_genome/genome.Garb.CRI.fa \
FastqFiles/SRR${INDEX}.1.fastq.gz \
FastqFiles/SRR${INDEX}.2.fastq.gz \
> BamFiles/SRR${INDEX}.sam
done
```

***Note: Here @RG refers to Read groups, that are collections of reads from a single sequencing run. This information is stored as @RG-starting lines in our SAM file. Read groups enable us to differentiate between samples and specific sequenced samples across experiments. GATK requires this information to account for the variability of sequencing runs.***

For details about BWA commands visit manual page 

http://bio-bwa.sourceforge.net/bwa.shtml

after this multiple sam files will be generated in the output directory

Convert our SAM files from BWA to BAM, which downstream programs use as input.
```
for INDEX in {1..4};
do
picard SortSam \
I=BamFiles/SRR${INDEX}.sam \
O=BamFiles/SRR${INDEX}.sorted.bam \
SORT_ORDER=coordinate \
CREATE_INDEX=true
done
```

### Create an index for BAM files in order for downstream programs to access its contents quickly.
```
for INDEX in {1..4}
do
picard BuildBamIndex \
I=BamFiles/SRR${INDEX}.sorted.bam
done
```
### Reference

Li H, Durbin R. Fast and accurate short read alignment with Burrows-Wheeler transform. Bioinformatics. 2009 Jul 15;25(14):1754-60. doi: 10.1093/bioinformatics/btp324. Epub 2009 May 18. PMID: 19451168; PMCID: PMC2705234.
