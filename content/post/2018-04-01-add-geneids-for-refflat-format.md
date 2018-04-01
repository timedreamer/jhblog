---
title: Add geneIDs for refFlat format
author: Ji Huang
date: '2018-04-01'
slug: add-geneids-for-refflat-format
categories:
  - dry lab
tags:
  - bioinformatics
  - maize
  - R
---

This post is to convert the first column of **reFlat** format from transcript ID to Gene ID for version 3 of maize genome annotation. For version 3 genome, either the transcript ID is (1) GRMZM6G708185_T01 or (2) AC202015.3_FGT004 (or starts with AF, AY or EF). To convert these IDs to gene ID, for (1) need to discard after "-"; for (2) need to delete the "T" after the underscore.

For how to create refFlat format from GFF3, please see [the previous post](http://jhuang.netlify.com/post/rnaseq-reads-distribution/).

## 1. Load libraries

```r
library(tidyverse)
library(stringr)
setwd("~/projects/Misc/iRNA-Seq/irna-seq_maize/v3/")
```

## 2. Read the file.

```r
## read all columns as character to avoid error at Pt and Mt. 
ref <- read_tsv("Zea_mays.AGPv3.31.refFlat", col_names = F,
                col_types = cols(.default = col_character())) %>%
  arrange(X1)
```

## 3. Convert

The basic idea is to separate geneIDs starting with "AC|AF|AY|EF" and "GRMZM", convert each type and then combine them together.

```r
## use linux command to find unique gene ID starting characters.
system("cut -f 1 Zea_mays.AGPv3.31.refFlat|cut -c 1-3|sort -u |uniq")

## convert for gene IDs starting with "AC, AF, AY and EF"
t_ac <- ref %>%
  filter(., str_detect(.$X1, "AC|AF|AY|EF")) %>%
  mutate(X1 = str_replace(.$X1, "_FGT", "_FG"))

## convert for gene IDs starting with "GRMZM"
t_gm <- ref %>%
  filter(., str_detect(.$X1, "GRMZM")) %>%
  mutate(X1 = str_replace(.$X1, "_T.+", ""))

## combine two together
ref_gene <- bind_rows(t_ac, t_gm)

## check all rows are retained.
nrow(ref_gene) == nrow(ref)
## check how many genes in the annotation.
length(unique(ref_gene$X1)) # 39469
```

## 4. Write table

```r
write_tsv(ref_gene, "Zea_mays.AGPv3.31.geneID.refFlat")
rm(list = ls())
gc()
```



