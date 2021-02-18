---
title: Quick query R package download stats
author: Ji Huang
date: '2021-02-18'
slug: quick-query-r-package-download-stats
categories:
  - dry lab
  - misc
tags:
  - bioinformatics
  - R
  - tools
lastmod: '2021-02-18T10:49:06-05:00'
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


I found a very handy package to get R package download information. The **[dlstats](https://cran.r-project.org/web/packages/dlstats/index.html)**, from Guangchuang Yu.

<!--more-->

The package is pretty easy to use, although I had some problems when queried multiple times.
The plot functions work well.

### Load required pacakges.

```
library("dlstats")
library("ggplot2")
library("scales")
```

### Query CRAN pacakge and plot

```
plot_cran_stats(c("dplyr", "ggplot2", "scales"))+
    scale_y_continuous(labels = label_comma())+
    xlab("Time") +
    ylab("Number of download")
```

![Imgur](https://imgur.com/iYVuOge.png)

### Query Bioconductor pacakge and plot

```
plot_bioc_stats(c("limma","DESeq2", "edgeR"))+
    scale_y_continuous(labels = label_comma())+
    xlab("Time") +
    ylab("Number of download")
```

![Imgur](https://imgur.com/ctFh1lT.png)
