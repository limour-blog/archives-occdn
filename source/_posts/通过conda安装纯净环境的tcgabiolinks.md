---
title: 通过conda安装纯净环境的TCGAbiolinks
tags: []
id: '1653'
categories:
  - - 数据库
  - - 生信
date: 2022-03-30 23:27:08
---

*   conda create -n tcga -c conda-forge r-base=4.1.2 -y
*   conda activate tcga
*   conda install -c conda-forge r-rvest=1.0.2 -y
*   conda install -c conda-forge r-xml=3.99\_0.8 -y
*   conda install -c conda-forge r-rcpparmadillo=0.10.8.1.0 -y
*   conda install -c conda-forge r-bh=1.78.0\_0 -y
*   conda install -c conda-forge r-biocmanager=1.30.16 -y
*   conda install -c bioconda bioconductor-summarizedexperiment=1.24.0 -y
*   conda install -c bioconda bioconductor-tcgabiolinks=2.22.1 -y
*   conda install -c bioconda bioconductor-deseq2=1.34.0 -y
*   conda install -c bioconda bioconductor-rhdf5=2.38.0 -y
*   conda install -c bioconda bioconductor-limma=3.50.1 -y
*   conda install -c bioconda bioconductor-apeglm=1.16.0 -y
*   conda install -c bioconda r-sleuth=0.30.0 -y
*   conda install -c bioconda r-wasabi=1.0.1 -y
*   conda install -c conda-forge r-irkernel=1.3 -y
*   conda install -c conda-forge r-ashr=2.2\_54 -y
*   conda install -c conda-forge r-robustrankaggreg=1.1 -y
*   conda install -c conda-forge r-devtools=2.4.3 -y
*   IRkernel::installspec(name='tcga', displayname='r-tcga')

*   deseq2 数据要求：低生物学重复 & raw counts；假定负二项分布；适合高通量测序数据
*   sleuth 数据要求：**Kallisto**输出的结果
*   limma 数据要求：logCPM；假定正态分布；适合芯片数据
*   [fpkm数据差异基因分析](https://bioconductor.org/packages/release/bioc/vignettes/limma/inst/doc/usersguide.pdf) ：理论上是不能进行分析的，无计可施时可以参考
*   高生物学重复请直接使用 **wilcox.test** 以避免大量假阳性
*   多数据集结果整合：RobustRankAggreg

fpkm转tpm示例（基于 SummarizedExperiment 数据框架）

```r
fpkmToTpm <- function(fpkm){
    exp(log(fpkm) - log(sum(fpkm)) + log(1e6))
}
f_fpkmToTpm <- function(l_e){
     apply(l_e,2,fpkmToTpm)
}
assay(sce, "TPM") <- f_fpkmToTpm(assay(sce, "HTSeq - FPKM"))
```