---
title: conda安装纯净环境的clusterprofiler
tags: []
id: '1617'
categories:
  - - 生信
  - - 通路富集
date: 2022-03-16 12:50:28
---

*   conda create -n clusterprofiler -c conda-forge r-base=4.1.2 -y
*   conda activate clusterprofiler
*   conda install -c conda-forge r-seurat=4.1.0 -y
*   conda install -c conda-forge r-irkernel=1.3 -y
*   conda install -c conda-forge r-biocmanager=1.30.16 -y
*   conda install -c conda-forge r-devtools=2.4.3 -y
*   conda install -c conda-forge r-forcats=0.5.1 -y
*   conda install -c conda-forge r-tidyverse=1.3.1 -y
*   BiocManager::install("clusterProfiler")
*   BiocManager::install("org.Hs.eg.db")
*   IRkernel::installspec(name='clusterprofiler', displayname='r-clusterprofiler')