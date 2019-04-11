---
title: Build STAR and kallisto index for rice MSU7 annotation
author: Ji Huang
date: '2019-04-10'
slug: build-star-and-kallisto-index-for-rice-msu7-annotation
categories:
  - dry lab
tags:
  - bioinformatics
  - rice
lastmod: '2019-04-10T20:11:17-04:00'
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

This post is to show how to make [STAR](https://github.com/alexdobin/STAR) and [kallisto](https://pachterlab.github.io/kallisto/) index for [rice MSU7 annotations](http://rice.plantbiology.msu.edu/pub/data/Eukaryotic_Projects/o_sativa/annotation_dbs/pseudomolecules/version_7.0/all.dir/).

<!--more-->

## 1. Download genome files.

```shell
# genome sequence
wget http://rice.plantbiology.msu.edu/pub/data/Eukaryotic_Projects/o_sativa/annotation_dbs/pseudomolecules/version_7.0/all.dir/all.con

# gff3 annotation
wget http://rice.plantbiology.msu.edu/pub/data/Eukaryotic_Projects/o_sativa/annotation_dbs/pseudomolecules/version_7.0/all.dir/all.gff3

# cdna sequences
wget http://rice.plantbiology.msu.edu/pub/data/Eukaryotic_Projects/o_sativa/annotation_dbs/pseudomolecules/version_7.0/all.dir/all.cdna
```

## 2. Convert GFFF3 to GTF

```shell
module load cufflinks/2.2.1
gffread all.gff3 -T -o all.gtf
```

## 3. Make kallisto index

```shell
module load kallisto/intel/0.43.0
kallisto index -i osj_msu7_kallisto.idx all.cdna
```

## 4. Build STAR index.

I submitted a slurm job, `STAR_genome_index_rice.slurm` showing below.

```shell
#!/bin/bash
#SBATCH --output=/scratch/cgsb/coruzzi/jh6577/GenomeFile/rice_MSU7/STAR_index/STAR_genome_build_%A_%a.out
#SBATCH --error=/scratch/cgsb/coruzzi/jh6577/GenomeFile/rice_MSU7/STAR_index/STAR_genome_build_%A_%a.err
#SBATCH -J STAR_genome_build
#SBATCH --cpus-per-task=12
#SBATCH --mem=40GB
#SBATCH --mail-type=END
##SBATCH --mail-user=jh6577@nyu.edu

module purge
module load star/intel/2.5.3a

STAR --runMode genomeGenerate --runThreadN 12 --genomeDir //scratch/cgsb/coruzzi/jh6577/GenomeFile/rice_MSU7/STAR_index/ --genomeFastaFiles /scratch/cgsb/coruzzi/jh6577/GenomeFile/rice_MSU7/all.con --sjdbGTFfile /scratch/cgsb/coruzzi/jh6577/GenomeFile/rice_MSU7/all.gtf
```

