---
title: 按TCGA的STAR流程处理组织高通量测序数据
tags: []
id: '1662'
categories:
  - - 生信
  - - 组织测序
date: 2022-04-06 10:00:14
---

![](https://img.limour.top/archives_2023/blog/20220404172423.webp)

流程：[https://docs.gdc.cancer.gov/Data/Bioinformatics\_Pipelines/Expression\_mRNA\_Pipeline/](https://docs.gdc.cancer.gov/Data/Bioinformatics_Pipelines/Expression_mRNA_Pipeline/)

## 第一步 安装必要的软件

*   conda create -n star -c bioconda star -y
*   **conda activate star**
*   conda install -c bioconda samtools -y

说明书：[https://github.com/alexdobin/STAR/blob/master/doc/STARmanual.pdf](https://github.com/alexdobin/STAR/blob/master/doc/STARmanual.pdf)

## 第二步 建立**基因组**索引

*   wget https://api.gdc.cancer.gov/data/07f2dca9-cd39-4cbf-90d2-c7a1b8df5139 -O star-2.7.5c\_GRCh38.d1.vd1\_gencode.v36.tgz
*   tar -zxvf star-2.7.5c\_GRCh38.d1.vd1\_gencode.v36.tgz
*   上面的原始文件已经损坏了！

地址：[https://gdc.cancer.gov/about-data/gdc-data-processing/gdc-reference-files](https://gdc.cancer.gov/about-data/gdc-data-processing/gdc-reference-files)

如果要自己构建，可以使用 zcat R1.fq.gz head 来查看reads长度，选用reads长度减1（149）作为 **\--sjdbOverhang** 比默认的100要好，但是说明里认为绝大多数情况下100和理想值差不多  
[https://www.gencodegenes.org/human/release\_36.html](https://www.gencodegenes.org/human/release_36.html)

*   wget https://ftp.ebi.ac.uk/pub/databases/gencode/Gencode\_human/release\_36/GRCh38.primary\_assembly.genome.fa.gz
*   wget https://ftp.ebi.ac.uk/pub/databases/gencode/Gencode\_human/release\_36/gencode.v36.primary\_assembly.annotation.gtf.gz
*   mkdir index\_150
*   gunzip gencode.v36.primary\_assembly.annotation.gtf.gz
*   gunzip GRCh38.primary\_assembly.genome.fa.gz

```shell
STAR \
--runMode genomeGenerate \
--genomeDir index_150 \
--genomeFastaFiles GRCh38.primary_assembly.genome.fa \
--sjdbOverhang 149 \
--sjdbGTFfile gencode.v36.primary_assembly.annotation.gtf \
--runThreadN 8
```

## 第三步 第一次对比

![](https://img.limour.top/archives_2023/blog/20220405043153.webp)

CleanData存放格式

```shell
#!/bin/sh

#设置CleanData存放目录
CLEAN=/home/jovyan/upload/yy_zhang（备份）/RNA-seq/Cleandata
#设置这一步的输出目录 (确保目录存在)
WORK=/home/jovyan/upload/zl_liu/star_data/yyz_01/output
#设置index目录
INDEX=/home/jovyan/upload/zl_liu/star/index_150
#设置参考文件目录
Reference=/home/jovyan/upload/zl_liu/star

echo $CLEAN", "$WORK", "$INDEX

export CDIR=$(basename `pwd`)
echo $CDIR
echo $CLEAN
for file in $CLEAN/*
do
echo $file
SAMPLE=${file##*/}
echo $SAMPLE
r1=${SAMPLE}"_R1.fq.gz"
r2=${SAMPLE}"_R2.fq.gz"
echo $r1
echo $r2
mkdir $WORK"/"$SAMPLE
cd $WORK"/"$SAMPLE

STAR \
--genomeDir $INDEX \
--readFilesIn $CLEAN/$SAMPLE/$r1","$CLEAN/$SAMPLE/$r2 \
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
--sjdbOverhang 149 \
--outSAMstrandField intronMotif \
--outSAMtype None \
--outSAMmode None \

done
```

## 第四步 建立中间索引

```shell
#!/bin/sh

#设置CleanData存放目录
CLEAN=/home/jovyan/upload/yy_zhang（备份）/RNA-seq/Cleandata
#设置这一步的输出目录 (确保目录存在)
WORK=/home/jovyan/upload/zl_liu/star_data/yyz_01/output
#设置index目录
INDEX=/home/jovyan/upload/zl_liu/star/index_150
#设置参考文件目录
Reference=/home/jovyan/upload/zl_liu/star

echo $CLEAN", "$WORK", "$INDEX

export CDIR=$(basename `pwd`)
echo $CDIR
echo $CLEAN
for file in $CLEAN/*
do
echo $file
SAMPLE=${file##*/}
echo $SAMPLE
r1=${SAMPLE}"_R1.fq.gz"
r2=${SAMPLE}"_R2.fq.gz"
echo $r1
echo $r2
cd $WORK"/"$SAMPLE
mkdir IIG

STAR \
--runMode genomeGenerate \
--genomeDir IIG \
--genomeFastaFiles $Reference"/GRCh38.primary_assembly.genome.fa" \
--sjdbOverhang 149 \
--runThreadN 4 \
--sjdbFileChrStartEnd SJ.out.tab \

done
```

## 第五步 第二次对比

```shell
#!/bin/sh

#设置CleanData存放目录
CLEAN=/home/jovyan/upload/yy_zhang（备份）/RNA-seq/Cleandata
#设置这一步的输出目录 (确保目录存在)
WORK=/home/jovyan/upload/zl_liu/star_data/yyz_01/output
#设置index目录
INDEX=/home/jovyan/upload/zl_liu/star/index_150
#设置参考文件目录
Reference=/home/jovyan/upload/zl_liu/star

echo $CLEAN", "$WORK", "$INDEX

export CDIR=$(basename `pwd`)
echo $CDIR
echo $CLEAN
for file in $CLEAN/*
do
echo $file
SAMPLE=${file##*/}
echo $SAMPLE
r1=${SAMPLE}"_R1.fq.gz"
r2=${SAMPLE}"_R2.fq.gz"
echo $r1
echo $r2

cd $WORK"/"$SAMPLE"/IIG"
ln -s $INDEX/exonGeTrInfo.tab .
ln -s $INDEX/exonInfo.tab .
ln -s $INDEX/geneInfo.tab .
ln -s $INDEX/sjdbList.fromGTF.out.tab .
ln -s $INDEX/transcriptInfo.tab .

mkdir $WORK"/"$SAMPLE"/Res"
cd $WORK"/"$SAMPLE"/Res"

STAR \
--genomeDir ../IIG \
--readFilesIn $CLEAN/$SAMPLE/$r1","$CLEAN/$SAMPLE/$r2 \
--runThreadN 4 \
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
--sjdbOverhang 149 \
--outSAMstrandField intronMotif \
--outSAMattributes NH HI NM MD AS XS \
--outSAMunmapped Within \
--outSAMtype BAM SortedByCoordinate \
--outSAMheaderHD @HD VN:1.4 \
--outSAMattrRGline ID:sample SM:sample PL:ILLUMINA

done
```

## 第六步 组装Counts文件

```r
#设置上一步的输出目录
WORK='/home/jovyan/upload/zl_liu/star_data/yyz_01/output'
#设置参考文件目录
Reference='/home/jovyan/upload/zl_liu/star'
Reference = file.path(Reference, 'index_150', 'geneInfo.tab')
file_list <- list.dirs(WORK, recursive=F, full.names = F)
geneN <- read.table(file = Reference, sep = '\t', skip = 1)
colnames(geneN) <- c('ID', 'symbol', 'type')
for(sample in file_list){
    test_tab <- read.table(file = file.path(WORK, sample, 'Res', 'ReadsPerGene.out.tab') , sep = '\t', header = F)
    test_tab <- test_tab[-c(1:4), ]
    geneN[sample] <- test_tab[2]
}
write.csv(x = geneN, file = 'yyz_01.csv')
```

```r
f_name_dedup <- function(lc_exp, rowN = 1){
    res <- lc_exp[-rowN]
    lc_tmp = by(res,
         lc_exp[[rowN]],
         function(x) rownames(x)[which.max(rowMeans(x))])
    lc_probes = as.character(lc_tmp)
    res = lc_exp[rownames(res) %in% lc_probes,]
    rownames(res) <- res[[rowN]]
    res[-rowN]
}
```

## 第七步 删除临时文件

```shell
#!/bin/sh

#设置CleanData存放目录
CLEAN=/home/jovyan/upload/yy_zhang（备份）/RNA-seq/Cleandata
#设置这一步的输出目录 (确保目录存在)
WORK=/home/jovyan/upload/zl_liu/star_data/yyz_01/output

for file in $CLEAN/*
do
echo $file
SAMPLE=${file##*/}
echo $SAMPLE

rm -rf $WORK"/"$SAMPLE"/IIG"

done
```