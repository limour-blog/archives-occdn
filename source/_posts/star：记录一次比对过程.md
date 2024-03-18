---
title: STAR：记录一次比对过程
tags: []
id: '1934'
categories:
  - - 组织测序
date: 2022-07-02 14:37:41
---

## 数据准备

```sh
.
├── gencode.vM29.primary_assembly.annotation.gtf
├── GRCm39.primary_assembly.genome.fa
├── index
│   ├── chrLength.txt
│   ├── chrNameLength.txt
│   ├── chrName.txt
│   ├── chrStart.txt
│   ├── exonGeTrInfo.tab
│   ├── exonInfo.tab
│   ├── geneInfo.tab
│   ├── Genome
│   ├── genomeParameters.txt
│   ├── Log.out
│   ├── SA
│   ├── SAindex
│   ├── sjdbInfo.txt
│   ├── sjdbList.fromGTF.out.tab
│   ├── sjdbList.out.tab
│   └── transcriptInfo.tab
└── XL789vs123
    ├── XL1
    │   └── default_s.fq.gz
    ├── XL2
    │   └── default_s.fq.gz
    ├── XL3
    │   └── default_s.fq.gz
    ├── XL7
    │   └── default_s.fq.gz
    ├── XL8
    │   └── default_s.fq.gz
    └── XL9
        └── default_s.fq.gz
```

https://occdn.limour.top/1929.html

https://occdn.limour.top/1932.html

*   XL789vs123中的fasta文件可以来自公司的cleandata，也可以从bam文件生成
*   index中的文件为STAR使用参考基因组fasta文件和对应的gtf注释生成

## 第三步 第一次对比

```sh
#!/bin/sh
#设置CleanData存放目录
CLEAN=/home/jovyan/upload/22.07.02/XL789vs123
#设置输出目录
WORK=/home/jovyan/upload/22.07.02/output_XL789vs123
#设置index目录
INDEX=/home/jovyan/upload/22.07.02/index
#设置参考文件位置
Reference=/home/jovyan/upload/22.07.02/GRCm39.primary_assembly.genome.fa
#设置 sjdbOverhang
sjdbOverhang=49
 
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
```

*   nano 3.sh
*   chmod +x 3.sh
*   ./3.sh 将得到以下输出

```sh
output_XL789vs123/
├── XL1
│   ├── Log.final.out
│   ├── Log.out
│   ├── Log.progress.out
│   └── SJ.out.tab
├── XL2
│   ├── Log.final.out
│   ├── Log.out
│   ├── Log.progress.out
│   └── SJ.out.tab
├── XL3
│   ├── Log.final.out
│   ├── Log.out
│   ├── Log.progress.out
│   └── SJ.out.tab
├── XL7
│   ├── Log.final.out
│   ├── Log.out
│   ├── Log.progress.out
│   └── SJ.out.tab
├── XL8
│   ├── Log.final.out
│   ├── Log.out
│   ├── Log.progress.out
│   └── SJ.out.tab
└── XL9
    ├── Log.final.out
    ├── Log.out
    ├── Log.progress.out
    └── SJ.out.tab
```

## 第四步 建立中间索引

```sh
#!/bin/sh
#设置CleanData存放目录
CLEAN=/home/jovyan/upload/22.07.02/XL789vs123
#设置第三步的输出目录(上一步的输出目录)
WORK=/home/jovyan/upload/22.07.02/output_XL789vs123
#设置index目录
INDEX=/home/jovyan/upload/22.07.02/index
#设置参考文件位置
Reference=/home/jovyan/upload/22.07.02/GRCm39.primary_assembly.genome.fa
#设置 sjdbOverhang
sjdbOverhang=49
#设置 IIG 目录(这一步的输出目录)
IIG=/home/jovyan/upload/22.07.02/IIG_XL789vs123

```

*   nano 4.sh
*   chmod +x 4.sh
*   ./4.sh 将得到以下输出

```sh
IIG_XL789vs123/
├── chrLength.txt
├── chrNameLength.txt
├── chrName.txt
├── chrStart.txt
├── Genome
├── genomeParameters.txt
├── Log.out
├── SA
├── SAindex
├── sjdbInfo.txt
└── sjdbList.out.tab
```

## 第五步 第二次对比

```sh
#!/bin/sh
#设置CleanData存放目录
CLEAN=/home/jovyan/upload/22.07.02/XL789vs123
#设置第三步的输出目录
WORK=/home/jovyan/upload/22.07.02/output_XL789vs123
#设置index目录
INDEX=/home/jovyan/upload/22.07.02/index
#设置参考文件位置
Reference=/home/jovyan/upload/22.07.02/GRCm39.primary_assembly.genome.fa
#设置 sjdbOverhang
sjdbOverhang=49
#设置 IIG 目录(第四步的输出目录)
IIG=/home/jovyan/upload/22.07.02/IIG_XL789vs123
 
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

*   nano 5.sh
*   chmod +x 5.sh
*   ./5.sh 将得到以下输出

```sh
output_XL789vs123/XL1/Res/
├── Aligned.sortedByCoord.out.bam
├── Aligned.toTranscriptome.out.bam
├── Log.final.out
├── Log.out
├── Log.progress.out
├── ReadsPerGene.out.tab
└── SJ.out.tab
```

## 第六步 组装Counts文件

```R
#设置第三步的输出目录
WORK='/home/jovyan/upload/22.07.02/output_XL789vs123'
#设置index中基因注释位置
Reference='/home/jovyan/upload/22.07.02/index/geneInfo.tab'
file_list <- list.dirs(WORK, recursive=F, full.names = F)
geneN <- read.table(file = Reference, sep = '\t', skip = 1)
colnames(geneN) <- c('ID', 'symbol', 'type')
for(sample in file_list){
    test_tab <- read.table(file = file.path(WORK, sample, 'Res', 'ReadsPerGene.out.tab') , sep = '\t', header = F)
    test_tab <- test_tab[-c(1:4), ]
    geneN[sample] <- test_tab[2]
}
write.csv(x = geneN, file = 'XL789vs123.csv')
```

![](https://img.limour.top/archives_2023/blog/20220702142511.webp)

组装的Counts文件格式

## 第七步 差异基因分析

```R
library(DESeq2)
count_all <- read.csv("XL789vs123.csv",header=TRUE,row.names=1)
count_all
cts_b <- count_all[ ,c(-1,-2,-3)]
rownames(cts_b) <- count_all$ID
conditions <- factor(c(rep("Control",3), rep("XL",3)))    
colData_b <- data.frame(row.names = colnames(cts_b), conditions)
colData_b
dds <- DESeqDataSetFromMatrix(countData = cts_b,
                              colData = colData_b,
                              design = ~ conditions)
dds <- DESeq(dds)
res <- results(dds)
rres <- cbind(count_all, data.frame(res))
write.csv(rres, file='XL789vs123_DESeq2.csv')
```