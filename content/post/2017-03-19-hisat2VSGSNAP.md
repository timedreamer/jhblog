---
title:  "HISAT2 vs GSNAP, a simple simulation study "
date: 2017-03-19
tags: ["bioinformatics", "rna-seq"]
categories: ["dry lab"]
---

There are so many aligner for next generation sequencing, especially for illumina platform that everyone is using now. Among all these, tophat/tophat2 is the most widely used, though several paper showed it's not the best choice.

<!--more-->

Based on the most recent Nature Method paper: [Simulation-based comprehensive benchmarking of RNA-seq aligners](http://go.nature.com/2mD3zwU), CLR, GSNAP, Novoallign performed better than others, including Tophat2 and HISAT2.

When I started my project, I chose HISAT2 over Tophat2 mainly because of the fast speed. Besides, based on this [blog](http://bit.ly/2ngO3Kn), HISAT2 is comparable to STAR and Tophat2. I also used GSNAP previously for finding SNPs using [123SNP](http://bit.ly/2mkwWDb) developed by Schnable lab. So with a little bit of time available, I  plan to do a simple comparison between GSNAP and HISAT2 based on simulated reads.

# Read simulator

First, we need to choose a short read simulator. There are many open tools available, here is a [**review**](http://go.nature.com/2mkr4d9) in Nature Review Genetics. One of the most widely used and simplest is [wgsim](https://github.com/lh3/wgsim) developed by Heng Li. In the following analysis, I used wgsim for read simulator. However, it generates reads from uniform distribution, but may not represent the true distribution of short reads. Many researchers believe short reads follow negative binomial distribution. A recent R pacakge [polyster](http://bit.ly/2nxvzBo) can do this, here is the [paper](http://bit.ly/2mF6t5W), developed by Jeff Leek. But for me, to keep things simple, I chose wgsim. An aligner comparison [paper](http://bit.ly/2lUu0kD) in 2013 used wgsim for simulating.

I generated 1 million reads from Arabidopsis gene model.

## Install wgsim

1. Download from https://github.com/lh3/wgsim
2. Compilation: *gcc -g -O2 -Wall -o wgsim wgsim.c -lz -lm*
3. Move **wgsim** and **wgsim_eval.pl** to **~/local/bin**
4. Run wgsim

* Download TARI10 representative gene model
* Generate three sets of 100bp pair-end synthetic reads
* no mutation and InDel  (**perfect**)
* Default setting  which is 0.001 mutation rate (**mut1**)
* Increased mutation rate to 0.01 (**mut2**)

code:
```bash
wget https://www.arabidopsis.org/download_files/Sequences/TAIR10_blastsets/TAIR10_seq_20110103_representative_gene_model_updated

mv TAIR10_seq_20110103_representative_gene_model_updated TAIR10_seq_20110103_representative_gene_model_updated.fasta

# Simulate Pefect reads from cDNA. Perfect

wgsim -e 0 -d 250 -s 25 -1 100 -2 100 -r 0 -R 0 -X 0 -S 1 TAIR10_seq_20110103_representative_gene_model_updated.fasta arab_perfect_read1.fq arab_perfect_read2.fq 2> arab_perfect.short 1>arab_perfect.log

# Simulate SNP rate 0.001. Mut1

wgsim -d 250 -s 25 -1 100 -2 100 -S 1 TAIR10_seq_20110103_representative_gene_model_updated.fasta arab_mut_read1.fq arab_mut_read2.fq 2> arab_mut.short 1> arab_mut.log

# Simulate SNP rate 0.01. Mut2

wgsim -d 250 -s 25 -1 100 -2 100 -S 1 -r 0.01 TAIR10_seq_20110103_representative_gene_model_updated.fasta arab_mut2_read1.fq arab_mut2_read2.fq 2> arab_mut2.short 1>arab_mut2.log

# Check how many SNPs generated

wc -l arab_mut.log # 70653 SNPs for mut1

wc -l arab_mut2.log # 707347 SNPs for mut2

```

# Alignment

To align reads to genome, [HISAT2](https://ccb.jhu.edu/software/hisat2/index.shtml) version 2.0.4 and [GSNAP](http://research-pub.gene.com/gmap/) 2017-02-25 was used.

## Install GSNAP
Follow manual, install on user directory, so you don't need root privilege.

From manual, following GNU commands
```bash
./configure prefix= $HOME/local/
make
make check
make install
```
## Build GSNAP database
```bash
wget ftp://ftp.ensemblgenomes.org/pub/release-34/plants/fasta/arabidopsis_thaliana/dna/Arabidopsis_thaliana.TAIR10.dna.toplevel.fa.gz
gmap-build -d arab_TAIR10 Arabidopsis_thaliana.TAIR10.dna.toplevel.fa
```

## Install HISAT2 and Build database
Binary version was downloaded from https://ccb.jhu.edu/software/hisat2/index.shtml.

```bash
hisat2-build Arabidopsis_thaliana.TAIR10.dna.toplevel.fa TAIR10
```

## Align to Genome
```bash

hisat2 -x TAIR10 -p 4 -t -1 arab_perfect_read1.fq -2  arab_perfect_read2.fq -S hisat2_perfect.sam 2>hisat2_perfect.txt
hisat2 -x TAIR10 -p 4 -t -1 arab_mut_read1.fq -2  arab_mut_read2.fq -S hisat2_mut1.sam 2>hisat2_mut1.txt
hisat2 -x TAIR10 -p 4 -t -1 arab_mut2_read1.fq -2  arab_mut2_read2.fq -S hisat2_mut2.sam 2>hisat2_mut2.txt

gsnap -d arab_TAIR10 -A sam arab_perfect_read1.fq arab_perfect_read2.fq >
gsnap_perfect.sam
gsnap -d arab_TAIR10 -A sam arab_mut_read1.fq arab_mut_read2.fq >
gsnap_mut1.sam
gsnap -d arab_TAIR10 -A sam arab_mut2_read1.fq arab_mut2_read2.fq >
gsnap_mut2.sam
```

# Count Process
After get aligned SAM files, they need to be converted to BAM format and then processed by FeatureCount.

```bash
Samtools view -Sb hisat2_perfect.sam > hisat2_perfect.bam
Samtools view -Sb hisat2_mut1.sam > hisat2_mut1.bam
Samtools view -Sb hisat2_mut2.sam > hisat2_mut2.bam

Samtools view -Sb gsnap_perfect.sam > gsnap_perfect.bam
Samtools view -Sb gsnap_mut1.sam > gsnap_mut1.bam
Samtools view -Sb gsnap_mut2.sam > gsnap_mut2.bam

# FeatureCount Version 1.5.0-p3
wget ftp://ftp.ensemblgenomes.org/pub/release-34/plants/gtf/arabidopsis_thaliana/Arabidopsis_thaliana.TAIR10.34.gtf.gz

featureCounts -T 16 -p -a Arabidopsis_thaliana.TAIR10.34.gtf -o FScount.txt gsnap_perfect.bam gsnap_mut1.bam gsnap_mut2.bam hisat2_perfect.bam hisat2_mut1.bam hisat_mut2.bam 2>FS.info
```
By default, multiple mapped reads were filtered out by FeatureCount. It checks for **NH tag** in BAM files.
Here is the summary report:

![Imgur](http://i.imgur.com/SzrMoHM.png)

HISAT2 has more unmapped reads than GSNAP, but less Assigned reads. With more SNPs, assigned reads decreased in both.

#Compare with real counts
Number of simulated reads for each genes were calculated. I used the same seed for all wgsim command, so they generated same reads for all files but with different number of SNPs.

```bash
# get real count simulated.
grep @ arab_mut_read1.fq |awk '{print substr($0,2,9)}'|cut -d '' -f1|sort -n|uniq -c >arab_mut_read1.realC
```
Merge real count with FeatureCount result. Some genes are not presented in the real count data, may be because representative gene model do not fit all GTF files? But anywaym 26695 genes were remained (FScount_processed.txt).

Percentage of correct aligned reads (I just calculated number of reads in total, did not consider position):

![Imgur](http://i.imgur.com/lY30YG5.png)

This is consistent with previous FeatureCount summary. GSNAP reported more correct reads.
Pairwise comparisons are very similar, with pearson correlation over 0.94.
![Imgur](http://i.imgur.com/WICckma.png)

# Conclusion
Based on simulated reads, GSNAP reported more assigned reads, more correct assigned reads and less unmapped reads than HISAT2. I know this is far from concrete, but just give a very naiive test. In the future, I will use GSNAP instead. I wonder whether provide pre-made SNP data and GTF file, how much the alignment can be improved.

PS: You may also want to check out these two posts.
1. [HISAT vs STAR vs TopHat2 vs Olego vs SubJunc](http://bit.ly/2ngO3Kn)
2. [Comparing salmon, kalliso and STAR-HTseq RNA-seq quantification](http://bit.ly/2nwXLVJ)
