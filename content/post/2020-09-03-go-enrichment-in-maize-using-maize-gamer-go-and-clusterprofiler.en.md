---
title: GO enrichment in maize using Maize-GAMER GO and clusterProfiler
author: Ji Huang
date: '2020-09-03'
slug: go-enrichment-in-maize-using-maize-gamer-go-and-clusterprofiler
categories:
  - dry lab
tags:
  - bioinformatics
  - maize
  - gene ontology
lastmod: '2020-09-03T12:41:54-04:00'
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

I've been using g:Profiler for GO enrichment for a while. It works great. Bundled with [gprofiler2](https://cran.r-project.org/web/packages/gprofiler2/index.html) R package, I can do GO enrichment analysis for many species within R.

Today, I wanted to try the [Maize-GAMER GO annotation](https://onlinelibrary.wiley.com/doi/full/10.1002/pld3.52). And this post is to show how you can use user-defined annotation in clusterProfiler.

<!--more-->

See code below.

```r
library(tidyverse)
library(clusterProfiler)

# Load GAMER.
zmaGO <- readr::read_tsv("https://github.com/timedreamer/public_dataset/raw/master/maize.B73.AGPv4.aggregate.gaf.gz", skip=1) %>% select(term_accession, db_object_id)

# Load GO_ID to GO_annotation mapping.
GOname <- readr::read_tsv("https://github.com/timedreamer/public_dataset/raw/master/agriGOv2_GOConsortium_term_v201608.txt.gz", col_names = c("GO","type","name","number")) %>% select(GO, name)

# Run GO enrichment.

ego <- enricher(gene = unique(zmaGO$db_object_id[1:100]), 
                TERM2GENE =  zmaGO, TERM2NAME = GOname)
head(ego)
barplot(ego, showCategory=20)
```

PS: 

1. When loading GAMER, there is a warning `Warning: 2 parsing failures.`. You can ignore it.
2. I also tried `go.obo`from *http://geneontology.org/docs/download-ontology/*. More GOs overlap with GAMER in the AgriGO mapping than this one. So I go with AgriGO (see below for the numbers). 

```r
# Some quick check. Skip.
## A few GOs in GAMER are not in the annotation mapping.
## How many GOs in GAMER?
length(unique(zmaGO$term_accession)) # 9336
# length(unique(GOname$GO))
## How many GOs overlap between GAMER and annotation mapping? 
length(intersect(unique(zmaGO$term_accession), unique(GOname$GO))) # 9304
```

3. To parse the `go.obo` file, I used [sgrote/OboToTerm](https://github.com/sgrote/OboToTerm). Works very well. 

