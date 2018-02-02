---
title:  "Remove tRNA and rRNA from RNA-Seq data"
date: 2017-10-06
lastmod: 2018-02-02
draft: false
tags: ["rna-seq", "bioinformatics"]
categories: ["dry lab"]
toc = true
---

When analyzing RNA-Seq data, rRNA and tRNA reads can be removed from the sequencing files. Here, I briefly describe how to do this step using [ERNE](http://erne.sourceforge.net/index.php)

<!--more-->

# Prepare software

Download software from `https://sourceforge.net/projects/erne/files/2.1.1/`

Unzip and move `erne-create` and `erne-filter` to `~/local/bin`

Install Seqkit `conda install -c bioconda seqkit` to use `rmdup` that remove duplicated fasta files.


# Prepare rRNA and tRNA sequence

## rRNA

rRNA sequences were downloaded from [Silver](https://www.arb-silva.de/)


```
wget https://www.arb-silva.de/fileadmin/silva_databases/current/Exports/SILVA_128_LSUParc_tax_silva.fasta.gz
```

## tRNA

tRNA sequences were downloaded from [GtRNAdb](http://gtrnadb2009.ucsc.edu/download.html).

```
wget http://gtrnadb2009.ucsc.edu/download/tRNAs/GtRNAdb-all-tRNAs.fa.gz
```

## Combine two fasta files together and remove duplicate

Combine two fasta files as **contaminate_rna.fa**.

```
cat GtRNAdb-all-tRNAs.fa SILVA_128_LSUParc_tax_silva.fasta > contaminate_rna.fa
cat contaminate_rna.fa |seqkit rmdup -o contaminate_rna_uniq.fa
```

# Prepare allign file

```
erne-create --output-prefix contaminate_rna --fasta contaminate_rna_uniq.fa & # takes about three hours
```

# Run erne-filter to remove rRNA/tRNA

```
erne-filter --contamination-reference contaminate_rna.ebh --threads 20 --query1 test_trimmed.fq --output-prefix rmcontac&
```

As a result, the clean file `rmcontac_1.fastq` has 55167445 sequences, while the original file has 55301662 sequences. Over **99.76%** of reads retained. This step is probably more important for small RNA-sequencing.
