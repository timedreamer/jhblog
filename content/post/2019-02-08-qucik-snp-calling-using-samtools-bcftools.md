---
title: Qucik SNP calling using samtools/bcftools
author: Ji Huang
date: '2019-02-08'
slug: qucik-snp-calling-using-samtools-bcftools
categories:
  - dry lab
tags:
  - bioinformatics
  - rice
lastmod: '2019-02-19T11:55:59-05:00'
keywords: []
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

RNA-Seq can provide more than just expression level. Because its sequencing nature, we can also call SNPs from the RNASeq libraries. The SNPs will be only in genic regions, but it may stil help us to answer what genetic variation might be contribution to our trait.

<!--more-->

We have two rice genotypes IR108 and IR64. I want to call SNPs for each genotypes and then compare. The analysis was done on NYU HPC.

## 1. Load modules

```shell
module load samtools/intel/1.6
module load bcftools/intel/1.3.1
module load vcflib/intel/20170223
```

## 2. Index the reference genome

```shell
samtools faidx Oryza_sativa.IRGSP-1.0.dna.toplevel.fa
```

## 3. Merge all bam file for each genotype

I used `STAR` for the allignment. This step took roughly 6h. Each file is about 90G.

```shell
samtools merge rice_ir108_total.bam ir108*.bam 
samtools merge rice_ir64_total.bam ir64*.bam 
```

## 4. Call SNPs

The final `bcf` files are about 19M.

```shell
samtools mpileup --redo-BAQ --min-BQ 20 --per-sample-mF --output-tags DP,AD -f /scratch/cgsb/coruzzi/jh6577/GenomeFile/rice_IRGSP1_0/Oryza_sativa.IRGSP-1.0.dna.toplevel.fa --BCF rice_ir108_total.bam | bcftools call --multiallelic-caller --variants-only -Ob > rice_ir108_var.bcf&

samtools mpileup --redo-BAQ --min-BQ 20 --per-sample-mF --output-tags DP,AD -f /scratch/cgsb/coruzzi/jh6577/GenomeFile/rice_IRGSP1_0/Oryza_sativa.IRGSP-1.0.dna.toplevel.fa --BCF rice_ir64_total.bam | bcftools call --multiallelic-caller --variants-only -Ob > rice_ir64_var.bcf&
```

## 5. Filter SNPs

From last step, we will have a lot of false SNPs that may only have several sequences support. To filter low quality SNPs, I used the criteria `DP > 300` which means at least 300 reads support the SNP. Also I only look at the homozygous site `GT == 1|1`. The final `vcf` files were about 6M.

```shell
bcftools view rice_ir108_var.bcf |vcffilter -f "DP > 300" -g "GT = 1|1" > rice_ir108_var_DP300_GT11.vcf&

bcftools view rice_ir64_var.bcf |vcffilter -f "DP > 300" -g "GT = 1|1" > rice_ir64_var_DP300_GT11.vcf&
```

## 6. Variant Effect

To predict the varaint effect, I used the online tool [Variant Effect Predictor](http://plants.ensembl.org/Oryza_sativa/Tools/VEP?db=core) from Ensembl Plant. It was super easy to use, just uploaded the `vcf` file and wait for the result.




