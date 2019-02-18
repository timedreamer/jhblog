---
title: Fisher's exact test using GeneOverlap
author: Ji Huang
date: '2019-02-01'
slug: fisher-s-exact-test-using-geneoverlap
categories:
  - dry lab
tags:
  - R
  - bioinformatics
  - statistics
lastmod: '2019-02-18'
keywords: []
description: ''
comment: yes
toc: no
autoCollapseToc: no
postMetaInFooter: no
contentCopyright: no
reward: no
mathjax: no
mathjaxEnableSingleDollar: no
---

<!--more-->

I use the [Fisher's exact test](https://en.wikipedia.org/wiki/Fisher%27s_exact_test) a lot to find whether two gene sets are significantly overlap. It is essentially the same as the [Hypergeometric test](https://en.wikipedia.org/wiki/Hypergeometric_distribution). As said in the wiki page:

> Relationship to Fisher's exact test
The test based on the hypergeometric distribution (hypergeometric test) is identical to the corresponding one-tailed version of Fisher's exact test). Reciprocally, the p-value of a two-sided Fisher's exact test can be calculated as the sum of two appropriate hypergeometric tests (for more information see)


The Fisher's exact test starts with the contingency table which similar with chi-squared test. Previously, I've been manually made the contingency table and did the test using `fisher.test` in R. However, I found this method is cumbersome and error prone. 

In this post, I found the [**GeneOverlap**](https://www.bioconductor.org/packages/release/bioc/html/GeneOverlap.html) package in R can do this job much more elegant. The nice thing is: it can also do the test for two `list` and have a convenient plotting function. More info is in the [manual](https://bioconductor.org/packages/release/bioc/vignettes/GeneOverlap/inst/doc/GeneOverlap.pdf).

---

The test only needs two sets of genes (They can be `lists` even) and the number of genes in the background. For me, I like to use genes expressed in the RNA-Seq experiment instead of all genes in the genome. 

So in the code next, I have a `background` that contains all the 22000 genes that past the filter. `DE_master` and `test_list` are two `lists` with 12 and 4 gene sets. Now I can do the test in a few lines. The heatmap is a quick and dirty way, but may not be publication ready.

```r
# load library
library(GeneOverlap)

# Do the testing
gom.obj <- newGOM(test_list, DE_master, genome.size = length(background))
getMatrix(gom.obj, name="pval")

# Plot the heatmap
drawHeatmap(gom.obj)
```

You can also compare a `list` to itself.

```r
gom.self <- newGOM(DE_master, genome.size = length(background))
drawHeatmap(gom.self)
getMatrix(gom.obj, name="pval")
```

If comparing two vector, just do:

```r
go.obj <- newGeneOverlap(DE_master$wg, test_list$M1)
go.obj <- testGeneOverlap(go.obj)
go.obj
```

There are also other functions that are quite useful, like `getNestedList` which:

> accessor can get gene lists for each comparison as a nested list: the outer
list represents the columns and the inner list represents the rows

In this way, I don't need to do `intersect()` repeatedly.

Overall, this is a really nice small package. I'm going to use it in my workflow regularly.

## 2019-02-18 Update

I found by default, the p-values reported by `getMatrix` are without adjustment. So I wrote a handy function `function_02_geneOverlap_adjusted_pvalue_matrix.R` to adjust multiple testing by "BH".

The function returns the adjusted pvalue matrix with the same row/column names. The significant values were set to `1` and non-significant values set to `0`. This helps me to plot the heatmap. You can also remove the `pv_adj <- 1* (pv_adj < cut_off)` line, so the function will return the adjusted pvalue matrix with the adjusted p-value numbers.

```function
pval_adj <- function(list1, list2, genome.size, cut_off){
    gom.obj <- newGOM(list1, list2, genome.size)
    pv_raw <- getMatrix(gom.obj, name="pval")

    # adjust p-values by "BH".
    pv_adj <- matrix(p.adjust(pv_raw, method = "BH"),nrow = nrow(pv_raw))
    
    # set the cut-off value for significance.
    pv_adj <- 1* (pv_adj < cut_off)
    
    colnames(pv_adj) <- colnames(pv_raw)
    rownames(pv_adj) <- rownames(pv_raw)

    return(pv_adj)
}
```

To use the function, simply call the function. The result can be visualized by `pheatmap`.

```r
source("src/function_02_geneOverlap_adjusted_pvalue_matrix.R")

tt1 <- pval_adj(test_list1, test_list2, genome.size = 22000,
                cut_off = 0.01)
pheatmap(t(tt1), cluster_cols = F, cluster_rows = F,
         fontsize = 20,cellwidth = 30, angle_col = "45",
         main = "test_lists with p-value adjustment")

```


