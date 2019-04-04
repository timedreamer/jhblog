---
title: Rice RAPDB to MSU7 ID conversion
author: Ji Huang
date: '2019-04-03'
categories:
  - dry lab
tags:
  - data-clean
  - R
  - rice
comment: no
contentCopyright: no
lastmod: '2019-04-03T18:28:30-04:00'
mathjax: no
mathjaxEnableSingleDollar: no
postMetaInFooter: no
reward: no
slug: rice-rapdb-to-msu7-id-conversion
autoCollapseToc: no
toc: no
---

In this post, I generate the table for **RAPDB** gene IDs and **MSU7** gene IDs conversion.

<!--more-->

There are some online tools can do the conversion, but it's more handy to have a local version. One online tool is [RAP-DB ID Converter](https://rapdb.dna.affrc.go.jp/tools/converter). The other is [OryzaExpress ID converter](http://bioinf.mind.meiji.ac.jp/OryzaExpress/ID_converter.php).

The conversion table was downloaded from [RAPDB](https://rapdb.dna.affrc.go.jp/download/irgsp1.html) on 2019-04-03.



# 0. Set up environment.

```r
library(tidyverse)
```

# 1. Read data


```r
# read raw downloaded table.
c_table <- read_tsv("./RAP-MSU_2019-03-22.txt", col_names = c("RAPDB", "MSU"))
```


# 2. Process table.

Get RAPDB gene ID correspondent MSU7 IDs. The MSU7 transcript ID was converted to Gene ID by removing **.[digits]**.

```r
cp_table <- c_table %>% separate_rows(MSU,sep = ",") %>%  
    mutate(MSU7 = str_replace(.$MSU,"\\.[:digit:]+","")) %>% 
    select(-MSU) %>% 
    distinct(RAPDB, MSU7)
```

There are **45967** RAPDB genes. And **55802** MSU7 genes.


# 3. Save table.

```r
write_tsv(cp_table, path = "./RAPDB_MSU_ID_conversion_20190403.txt")
```

# 3. Some exploratory analysis.

1. How many RAPDB genes don't have MSU7 genes, as indicated by **None** in the `MSU` column?

Answer: 12282.

```r
sum(cp_table$MSU7 == "None")
```

2. How many MSU7 genes don't have RAPDB IDs, as indicated by **None** in the `RAPDB` column?

Answer: 22991.

```r
sum(cp_table$RAPDB == "None")
```

3. How many RAPDB genes have multiple MSU7 genes?

Answer: 430.

```r
cp_table %>% group_by(RAPDB) %>% tally() %>% filter(n > 1) %>% 
    dim() %>% magrittr::extract(1) -1
```

4. How many MSU7 genes have multiple RAPDB genes?

Answer: 1233.

```r
cp_table %>% group_by(MSU7) %>% tally() %>% filter(n > 1) %>% 
    dim() %>% magrittr::extract(1) -1
```

