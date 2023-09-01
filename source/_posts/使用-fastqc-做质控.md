---
title: 使用 FastQC 做质控
tags: []
id: '1958'
categories:
  - - 组织测序
date: 2022-07-04 07:49:46
---

## 来源

[https://anaconda.org/bioconda/fastqc](https://anaconda.org/bioconda/fastqc)

[https://zhuanlan.zhihu.com/p/20731723](https://zhuanlan.zhihu.com/p/20731723)

[https://liripo.github.io/posts/2019/fastqc%E4%BD%BF%E7%94%A8/](https://liripo.github.io/posts/2019/fastqc%E4%BD%BF%E7%94%A8/)

## 通过conda安装纯净环境的FastQC

*   conda create -n fastqc -c bioconda fastqc
*   conda activate fastqc

## 批量运行FastQC

```sh
#!/bin/sh
#任务名
TASKN=XL1_12
#设置CleanData存放目录
CLEAN=/home/jovyan/upload/22.07.02/$TASKN
#设置qc结果的输出目录
QCDIR=/home/jovyan/upload/22.07.02/fastqc_$TASKN
 
mkdir $QCDIR
 
for file in $CLEAN/*
do
echo $file
SAMPLE=${file##*/}
echo $QCDIR"/"$SAMPLE
mkdir $QCDIR"/"$SAMPLE
fastqc -o $QCDIR"/"$SAMPLE -t 6 `ls $CLEAN/$SAMPLE/*`
done
```

*   nano qc.sh
*   chmod +x qc.sh
*   ./qc.sh