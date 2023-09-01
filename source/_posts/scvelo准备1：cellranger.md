---
title: scVelo准备1：cellranger
tags: []
id: '1783'
categories:
  - - 拟时序
  - - 生信
date: 2022-05-01 16:45:52
---

## 重命名R1、R2

data  
├── hPB003  
│ ├── hPB003\_S1\_L001\_R1\_001.fastq.gz  
│ └── hPB003\_S1\_L001\_R2\_001.fastq.gz  
├── hPB004  
│ ├── hPB004\_S1\_L001\_R1\_001.fastq.gz  
│ └── hPB004\_S1\_L001\_R2\_001.fastq.gz  
├── hPB005  
│ ├── hPB005\_S1\_L001\_R1\_001.fastq.gz  
│ └── hPB005\_S1\_L001\_R2\_001.fastq.gz  
├── hPB006  
│ ├── hPB006\_S1\_L001\_R1\_001.fastq.gz  
│ └── hPB006\_S1\_L001\_R2\_001.fastq.gz  
└── hPB007  
├── hPB007\_S1\_L001\_R1\_001.fastq.gz  
└── hPB007\_S1\_L001\_R2\_001.fastq.gz

## 运行Cell Ranger

[https://mp.weixin.qq.com/s?\_\_biz=MzAxMDkxODM1Ng==&mid=2247495226&idx=1&sn=baff3b7bcf2091a2a658c046dce421cd&scene=21#wechat\_redirect](https://mp.weixin.qq.com/s?__biz=MzAxMDkxODM1Ng==&mid=2247495226&idx=1&sn=baff3b7bcf2091a2a658c046dce421cd&scene=21#wechat_redirect)

```shell
#!/bin/bash
export PATH=/opt/cellranger/cellranger-6.1.2:$PATH
db=/opt/cellranger/refdata-gex-GRCh38-2020-A
data=/home/jovyan/upload/zl_liu/data/data/data
work=/home/jovyan/upload/zl_liu/data/data/res
mkdir $work
cd $work
for sample in ${data}/*;
do echo $sample
sample_res=${sample##*/}
cellranger count --id=$sample_res \
--localcores=4 \
--transcriptome=$db \
--fastqs=$sample \
--sample=$sample_res \
--expect-cells=5000
done
```