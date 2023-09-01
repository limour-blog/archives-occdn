---
title: Biobambam：将BAM转成FASQ
tags: []
id: '1929'
categories:
  - - 组织测序
date: 2022-07-02 10:05:13
---

## 来源

[https://docs.gdc.cancer.gov/Data/Bioinformatics\_Pipelines/DNA\_Seq\_Variant\_Calling\_Pipeline/#step-1-converting-bams-to-fastqs-with-biobambam-biobambam2-2054](https://docs.gdc.cancer.gov/Data/Bioinformatics_Pipelines/DNA_Seq_Variant_Calling_Pipeline/#step-1-converting-bams-to-fastqs-with-biobambam-biobambam2-2054)

[https://j11.fun/biobambam2](https://j11.fun/biobambam2)

## 步骤

```sh
for _ in `ls ~/STAR/liver/input.XL{1,2,3,7,8,9}.bam.gz`;do mv $_ ${_##*input.} ;done
conda create -n biobambam -c bioconda biobambam=2.0.87 -y
conda activate biobambam
gunzip *.gz
for _ in *.bam; do echo ${_%.bam} ; done
nano 1.sh
chmod +x 1.sh

#!/bin/sh
for _ in *.bam
do

mkdir ${_%.bam}

bamtofastq \
collate=1 \
exclude=QCFAIL,SECONDARY,SUPPLEMENTARY \
filename=$_ \
gz=1 \
inputformat=bam \
level=5 \
outputdir=${_%.bam} \
outputperreadgroup=1 \
outputperreadgroupsuffixF=_1.fq.gz \
outputperreadgroupsuffixF2=_2.fq.gz \
outputperreadgroupsuffixO=_o1.fq.gz \
outputperreadgroupsuffixO2=_o2.fq.gz \
outputperreadgroupsuffixS=_s.fq.gz \
tryoq=1 \

done
```

*   biobambam高版本可能有 libmaus2相关错误尚未修复
*   filename、outputdir等参数等于号后不能有空格
*   单端测序，outputdir里只有 default\_s.fq.gz 的输出