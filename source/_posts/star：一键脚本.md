---
title: STAR：一键脚本
tags: []
id: '1940'
categories:
  - - 组织测序
date: 2022-07-02 15:53:52
---

## 可选：BAM转FASTQ

将所有bam文件以.bam结尾，单独存放到一个目录，conda activate biobambam，新建以下脚本运行

```sh
#!/bin/sh
ROOT=/home/jovyan/upload/22.07.02/muscle
mkdir $ROOT
 
for _ in *.bam
do
 
mkdir $ROOT/${_%.bam}
 
bamtofastq \
collate=1 \
exclude=QCFAIL,SECONDARY,SUPPLEMENTARY \
filename=$_ \
gz=1 \
inputformat=bam \
level=5 \
outputdir=$ROOT/${_%.bam} \
outputperreadgroup=1 \
outputperreadgroupsuffixF=_1.fq.gz \
outputperreadgroupsuffixF2=_2.fq.gz \
outputperreadgroupsuffixO=_o1.fq.gz \
outputperreadgroupsuffixO2=_o2.fq.gz \
outputperreadgroupsuffixS=_s.fq.gz \
tryoq=1 \
 
done
```

## 一键脚本

单独起一个目录，在这个目录下，以样本名为目录名，将样本的fataq文件以gzip压缩后存放，单端一个文件，双端两个文件，都可以，示例结构如下

```sh
XL1_12/
├── XL01
│   └── default_s.fq.gz
├── XL02
│   └── default_s.fq.gz
├── XL03
│   └── default_s.fq.gz
├── XL04
│   └── default_s.fq.gz
├── XL05
│   └── default_s.fq.gz
├── XL06
│   └── default_s.fq.gz
├── XL07
│   └── default_s.fq.gz
├── XL08
│   └── default_s.fq.gz
├── XL09
│   └── default_s.fq.gz
├── XL10
│   └── default_s.fq.gz
├── XL11
│   └── default_s.fq.gz
└── XL12
    └── default_s.fq.gz
```

在这个目录外，conda activate star，新建以下脚本运行，即可跑完前五步

```sh
#!/bin/sh
#任务名
TASKN=XL1_12
#设置CleanData存放目录
CLEAN=/home/jovyan/upload/22.07.02/$TASKN
#设置输出目录
WORK=/home/jovyan/upload/22.07.02/output_$TASKN
#设置index目录
INDEX=/home/jovyan/upload/22.07.02/index
#设置参考文件位置
Reference=/home/jovyan/upload/22.07.02/GRCm39.primary_assembly.genome.fa
#设置 sjdbOverhang
sjdbOverhang=49
#设置 IIG 目录(这一步的输出目录)
IIG=/home/jovyan/upload/22.07.02/IIG_$TASKN
 
echo $CLEAN", "$WORK", "$INDEX
mkdir $WORK
 
CDIR=$(basename `pwd`)
echo $CDIR
echo $CLEAN
for file in $CLEAN/*
do
echo $file
SAMPLE=${file##*/}
echo $SAMPLE
mkdir $WORK"/"$SAMPLE
cd $WORK"/"$SAMPLE
 
STAR \
--genomeDir $INDEX \
--readFilesIn `ls $CLEAN/$SAMPLE/*` \
--runThreadN 4 \
--outFilterMultimapScoreRange 1 \
--outFilterMultimapNmax 20 \
--outFilterMismatchNmax 10 \
--alignIntronMax 500000 \
--alignMatesGapMax 1000000 \
--sjdbScore 2 \
--alignSJDBoverhangMin 1 \
--genomeLoad LoadAndRemove \
--readFilesCommand zcat \
--outFilterMatchNminOverLread 0.33 \
--outFilterScoreMinOverLread 0.33 \
--sjdbOverhang $sjdbOverhang \
--outSAMstrandField intronMotif \
--outSAMtype None \
--outSAMmode None \
 
done
 
echo $CLEAN", "$WORK", "$INDEX", "$IIG
mkdir $IIG
 
CDIR=$(basename `pwd`)
echo $CDIR
echo $CLEAN
for file in $CLEAN/*
do
echo $file
SAMPLE=${file##*/}
echo $WORK"/"$SAMPLE
done
 
STAR \
--runMode genomeGenerate \
--genomeDir $IIG \
--genomeFastaFiles $Reference \
--sjdbOverhang $sjdbOverhang \
--runThreadN 4 \
--sjdbFileChrStartEnd `ls $WORK/*/SJ.out.tab`
 
ln -s $INDEX/exonGeTrInfo.tab $IIG
ln -s $INDEX/exonInfo.tab $IIG
ln -s $INDEX/geneInfo.tab $IIG
ln -s $INDEX/sjdbList.fromGTF.out.tab $IIG
ln -s $INDEX/transcriptInfo.tab $IIG
 
CDIR=$(basename `pwd`)
echo $CDIR
echo $CLEAN
for file in $CLEAN/*
do
echo $file
SAMPLE=${file##*/}
echo $WORK"/"$SAMPLE
mkdir $WORK"/"$SAMPLE"/Res"
cd $WORK"/"$SAMPLE"/Res"
 
STAR \
--genomeDir $IIG \
--readFilesIn `ls $CLEAN/$SAMPLE/*` \
--runThreadN 8 \
--quantMode TranscriptomeSAM GeneCounts \
--outFilterMultimapScoreRange 1 \
--outFilterMultimapNmax 20 \
--outFilterMismatchNmax 10 \
--alignIntronMax 500000 \
--alignMatesGapMax 1000000 \
--sjdbScore 2 \
--alignSJDBoverhangMin 1 \
--genomeLoad LoadAndRemove \
--limitBAMsortRAM  35000000000 \
--readFilesCommand zcat \
--outFilterMatchNminOverLread 0.33 \
--outFilterScoreMinOverLread 0.33 \
--sjdbOverhang $sjdbOverhang \
--outSAMstrandField intronMotif \
--outSAMattributes NH HI NM MD AS XS \
--outSAMunmapped Within \
--outSAMtype BAM SortedByCoordinate \
--outSAMheaderHD @HD VN:1.4 \
--outSAMattrRGline ID:sample SM:sample PL:ILLUMINA
 
done
```

## DESeq2分析

```R
library(DESeq2)
count_all <- read.csv("liver.csv",header=TRUE,row.names=1)
count_all
cts_b <- count_all[ ,c(-1,-2,-3)]
rownames(cts_b) <- count_all$ID
cts_bb <- cts_b
 
cts_b <- cts_bb[,c('XL07', 'XL08', 'XL09', 'XL10', 'XL11', 'XL12')]
keep <- rowSums(cts_b) > 10
cts_b[keep,]
conditions <- factor(c(rep("Control",3), rep("XL",3)))    
colData_b <- data.frame(row.names = colnames(cts_b), conditions)
colData_b
dds <- DESeqDataSetFromMatrix(countData = cts_b[keep,],
                              colData = colData_b,
                              design = ~ conditions)
dds <- DESeq(dds)
res <- results(dds)
rres <- cbind(count_all[keep,c(1,2,3)], data.frame(res))
write.csv(rres, file='XL101112vs789_DESeq2.csv')
rres
```

## 演示视频