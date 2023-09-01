---
title: 通过conda安装纯净环境的R绘图合集
tags: []
id: '1561'
categories:
  - - 生信
  - - 绘图
date: 2022-02-24 21:14:49
---

*   conda create -n rplot -c conda-forge r-base=4.1.2 -y
*   conda activate rplot
*   conda install -c conda-forge r-seurat=4.1.0 -y
*   conda install -c conda-forge r-irkernel=1.3 -y
*   R
*   IRkernel::installspec(name='rplot', displayname='r-plot')
*   q()

## 火山图

*   conda install -c bioconda bioconductor-enhancedvolcano=1.12.0 -y

## 和弦图

*   conda install -c conda-forge r-circlize=0.4.14 -y

## 热图

*   conda install -c bioconda bioconductor-complexheatmap=2.10.0 -y
*   conda install -c conda-forge r-pheatmap=1.0.12 -y

## 调色板

*   conda install -c conda-forge r-ggsci=2.9 -y

## iRGSEA

*   conda install -c conda-forge r-biocmanager=1.30.16 -y
*   conda install -c conda-forge r-devtools=2.4.3 -y
*   conda install -c conda-forge r-domc=1.3.8 -y
*   R

```r
cran.packages <- c("msigdbr", "dplyr", "purrr", "stringr","magrittr",
                   "RobustRankAggreg", "tibble", "reshape2", "ggsci",
                   "tidyr", "aplot", "ggfun", "ggplotify", "ggridges",
                   "gghalves", "Seurat", "SeuratObject", "methods",
                   "devtools", "BiocManager","data.table","doParallel",
                   "doRNG")
install.packages(cran.packages, ask = F, update = F)
bioconductor.packages <- c("GSEABase", "AUCell", "SummarizedExperiment",
                           "singscore", "GSVA", "ComplexHeatmap", "ggtree",
                           "Nebulosa")
BiocManager::install(bioconductor.packages, ask = F, update = F)
devtools::install_local('~/github_repo/UCell-master.zip')
devtools::install_local('~/github_repo/irGSEA-master.zip')
```

## DIY

*   conda install -c conda-forge r-tidyverse=1.3.1 -y
*   conda install -c conda-forge r-ggdendro=0.1.23 -y
*   conda install -c conda-forge r-ggpubr=0.4.0 -y