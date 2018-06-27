---
title: RNASeq reads distribution
author: Ji Huang
date: '2018-03-28'
slug: rnaseq-reads-distribution
categories:
  - dry lab
tags:
  - bioinformatics
  - rna-seq
lastmod: '2018-03-28T14:24:58-04:00'
keywords: [rnaseq, picard, qc]
description: ''
comment: no
toc: no
autoCollapseToc: no
postMetaInFooter: no
contentCopyright: no
reward: no
mathjax: no
mathjaxEnableSingleDollar: no
---

In a RNA-Seq analysis, sometimes it is helpful to know where your reads alligned. What propotion of reads alligned to exon, intron and intergenic regions. The easiest way to do this (as far as I know), is to use `CollectRnaSeqMetrics` from [Picard Tool](https://broadinstitute.github.io/picard/command-line-overview.html#CollectRnaSeqMetrics).

<!--more-->

I will assume you already installed Picard Tool. It's based on Java, just follow the instruction [here](https://broadinstitute.github.io/picard/index.html).


The command you will need is `CollectRnaSeqMetrics`. Before run this command, you need to prepare a **refFlat** format gene annotation file for your organism. **The refFLat** format is a version of genePred format, mainly used for UCSC genome browser. You can find the details [here](https://genome.ucsc.edu/FAQ/FAQformat.html#format9). I work with maize, so I will use maize gene annotation file as an example.

### 1. Convert GTF/GFF to genePred.

I assume you have your gene annotation file in GTF or GFF3 format which are most popular format. To convert GTF/GFF3 to genePred, you need `gff3ToGenePred` or `gtfToGenePred`. These two are from Kent Utilites, just need to download and add to your `$PATH`. The [link is here](http://hgdownload.cse.ucsc.edu/admin/exe/linux.x86_64/).

The maize v3 gene annotation can be downloaded from Ensembl Plant. I kept all data source from Ensembl Plant as much as possible. The version I use is **AGPv3.31**.The [link is here](ftp://ftp.ensemblgenomes.org/pub/plants/release-31/gff3/zea_mays/). The script is below:

```shell
# gff2pred.sh

wget -q ftp://ftp.ensemblgenomes.org/pub/plants/release-31/gff3/zea_mays/Zea_mays.AGPv3.31.gff3.gz

gunzip Zea_mays.AGPv3.31.gff3.gz

gff3ToGenePred Zea_mays.AGPv3.31.gff3 Zea_mays.AGPv3.31.genePred
```

### 2. Re-format genePred file.

The file we get from last script does not fit the **refFlat** format yet. Check out the **refFlat** format below.

```
table refFlat
"A gene prediction with additional geneName field."
    (
    string  geneName;           "Name of gene as it appears in Genome Browser."
    string  name;               "Name of gene"
    string  chrom;              "Chromosome name"
    char[1] strand;             "+ or - for strand"
    uint    txStart;            "Transcription start position"
    uint    txEnd;              "Transcription end position"
    uint    cdsStart;           "Coding region start"
    uint    cdsEnd;             "Coding region end"
    uint    exonCount;          "Number of exons"
    uint[exonCount] exonStarts; "Exon start positions"
    uint[exonCount] exonEnds;   "Exon end positions"
    )
```

Here is the file (Zea_mays.AGPv3.31.genePred) we got:

```
transcript:GRMZM6G708185_T01    scaffold_99     +       127     1747    185     1746    4       127,571,1364,1507,      326,877,1428,1747, 0  gene:GRMZM6G708185      cmpl    cmpl    0,0,0,1,
transcript:GRMZM6G036147_T01    scaffold_98     +       99      1334    108     1266    4       99,336,688,837, 240,594,733,1334,       0  gene:GRMZM6G036147       cmpl    cmpl    0,0,0,0,
transcript:GRMZM6G699895_T01    scaffold_94     +       699     3163    707     2835    4       699,1717,2535,2721,     867,1848,2646,3163,0  gene:GRMZM6G699895      cmpl    cmpl    0,1,0,0,

```

So there are couple of things we need to do:

1. delete `transcript:` and `gene:`.

2. duplicate the first column. The first column in the **refFlat** format is the gene name and the second is the transcript name. But here, since we don't care gene/transcript name so much, so we can just duplicate the first column.

3. keep the first 11 columns. Delete some extra column that we don't need in **refFlat** format.

The code is below:

```shell
# reformatGenePred.sh
sed 's/^transcript://; s/gene://' Zea_mays.AGPv3.31.genePred | \
awk 'BEGIN {FS="\t";OFS="\t"} {$1=$1 "\t" $1} 1' |cut -f 1-11 > Zea_mays.AGPv3.31.refFlat
```

Now you have the **refFlat** format gene annotation file.


### 3. Run `CollectRnaSeqMetrics`.

The step is pretty easy. Just follow the [document online](https://broadinstitute.github.io/picard/command-line-overview.html#CollectRnaSeqMetrics).

It's ok that we don't have the `RIBOSOMAL_INTERVALS` file. The single- and paired-end libraries need different settings on `STRAND_SPECIFICITY `.

```shell
java -jar /home5/jhuang/software/picard-2.10.2/picard.jar CollectRnaSeqMetrics I=YOUR.bam O=YOUR.metrics REF_FLAT=YOUR.refFlat STRAND=NONE
```

It took ~30min on my bam file. So probably a good idea to send it to background. The result is a single txt file. The meaning for each column can be found [here](http://broadinstitute.github.io/picard/picard-metric-definitions.html#RnaSeqMetrics). **PF** means **passing filter**. You are welcome~


Good luck!

