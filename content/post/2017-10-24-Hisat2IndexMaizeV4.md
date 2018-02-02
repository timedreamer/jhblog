---
title:  "Prepare HISAT2 index file for maize version 4 genome"
date: 2017-10-24
tags: ["bioinformatics", "rna-seq", "maize"]
categories: ["dry lab"]
---

I tried to make new HISAT2 index for the maize version 4 genome including SNP informations. However, I had multiple issues while doing it. After an hour work, finally get it done.

<!--more-->

# Change variable names and more

In the **make_zm4_snp_tran_ercc.sh** file which provided by the HISAT2 team in their [FTP server](ftp://ftp.ccb.jhu.edu/pub/infphilo/hisat2/data), multiple variables are indicated at first. It really helps to reduce changes. But, there are also several places that need to change manually.

For example:

```
GTF_FILE=Zea_mays.AGPv4.37.gtf
F=Zea_mays.AGPv4.dna.toplevel.fa
```

# Use python 2!

One of the problem is those helper python scripts provided by the software are in Python 2 (which I did not find any documentation indicating this). The default python version on our server is Python 3.6, so the `hisat2_extract_snps_haplotypes_VCF.py` wont't work. It report the following errors:

```
TypeError: startswith first arg must be bytes or a tuple of bytes, not str
```

Luckily, the fix is easy. Just change the first line of that script to (or where your python 2 is):

```
#!/usr/bin/python2.7
```

Then it works great.

It tooks **03:43:45** for building index.
