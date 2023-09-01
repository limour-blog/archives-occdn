---
title: 通过conda安装纯净环境的CellChat
tags: []
id: '1612'
categories:
  - - 生信
  - - 细胞通讯
date: 2022-03-12 17:52:55
---

*   https://anaconda.org/conda-forge/r-base
*   conda create -n cellchat -c conda-forge r-base=4.1.2 -y
*   conda activate cellchat
*   conda install -c conda-forge r-seurat=4.1.0 -y
*   conda install -c conda-forge r-irkernel=1.3 -y
*   conda install -c conda-forge r-biocmanager=1.30.16 -y
*   conda install -c conda-forge r-devtools=2.4.3 -y
*   conda install -c conda-forge r-circlize=0.4.14 -y
*   conda install -c conda-forge umap-learn=0.5.3 -y
*   conda install -c bioconda bioconductor-biobase=2.54.0 -y
*   wget https://github.com/sqjin/CellChat/archive/refs/heads/master.zip -O CellChat-master.zip
*   **移除CellChat-master.zip里src目录下的\*.so和\*.o文件**
*   unzip CellChat-master.zip
*   rm CellChat-master/src/\*.so
*   rm CellChat-master/src/\*.o
*   wget https://github.com/jokergoo/ComplexHeatmap/archive/refs/heads/master.zip -O ComplexHeatmap-master.zip
*   conda install -c conda-forge r-svglite=2.1.0 -y
*   install.packages('NMF')
*   devtools::install\_local('ComplexHeatmap-master.zip')
*   devtools::install\_local('CellChat-master')
*   IRkernel::installspec(name='cellchat', displayname='r-cellchat')

```r
Sys.setenv(RETICULATE_PYTHON = "/opt/conda/envs/cellchat/bin/python3.7")
library('reticulate')
use_condaenv("cellchat")
py_config()
```