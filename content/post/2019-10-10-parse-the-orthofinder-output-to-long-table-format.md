---
title: Parse the OrthoFinder output to long table format
author: Ji Huang
date: '2019-10-10'
slug: parse-the-orthofinder-output-to-long-table-format
categories:
  - dry lab
tags:
  - arabidopsis
  - rice
  - R
  - orthology
lastmod: '2019-10-10T14:44:48-04:00'
keywords: []
description: ''

---


It's been a while since I update any blogs. This blog is to show how to parse the **OrthoFinder** output (*Orthogroups.csv*) into the *tidy long* format.

<!--more-->

I did the OrthoFinder on two speceis, rice and Arabidopsis. The *Orthogroups.csv* file has three columns: 

1. *orthogroup ID*
2. *arabidopsis gene IDs* (separate by comma)
3. *rice gene IDs* (separate by comma)

![](https://i.imgur.com/6Qt4ouf.png)


To do the parse, follow the following code:


```r
library(tidyverse)

#  Deal with OrthoFinder result.
ogroup <- read_tsv(here("data", "orthoFinder", "Orthogroups.csv"))

# Drop groups that have only one-species. Then convert character to a list.
ogroup1 <- ogroup %>% drop_na() %>% 
  mutate(ath = str_split(.$ath, pattern = ", "),  
         msu7 = str_split(.$msu7, pattern = ", "))

parse_orthogroup_long <- function(i) {
    temp1 <- list(ogroup1$ath[[i]], ogroup1$msu7[[i]])
    temp1 <- cross(temp1) %>%  map(lift(paste)) %>% unlist()
    temp2 <- tibble(edge = temp1) %>% mutate(group = ogroup1$X1[i]) %>% 
        separate(edge, into = c("tair10", "msu7"), sep = " ")
}

ogroup_long <- map(1:nrow(ogroup1), parse_orthogroup_long)
ogroup_long <- bind_rows(ogroup_long)

write_tsv(ogroup_long, here("result", "tair10_to_msu7_orthogroup.tsv.gz"))

```

The result is a long format table with three columns. From this format, I can do `join` quite easy.

**tair10**|**msu7**|**group**
:-----:|:-----:|:-----:
AT1G05080|LOC\_Os01g57920|OG0000000
AT1G06630|LOC\_Os01g57920|OG0000000
AT1G13780|LOC\_Os01g57920|OG0000000
AT1G16930|LOC\_Os01g57920|OG0000000
AT1G16940|LOC\_Os01g57920|OG0000000
AT1G16945|LOC\_Os01g57920|OG0000000
AT1G19070|LOC\_Os01g57920|OG0000000
AT1G19410|LOC\_Os01g57920|OG0000000
AT1G21990|LOC\_Os01g57920|OG0000000
AT1G22000|LOC\_Os01g57920|OG0000000
AT1G26890|LOC\_Os01g57920|OG0000000



