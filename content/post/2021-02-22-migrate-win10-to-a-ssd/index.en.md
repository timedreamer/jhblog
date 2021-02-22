---
title: Migrate Win10 to a SSD
author: Ji Huang
date: '2021-02-22'
slug: migrate-win10-to-a-ssd
categories:
  - dry lab
tags:
  - misc
lastmod: '2021-02-22T00:35:24-05:00'
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

I bought a [SSD](https://www.amazon.com/gp/product/B07SK5BNM1?psc=1) for my Dell server. I installed it on my Dell server and migrate Win10 from the old HDD to the new SSD. Here are the steps.


<!--more-->

1. Install the SSD in the desktop. The connection is SATA-based.

2. Download [AOMEI Partition Assistant](https://www.diskpart.com/free-partition-manager.html). 中文：[傲梅分区助手](https://www.disktool.cn/). There is an option in the tool for *Migrate OS to SSD*. Just follow the instructions. For me, the T30 has RAID, so I need to use the PreOS migration.

3. Go to BIOS to change the boost order. Select boosting from SSD as the first option. I kept the old system disk. You can remove it to free space if you want to.

4. Restart computer and you are done.

5. I installed the [CrystalDiskInfo8_11_1](https://osdn.net/projects/crystaldiskinfo/) to check the SSD info. Below is the screenshot.

![Imgur](https://imgur.com/AxoHFb4.png)

6. I installed the [AS SSD Benchmark](https://as-ssd-benchmark.en.softonic.com/) for SSD benchmark. Below is the screenshot. [CrystalDiskMark](https://osdn.net/projects/crystaldiskmark/) is an alternative. 

![Imgur](https://imgur.com/qXIUhOx.png)

The performace is great so far. I can feel the Win10 is much faster now.

