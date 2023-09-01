---
title: 通过conda安装纯净环境的CellTypist
tags: []
id: '1539'
categories:
  - - 分群
  - - 生信
date: 2022-02-22 21:42:34
---

*   conda create -n celltypist -c conda-forge r-base=4.1.2
*   conda activate celltypist
*   conda install -c conda-forge r-seurat=4.1.0 -y
*   conda install -c conda-forge r-irkernel=1.3 -y
*   IRkernel::installspec(name='celltypist', displayname='r-celltypist')
*   conda install -c conda-forge scanpy=1.8.2 -y
*   /opt/conda/envs/celltypist/bin/pip3 install celltypist -i https://pypi.tuna.tsinghua.edu.cn/simple
*   conda install -c conda-forge r-reticulate=1.24 -y
*   python3
*   import celltypist
*   celltypist.models.download\_models(force\_update = False)