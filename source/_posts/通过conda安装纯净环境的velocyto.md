---
title: 通过conda安装纯净环境的velocyto
tags: []
id: '1779'
categories:
  - - 拟时序
  - - 生信
date: 2022-05-01 15:54:51
---

## cellranger

[https://support.10xgenomics.com/single-cell-gene-expression/software/pipelines/latest/installation](https://support.10xgenomics.com/single-cell-gene-expression/software/pipelines/latest/installation)

[https://www.jianshu.com/p/1e7acde1a318](https://www.jianshu.com/p/1e7acde1a318)

[https://mp.weixin.qq.com/s/TJsdj7qesZGlvmEkNLAy0A](https://mp.weixin.qq.com/s/TJsdj7qesZGlvmEkNLAy0A)

GRCh38\_rmsk.gtf.gz：[https://genome.ucsc.edu/cgi-bin/hgTables](https://genome.ucsc.edu/cgi-bin/hgTables?hgsid=611454127_NtvlaW6xBSIRYJEBI0iRDEWisITa&clade=mammal&org=Human&db=0&hgta_group=allTracks&hgta_track=rmsk&hgta_table=rmsk&hgta_regionType=genome&position=&hgta_outputType=gff&hgta_outFileName=GRCh38_rmsk.gtf)

![](https://img.limour.top/archives_2023/blog/20220501140947.webp)

*   cd /opt/cellranger
*   wget <Cell Ranger>
*   wget <References>
*   tar -xzvf cellranger-6.1.2.tar.gz
*   tar -xzvf refdata-gex-GRCh38-2020-A.tar.gz
*   下载 GRCh38\_rmsk.gtf.gz 上传阿里云盘
*   ./aliyunpan
*   login
*   d GRCh38\_rmsk.gtf.gz -saveto /opt/cellranger
*   gunzip GRCh38\_rmsk.gtf.gz
*   export PATH=/opt/cellranger/cellranger-6.1.2:$PATH
*   cellranger sitecheck > sitecheck.txt
*   cellranger upload xxx@fudan.edu.cn sitecheck.txt
*   cellranger testrun --id=tiny
*   cellranger upload xxx@fudan.edu.cn tiny/tiny.mri.tgz

## velocyto

[http://velocyto.org/](http://velocyto.org/)

[http://velocyto.org/velocyto.py/install/index.html#install](http://velocyto.org/velocyto.py/install/index.html#install)

*   conda create -n velocyto -c conda-forge python=3.7 -y
*   conda activate velocyto
*   conda install numpy scipy cython numba matplotlib scikit-learn h5py click -y
*   pip install pysam
*   pip install velocyto
*   velocyto --help
*   conda install -c bioconda samtools=1.15.1 -y