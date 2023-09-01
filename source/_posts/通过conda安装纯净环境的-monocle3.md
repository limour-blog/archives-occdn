---
title: 通过conda安装纯净环境的 Monocle3
tags: []
id: '1609'
categories:
  - - 拟时序
  - - 生信
date: 2022-03-12 16:40:26
---

*   conda create -n monocle3 -c conda-forge r-base=4.1.2 -y
*   conda activate monocle3
*   conda install -c conda-forge r-seurat=4.1.0 -y
*   conda install -c conda-forge r-irkernel=1.3 -y
*   conda install -c conda-forge r-biocmanager=1.30.16 -y
*   conda install -c conda-forge r-devtools=2.4.3 -y
*   conda install -c bioconda bioconductor-limma=3.50.1 -y
*   conda install -c bioconda bioconductor-summarizedexperiment=1.24.0 -y
*   conda install -c bioconda bioconductor-batchelor=1.10.0 -y
*   conda install -c bioconda bioconductor-biocparallel=1.28.3 -y
*   conda install -c conda-forge r-doparallel=1.0.17 -y
*   conda install -c conda-forge parallel=20220222 -y
*   conda install -c conda-forge r-foreach=1.5.2 -y
*   conda install -c conda-forge r-matrix.utils=0.9.8 -y
*   conda create -n cytoTRACE -c conda-forge python=3.7 -y
*   conda activate cytoTRACE
*   conda install -c conda-forge numpy -y
*   /opt/conda/envs/cytoTRACE/bin/pip3 install scanoramaCT -i https://pypi.tuna.tsinghua.edu.cn/simple
*   conda deactivate
*   wget https://cytotrace.stanford.edu/CytoTRACE\_0.3.3.tar.gz -O CytoTRACE\_0.3.3.tar.gz
*   conda install -c conda-forge r-sf=1.0\_6 -y
*   wget https://github.com/cole-trapnell-lab/leidenbase/archive/refs/heads/master.zip -O leidenbase-master.zip
*   wget https://github.com/cole-trapnell-lab/monocle3/archive/refs/heads/master.zip -O monocle3-master.zip
*   conda install -c bioconda bioconductor-sva=3.42.0 -y
*   conda install -c conda-forge r-ggpubr=0.4.0 -y
*   conda install -c conda-forge r-spdep=1.2\_2 -y
*   devtools::install\_local("CytoTRACE\_0.3.3.tar.gz")
*   devtools::install\_local("leidenbase-master.zip")
*   devtools::install\_local("monocle3-master.zip")
*   BiocManager::install(c("DDRTree", "monocle"))
*   IRkernel::installspec(name='monocle3', displayname='r-monocle3')

```r
library(reticulate)
use_condaenv("cytoTRACE")
```