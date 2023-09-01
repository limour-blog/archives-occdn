---
title: 通过conda安装纯净环境的Seurat
tags: []
id: '1534'
categories:
  - - 环境
  - - 生信
date: 2022-02-22 19:38:30
---

*   conda create -n seurat -c conda-forge r-base=4.1.2
*   conda activate seurat
*   conda install -c conda-forge r-seurat=4.1.0 -y
*   conda install -c conda-forge r-tidyverse=1.3.1 -y
*   conda install -c conda-forge r-irkernel -y
*   R
*   IRkernel::installspec(name='seurat', displayname='r-seurat')
*   q()