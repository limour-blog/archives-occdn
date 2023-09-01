---
title: 通过conda安装纯净环境的SCENIC
tags: []
id: '1611'
categories:
  - - 生信
  - - 转录因子
date: 2022-03-12 18:14:37
---

*   https://scenic.aertslab.org/tutorials/
*   https://anaconda.org/conda-forge/r-base
*   conda create -n scenic -c conda-forge r-base=4.1.2 -y
*   conda activate scenic
*   conda install -c conda-forge r-seurat=4.1.0 -y
*   conda install -c conda-forge r-irkernel=1.3 -y
*   conda install -c conda-forge r-biocmanager=1.30.16 -y
*   conda install -c conda-forge r-devtools=2.4.3 -y
*   conda install -c conda-forge --strict-channel-priority r-arrow=7.0.0 -y
*   conda install -c bioconda bioconductor-aucell=1.16.0 -y
*   conda install -c bioconda bioconductor-rcistarget -y
*   conda install -c bioconda bioconductor-complexheatmap=2.10.0 -y
*   conda install -c bioconda bioconductor-genie3=1.16.0 -y
*   conda install -c bioconda bioconductor-biocparallel=1.28.3 -y
*   conda install -c conda-forge r-doparallel=1.0.17 -y
*   conda install -c conda-forge parallel=20220222 -y
*   conda install -c conda-forge r-foreach=1.5.2 -y
*   conda install -c maximinio r-scopeloomr=0.3.1 -y
*   conda install -c conda-forge r-rbokeh=0.5.2 -y
*   conda install -c conda-forge r-nmf=0.21.0 -y
*   wget https://github.com/aertslab/SCENIC/archive/refs/heads/master.zip -O SCENIC-master.zip
*   BiocManager::install(c("NMF", "R2HTML"))
*   BiocManager::install(c("doMC", "doRNG"))
*   devtools::install\_local("SCENIC-master.zip")
*   IRkernel::installspec(name='scenic', displayname='r-scenic')

```shell
mkdir RcisTarget_data && cd RcisTarget_data
mkdir human && cd human
wget https://resources.aertslab.org/cistarget/databases/homo_sapiens/hg19/refseq_r45/mc9nr/gene_based/hg19-500bp-upstream-7species.mc9nr.feather
wget https://resources.aertslab.org/cistarget/databases/homo_sapiens/hg19/refseq_r45/mc9nr/gene_based/hg19-tss-centered-10kb-7species.mc9nr.feather

wget https://resources.aertslab.org/cistarget/databases/homo_sapiens/hg38/refseq_r80/mc9nr/gene_based/hg38__refseq-r80__10kb_up_and_down_tss.mc9nr.feather
wget https://resources.aertslab.org/cistarget/databases/homo_sapiens/hg38/refseq_r80/mc9nr/gene_based/hg38__refseq-r80__500bp_up_and_100bp_down_tss.mc9nr.feather
```

[总体来说，hg38相对于 hg19是一个巨大进步](https://www.zhihu.com/question/317951769/answer/1451116214)。by [员炊事](https://www.zhihu.com/people/csyk2)

1.  **改变了之前的一些测序错误，组装错误**。直观的例子有 degenerate bases 少了很多。
2.  **补上了hg19中的很多gap**。 特别重要的是 centromere 的序列也不再是空白。hg19的时候，centromere 的部分直接就是gap， hg38后，开始有了 a-satellitle sequences。尽管只是“简单”的表现了 a-satellite repeats， 已经是一个巨大的提升。
3.  sequence 改了之后，**相应的 annotation 也改了很多**。比较宏观的就是 hg38 的 exome 比hg19 的大了不少。如果我的记忆没出错的话，大了25% 左右。

```r
dbs <- list(`500bp`='hg38__refseq-r80__500bp_up_and_100bp_down_tss.mc9nr.feather', `10kb`='hg38__refseq-r80__10kb_up_and_down_tss.mc9nr.feather')
scenicOptions <- initializeScenic(org="hgnc", dbDir="~/upload/zl_liu/RcisTarget_data/human/", nCores=10, dbs = dbs)
```

```r
library(foreach)
library(parallel)
library(doParallel)
library(BiocParallel)
```