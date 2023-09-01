---
title: STAR：构建小鼠的基因组索引
tags: []
id: '1932'
categories:
  - - 组织测序
date: 2022-07-02 10:56:40
---

## 来源

[https://www.gencodegenes.org/mouse/](https://www.gencodegenes.org/mouse/)

[https://www.ncbi.nlm.nih.gov/genome/browse#!/overview/](https://www.ncbi.nlm.nih.gov/genome/browse#!/overview/)

[http://asia.ensembl.org/info/about/species.html](http://asia.ensembl.org/info/about/species.html)

## 步骤

```sh
zcat XL789vs123/XL1/default_s.fq.gz  head #这个例子只有50, 因此 sjdbOverhang 为49
 
wget https://ftp.ebi.ac.uk/pub/databases/gencode/Gencode_mouse/release_M29/GRCm39.primary_assembly.genome.fa.gz
wget https://ftp.ebi.ac.uk/pub/databases/gencode/Gencode_mouse/release_M29/gencode.vM29.primary_assembly.annotation.gtf.gz
 
gunzip *.gz
 
conda activate star
nano 2.sh
chmod +x 2.sh
 
#!/bin/sh
STAR \
--runMode genomeGenerate \
--genomeDir index \
--genomeFastaFiles GRCm39.primary_assembly.genome.fa \
--sjdbOverhang 49 \
--sjdbGTFfile gencode.vM29.primary_assembly.annotation.gtf \
--runThreadN 8
```

*   human和mouse推荐从gencode上下载
*   其他物种可以从NCBI上下载，也可以从ENSEMBL上下载，需要参考基因组fasta文件和对应的gtf注释