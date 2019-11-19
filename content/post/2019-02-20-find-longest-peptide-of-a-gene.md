---
title: Find longest peptide of a gene
author: Ji Huang
date: '2019-02-20'
slug: find-longest-peptide-of-a-gene
categories:
  - dry lab
tags:
  - arabidopsis
  - rice
  - maize
  - data-clean
lastmod: '2019-02-20T22:36:57-05:00'
keywords: []
description: ''
comment: yes
toc: true
autoCollapseToc: no
postMetaInFooter: no
contentCopyright: no
reward: no
mathjax: no
mathjaxEnableSingleDollar: no
---

<!--more-->

Update: 2019-11-19. For many plant species, you can acturally directly download the longest transcript/peptide from [Phytozome](https://genome.jgi.doe.gov/portal/pages/dynamicOrganismDownload.jsf?organism=Phytozome) (Required to login first). 

You can find the longest *CDS*, *Transcript* and *protein* fasta files.

![](https://i.imgur.com/y2yUbdV.png)

-----

**Original post:**

This script is to (1) download peptide sequences (2) modify the title for the fasta file  (3) choose the longest peptide sequence for each gene.

Currently, I included (1) Arabidopsis (2) Maize (3) Rice in the file. The version is Ensembl plant release 41.

This script is highly depends on this [biostar question](https://www.biostars.org/p/107759/).

For maize (267 genes) and rice (158 genes), there are very small amount of gene with irregular gene names. Those genes were discarded. The gene names can be retrieved by this command 
`grep ">" Zea_mays.B73_RefGen_v4.pep.all.nameOnly.fa |sort|grep -v "Zm"|wc -l`. `grep ">" Oryza_sativa.IRGSP-1.0.pep.all.nameOnly.fa |sort|grep -v "Os"|wc -l`

# 1. Arabidopsis

## 1.1 Download Arabidopsis peptide sequences. Unzip.

```bash
wget ftp://ftp.ensemblgenomes.org/pub/release-41/plants/fasta/arabidopsis_thaliana/pep/Arabidopsis_thaliana.TAIR10.pep.all.fa.gz

gunzip Arabidopsis_thaliana.TAIR10.pep.all.fa.gz
```

## 1.2 Modify fasta title. Keep transcript name only.

```bash
cat Arabidopsis_thaliana.TAIR10.pep.all.fa |sed 's/ pep.*$//g' > Arabidopsis_thaliana.TAIR10.pep.all.nameOnly.fa
```

## 1.3 Keep the longest peptide only.

```bash
cat Arabidopsis_thaliana.TAIR10.pep.all.nameOnly.fa |
awk '/^>/ {if(N>0) printf("\n"); printf("%s\t",$0);N++;next;} {printf("%s",$0);} END {if(N>0) printf("\n");}' | #linearize fasta
tr "." "\t" | #extract version from header
awk -F '	'  '{printf("%s\t%d\n",$0,length($3));}' | #extact length
sort -t '	' -k1,1 -k4,4nr | #sort on name, inverse length
sort -t '	' -k1,1 -u -s | #sort on name, unique, stable sort (keep previous order)
sed 's/	/./' | #restore name
cut -f 1,2 | #cut name, sequence
tr "\t" "\n"  | #back to fasta
fold -w 60 > Arabidopsis_thaliana.TAIR10.pep.all.longestOnly.fa #pretty fasta
```

# 2. Maize

## 2.1 Download maize peptide sequences. Unzip.

```bash
wget ftp://ftp.ensemblgenomes.org/pub/release-41/plants/fasta/zea_mays/pep/Zea_mays.B73_RefGen_v4.pep.all.fa.gz

gunzip Zea_mays.B73_RefGen_v4.pep.all.fa.gz
```

## 2.2 Modify fasta title. Keep transcript name only.

```bash
cat Zea_mays.B73_RefGen_v4.pep.all.fa |sed 's/ pep.*$//g' >Zea_mays.B73_RefGen_v4.pep.all.nameOnly.fa
```

## 2.3 Keep the longest peptide only.

```bash
cat Zea_mays.B73_RefGen_v4.pep.all.nameOnly.fa|
awk '/^>/ {if(N>0) printf("\n"); printf("%s\t",$0);N++;next;} {printf("%s",$0);} END {if(N>0) printf("\n");}' | #linearize fasta
tr "_" "\t" | #extract version from header
grep "Zm00"| # keep Zm genes.
awk -F '	'  '{printf("%s\t%d\n",$0,length($3));}' | #extact length
sort -t '	' -k1,1 -k4,4nr | #sort on name, inverse length
sort -t '	' -k1,1 -u -s | #sort on name, unique, stable sort (keep previous order)
sed 's/	/_/' | #restore name
cut -f 1,2 | #cut name, sequence
tr "\t" "\n"  | #back to fasta
fold -w 60 > Zea_mays.B73_RefGen_v4.pep.all.longestOnly.fa #pretty fasta
```

# 3. Rice

## 3.1 Download rice peptide sequences. Unzip.

```bash
wget ftp://ftp.ensemblgenomes.org/pub/release-41/plants/fasta/oryza_sativa/pep/Oryza_sativa.IRGSP-1.0.pep.all.fa.gz

gunzip Oryza_sativa.IRGSP-1.0.pep.all.fa.gz
```

## 3.2 Modify fasta title. Keep transcript name only.

```bash
cat Oryza_sativa.IRGSP-1.0.pep.all.fa |sed 's/ pep.*$//g' > Oryza_sativa.IRGSP-1.0.pep.all.nameOnly.fa
```

## 3.3 Keep the longest peptide only.

```bash
cat Oryza_sativa.IRGSP-1.0.pep.all.nameOnly.fa |
awk '/^>/ {if(N>0) printf("\n"); printf("%s\t",$0);N++;next;} {printf("%s",$0);} END {if(N>0) printf("\n");}' | #linearize fasta
tr "-" "\t" | #extract version from header
grep "Os"| # keep Os genes.
awk -F '	'  '{printf("%s\t%d\n",$0,length($3));}' | #extact length
sort -t '	' -k1,1 -k4,4nr | #sort on name, inverse length
sort -t '	' -k1,1 -u -s | #sort on name, unique, stable sort (keep previous order)
sed 's/	/-/' | #restore name
cut -f 1,2 | #cut name, sequence
tr "\t" "\n"  | #back to fasta
fold -w 60 > Oryza_sativa.IRGSP-1.0.pep.all.longestOnly.fa #pretty fasta
```

# 4. Clean

```bash
rm *.all.fa
rm *.nameOnly.fa
```
