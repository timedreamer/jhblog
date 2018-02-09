---
title: Download processed RNA-Seq data from EBI 
author: Ji Huang
date: '2018-02-09'
slug: download-processed-rna-seq-data
categories:
  - dry lab
tags:
  - rna-seq
  - bioinformatics
lastmod: '2018-02-09T14:13:39-05:00'
keywords: [rna-seq, bioinformatics, embl, gene expression]
description: ''
comment: yes
toc: yes
autoCollapseToc: no
postMetaInFooter: no
contentCopyright: no
reward: no
mathjax: no
mathjaxEnableSingleDollar: no
---

For my gene network projects, a majority of my time was spent on manually curate RNA-Seq data on SRA, download SRA data and align them on the maize genome. This week I found an API or resouece from EMBL, [RNASeq-er API][1] that already alligned RNA-Seq reads on genome for you. 

The paper ([The RNASeq-er API—a gateway to systematically updated analysis of public RNA-seq data][5] was published on Bioinformatics, 2017.

This post is to show how to do some basic search and download using the API. I followed steps on API's [document][1].

<!--more-->

# 1. What is RNASeq-er?

RNASeq-er is an API that allow users to download processed RNA-Seq files, including meta-data, cram and processed gene experssion data. It uses [iRAP pipeline][2]. It uses TopHat2 and HTSeq2/DEXSeq based on Ensemble genome assembly. See below flowchat.

![image](https://www.ebi.ac.uk/fg/rnaseq/api/resources/Figure2_iRAP.png)


# 2. Usage exmples

Here I will use maize RNA-Seq study **[SRP047420][4]** as an example. This study was published by Gent et al 2014 [Plant Cell paper][3]. It contains 6 RNA-Seq libraries, mop1-wt vs mop1-mut. Based on the information, all sequences were aligned to AGPv4.

## 2.1 How many plant species in the database?

You can run `wget` or `curl` in Linux console to download the table, or just use plain **http** and search in Chrome as I did.

**https://www.ebi.ac.uk/fg/rnaseq/api/tsv/0/getOrganisms/plants**

The result is like below, you will get a tsv table with a) Taxomic name of the organism and b) name of the organism used as reference genome. The `/0` in the url is the minimum percentage of reads mapped to the reference genome.

![Imgur](https://i.imgur.com/TGkpUe3.jpg)


## 2.2 Sample meta-data for one study.

You can get the meta-data for one study by searchinng: **https://www.ebi.ac.uk/fg/rnaseq/api/tsv/getSampleAttributesPerRunByStudy/SRP047420**

But, I found it's not very easy to extract useful information. Maybe JSON format will be better.

![Imgur](https://i.imgur.com/pJ5dAIa.jpg)


## 2.3 Alignment files for one study

```shell
wget https://www.ebi.ac.uk/fg/rnaseq/api/tsv/70/getRunsByStudy/SRP047420
```

You will get Sample IDs, Mapping quality, genome version and download files FTP address (including cram, bedgraph and bigwig format).

![Imgur](https://i.imgur.com/b1a5Mun.jpg)

Actually, if you go to the FTP site of one library, for example SRR1583909, there are all files downloadable. These files include gene/transcript level expression, exon level expression, kallisto result and quality control result.  

![Imgur](https://i.imgur.com/PzANi4K.jpg)


## 2.4 Alignment files for one organism

You can get FTP addresses for all aligned files for one organism (at 70% aligned on genome).

```shell
wget https://www.ebi.ac.uk/fg/rnaseq/api/tsv/70/getRunsByOrganism/zea_mays
```

On 2018-02-09, there all 7147 libraries in maize that have been processed. The last line did not have `LF`, so if use `wc -l`, there was one line less.

![Imgur](https://i.imgur.com/Ps4IP5N.jpg)


## 2.5 Gene expression data for one study.

To get gene expresion data for one study:

Search: https://www.ebi.ac.uk/fg/rnaseq/api/tsv/getStudy/SRP047420

![Imgur](https://i.imgur.com/yEOZX8o.jpg)

You can also find other types of gene expression data from the FTP site (ftp://ftp.ebi.ac.uk/pub/databases/arrayexpress/data/atlas/rnaseq/studies/ena/SRP047420/zea_mays)

![Imgur](https://i.imgur.com/fpqpNTW.jpg)

p
## 2.6 Gene expression data for one organism.

You can also get all gene expression files for one specific organism, for example **zea_mays**. I did not see kallisto result in the table, but you can download based on the FTP from 2.2.

```shell
wget https://www.ebi.ac.uk/fg/rnaseq/api/tsv/getStudiesByOrganism/zea_mays
```

In total there are **661** studies in maize.

![Imgur](https://i.imgur.com/twgnxpM.jpg)


## 2.7 Mapping quality for all organisms

Search: **https://www.ebi.ac.uk/fg/rnaseq/api/tsv/getOrganismsMappingQuality**


# 3. Summary

I think this is awesome that EBI already processed the gene expression data FOR YOU! The only thing that can improve is to use HISAT2/STAR instead of TopHat2, since TopHat is out-dated.








[1]:https://www.ebi.ac.uk/fg/rnaseq/api/index.html#IntroductionRNA-seqAnalysisAPI
[2]:https://github.com/nunofonseca/irap
[3]:http://www.plantcell.org/content/26/12/4903
[4]:https://trace.ncbi.nlm.nih.gov/Traces/study/?acc=SRP047420
[5]:https://academic.oup.com/bioinformatics/article/33/14/2218/3078600