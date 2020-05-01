---
title: Resources for rice gene ontology annotations
author: Ji Huang
date: '2020-05-01'
slug: resources-for-rice-gene-ontology-annotations
categories:
  - dry lab
tags:
  - rice
  - gene ontology
lastmod: '2020-05-01T15:19:30-04:00'
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

I've been doing some analysis in rice for a while. Here is a short post on some resources for the rice GO annotation. Many of them also contain GO info for other species.

<!--more-->

1. [Rice Genome Annotation Project](http://rice.plantbiology.msu.edu/index.shtml) aka *MSU7*. You can download the *GOSlim assignment* from its [FTP website](http://rice.plantbiology.msu.edu/pub/data/Eukaryotic_Projects/o_sativa/annotation_dbs/pseudomolecules/version_7.0/all.dir/).

2. [agriGO v2.0](http://systemsbiology.cau.edu.cn/agriGOv2/). It allows you to do GO analysis online. You can also download the GO annotaion files from its [website](http://systemsbiology.cau.edu.cn/agriGOv2/download.php).

3. [PLAZA](https://bioinformatics.psb.ugent.be/plaza/). Similar with agriGO, PLAZA has many online analysis tools. The functional annotation files can be downloaded from PLAZA [website](https://bioinformatics.psb.ugent.be/plaza/versions/plaza_v4_5_monocots/download), including GO and InterPro. The PLAZA 4.0 also has Mapman.

4. [Ensembl Plant](https://plants.ensembl.org/index.html). The GO, KEGG and some other annotaions can be accessed from online biomart. Or [biomaRt](https://bioconductor.org/packages/release/bioc/html/biomaRt.html) and [biomartr](https://cran.r-project.org/web/packages/biomartr/index.html) R packages. Ensembl Plant and Gramene uses RAPDB IDs only.

5. [Phytozome](https://phytozome-next.jgi.doe.gov/). Similar with Ensembl Plant. It uses MSU7 IDs only. It's also possible to access GO from PhytzoMine online or through its API.

6. [g:Profiler](https://biit.cs.ut.ee/gprofiler/page.cgi?welcome). I use this for rice most of the time. The backend data is based on Ensembl Plant, so RAPDB IDs only. It has easy to use webiste and [gprofiler2 pacakge](https://cran.r-project.org/web/packages/gprofiler2/index.html).

7. [AnnotationHub](https://bioconductor.org/packages/release/bioc/html/AnnotationHub.html). The AnnotationHub contains four databases related to *Oryza Sativa*. Three OrgDB and one Inparanoid8Db. Not sure what's the difference among three OrgDB. They all uses *ENTREZID* and *REFSEQ*.

![](https://i.imgur.com/JhhDxqM.png?1)

