---
title: scVelo准备2：从cellranger得到loom文件
tags: []
id: '1788'
categories:
  - - 原始数据
  - - 拟时序
  - - 生信
date: 2022-05-05 18:01:41
---

```shell
conda activate velocyto

#!/bin/bash
db=/opt/cellranger/refdata-gex-GRCh38-2020-A
work=/home/jovyan/upload/zl_liu/data/data/res
rmsk_gtf=/opt/cellranger/GRCh38_rmsk.gtf # 从genome.ucsc.edu下载 
cellranger_gtf=${db}/genes/genes.gtf
ls -lh $rmsk_gtf  $work $cellranger_gtf
for sample in ${work}/*;
do echo $sample
velocyto run10x -m $rmsk_gtf $sample $cellranger_gtf
done
```