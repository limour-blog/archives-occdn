---
title: 通过conda安装纯净环境的imputation
tags: []
id: '1688'
categories:
  - - 分群
  - - 生信
date: 2022-04-18 13:03:11
---

[https://genomebiology.biomedcentral.com/articles/10.1186/s13059-020-02132-x](https://genomebiology.biomedcentral.com/articles/10.1186/s13059-020-02132-x)

## SAVER-X

[https://github.com/jingshuw/](https://github.com/jingshuw/SAVERX)[SAVERX](https://github.com/jingshuw/SAVERX)

*   conda create -n imputation -c conda-forge r-base=4.1.3 -y
*   conda activate imputation
*   conda install -c conda-forge r-seurat=4.1.0 -y
*   conda install -c conda-forge r-tidyverse=1.3.1 -y
*   conda install -c conda-forge r-biocmanager=1.30.16 -y
*   conda install -c conda-forge r-devtools=2.4.3 -y
*   conda install -c conda-forge r-reticulate=1.24 -y
*   conda install -c conda-forge r-irkernel=1.3 -y
*   wget https://github.com/jingshuw/SAVERX/archive/refs/heads/master.zip -O SAVERX-master.zip
*   devtools::install\_local("SAVERX-master.zip")
*   IRkernel::installspec(name='imputation', displayname='r-imputation')
*   \# IO错误 请修改xui2.json内的DNS服务器ip
*   ~/dev/xray/xray -c ~/etc/xui2.json &
*   wget -e "https\_proxy=http://127.0.0.1:20809" https://www.dropbox.com/sh/4u22cfuswcfcwvu/AAC4hl-f7P5lD8EZU1CFVt6-a/human\_Immune.hdf5?dl=0 -O human\_Immune.hdf5
*   wget -e "https\_proxy=http://127.0.0.1:20809" https://www.dropbox.com/sh/4u22cfuswcfcwvu/AABv0YxcNAChwEPS-D9hnDM6a/human\_Tcells.hdf5?dl=0 -O human\_Tcells.hdf5
*   fg

*   **垃圾程序让人高血压！！！**
*   conda deactivate
*   conda create -n imputation\_saver\_x -c conda-forge python=3.7 -y
*   conda activate imputation\_saver\_x
*   pip install scanpy==1.4.4
*   pip install tensorflow==2.1.0
*   pip install Keras==2.3.1
*   [https://github.com/tensorflow/tensorflow/issues/38589](https://github.com/tensorflow/tensorflow/issues/38589)
*   nano -K /opt/conda/envs/imputation\_saver\_x/lib/python3.7/site-packages/keras/backend/tensorflow\_backend.py
*   按说明重定义 is\_tensor 函数
*   pip install sctransfer
*   修改 /opt/conda/envs/imputation\_saver\_x/lib/python3.7/site-packages/keras/engine/saving.py
*   删除全部的 .decode('utf8')和.encode('utf8')

*   conda deactivate
*   conda create -n imputation\_saver\_x -c conda-forge python=3.7 -y
*   conda activate imputation\_saver\_x
*   pip install scanpy==1.4.4
*   pip install tensorflow==1.15.0
*   pip install Keras==2.3.1
*   pip install sctransfer

```r
Sys.setenv(RETICULATE_PYTHON = "/opt/conda/envs/imputation_saver_x/bin/python3.7")
library('reticulate')
use_condaenv("imputation_saver_x")
py_config()
library(SAVERX)
```

## MAGIC

[http://htmlpreview.github.io/?https://github.com/KrishnaswamyLab/MAGIC/blob/master/Rmagic/inst/examples/bonemarrow\_tutorial.html](http://htmlpreview.github.io/?https://github.com/KrishnaswamyLab/MAGIC/blob/master/Rmagic/inst/examples/bonemarrow_tutorial.html)

*   conda deactivate
*   conda activate imputation
*   conda install -c conda-forge r-viridis=0.6.2 -y
*   conda install -c conda-forge r-ggplot2=3.3.5 -y
*   conda install -c conda-forge r-readr=2.1.2 -y
*   conda install -c conda-forge r-phater=1.0.7 -y
*   conda deactivate
*   conda create -n imputation\_magic -c conda-forge numpy=1.22.3 -y
*   conda activate imputation\_magic
*   pip install phate
*   pip install magic-impute

```r
Sys.setenv(RETICULATE_PYTHON = "/opt/conda/envs/imputation_magic/bin/python3.8")
library('reticulate')
use_condaenv("imputation_magic")
py_config()
library(Rmagic)
```

## harmony

*   conda install -c bioconda r-harmony -y

```r
require(tidyverse)
f_ScaleData_RunPCA <- function(scRNA){
    scRNA <- FindVariableFeatures(scRNA, selection.method = "vst", nfeatures = 2000)
    lc_all.genes <- rownames(scRNA)
    scRNA <- ScaleData(scRNA, features = lc_all.genes)
 
    scRNA <- RunPCA(scRNA, features = VariableFeatures(object = scRNA))
    print(ElbowPlot(scRNA, ndims = 40))
    scRNA
}

require(harmony)
f_RunHarmony <-  function(scRNA, dims=1:30, batchN="batch"){
    scRNA = scRNA %>% RunHarmony(batchN, plot_convergence = TRUE, max.iter.harmony = 30)
    scRNA <- scRNA %>% RunUMAP(reduction = "harmony", dims = dims)
    scRNA
}

f_FindNeighbors <- function(scRNA, resolution = 0.5){
    scRNA <- scRNA %>% FindNeighbors(reduction = "harmony") %>%  FindClusters(resolution = resolution)
    scRNA[[paste0('h_resolution_', resolution)]] <- Idents(scRNA)
    scRNA
}
```