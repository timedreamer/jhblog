---
title:  "Simple Boxplot in R "
date: 2017-03-27
lastmod: 2018-02-02
draft: false
tags: ["R", "visualization"]
categories: ["dry lab"]
---

In last post, I downloaded TCGA 309 normalized RNA-Seq data for OV tumor tissue.This time, I'm going to draw simple boxplot from this expression tissue.

<!--more-->

I will show two solutions here, one with base R and base plot functions, the other using the so-called [tidy data](https://bit.ly/1V7jmkY), pipe and ggplot2.

# Base R

Here the only function not base is `str_split_fixed` which imported from "stringr". This makes split so easy, but you can also use `strsplit()`` for this purpose. Of course you can adjust parameters to make them look better.

```r
#load required pacakges.
library("stringr");library("tidyr")
library(dplyr);library(reshape2)
library(ggplot2);library(gridExtra)
# read file and draw boxplot of interested genes
ov <- read.table("OV__gene.normalized_RNAseq__tissueTypeAll__20170308150904.txt",header=T,stringsAsFactors = F)
# splite gene id to "geneName" and "EntrezID". from stingr
t <- str_split_fixed(ov$gene_id, "\\|",n=2)
ov <- cbind(t,ov)
ov <- ov[,-3];rm(t) # combine splite name and original values
colnames(ov)[1:2] <- c("geneName","EntrezID")

# get expression for genes we want.
expr <- ov[ov$EntrezID %in% c("672","10575"),]

# plot use base R boxplot() function
par(mfrow=c(1,2))
boxplot(t(expr[,c(3:311)]),names = expr[,1],main="beforeLog2")
boxplot(log2(t(expr[,c(3:311)])+1),names=expr[,1],main="afterLog2")

# plot use ggplot2
expr_m <- melt(expr,id.vars =c("geneName","EntrezID"))
p1 <- ggplot(data=expr_m,aes(x=geneName,y=value)) + geom_boxplot() +
ggtitle("beforeLog2")
p2 <- ggplot(data=expr_m,aes(x=geneName,y=log2(value+1)))+ geom_boxplot() +
ggtitle("afterLog2")
grid.arrange(p1, p2, ncol = 2)
```


<pre class="GGHFMYIBMOB">gene_id TCGA.04.1348.01A.01R.1565.13 TCGA.04.1357.01A.01R.1565.13
1 ?|100130426                       0.0000                       0.0000
2 ?|100133144                      27.3245                      21.9661
3 ?|100134869                      45.8134                      36.6276
4     ?|10357                      24.0979                       9.1146
5     ?|10431                    1509.2767                     696.6146
  TCGA.04.1362.01A.01R.1565.13 TCGA.04.1364.01A.01R.1565.13
1                       0.6619                       0.0000
2                      27.2611                      18.6062
3                      53.1619                      14.4677
4                      32.1030                      30.2498
5                    1056.4202                    1372.5640</pre>
<pre id="rstudio_console_output" class="GGHFMYIBMOB">20531   310</pre>

![Imgur](https://i.imgur.com/qGtQsNU.png)

![Imgur](https://i.imgur.com/REEsv5O.png)

# Tidy and Pipe

Probably because I have used base function for too long, that the tidy format at first doesn't make sense to me. After read [Mastering Software Development in R](https://bookdown.org/rdpeng/RProgDA/), I finally got some ideas that I could implement the pipe. Here is the code:

```r
# To do the samething in pipe or the so called tidy data
ov1 <- read_tsv("OV__gene.normalized_RNAseq__tissueTypeAll__20170308150904.txt") %>%
 separate(gene_id,c("geneName","EntrezID"),"\\|") %&>%
 filter(EntrezID %in% c("672","10575")) %>%
 select(-EntrezID) %>%
 gather(sample,value,-geneName)
 #mutate(value=log2(value+1))

# draw violin plot in ggplot2
p3 <- ggplot(data=ov1,aes(x=geneName,y=value))+ geom_violin() +
 ggtitle("beforeLog2")
p4 <- ggplot(data=ov1,aes(x=geneName,y=log2(value+1)))+ geom_violin() +
 ggtitle("afterLog2")
grid.arrange(p3,p4,ncol=2)
```

![Imgur](https://i.imgur.com/xjsWjg4.png)

# Speed test and memory usage

I also did a speed test for two different code. BaseR took **11.962s**, pipe took **2.51s**, pipe is much faster. I did not do microbenhmark, but it looks like read_table is very fast.

In the end I compared three object memory use, all with two genes' expression in 309 tissues.

1. base R processed dataframe (expr)
2. melt base R dataframe (expr_m)
3. tibble from pipe (ov1)

```r
library(pryr)
obsize <- c(object_size(expr),object_size(expr_m),object_size(ov1))
names(obsize) <- c("expr","expr_m","ov1")
barplot((obsize/1e6),ylim=c(0,3),las=2,ylab="MB",xlab="object",main="memoryUsage")    
```

![Imgur](https://i.imgur.com/onFq8HS.png)

**Ov1 only takes 38.2k! That's impressive. Why? Next time i will test on a bigger dataset.**

| expr | expr_m | ov1 |
|----------|:-------------:|------:|
| 2.37MB | 2.36MB| 38.2kB |
