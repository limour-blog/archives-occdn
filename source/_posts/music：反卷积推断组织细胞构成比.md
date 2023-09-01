---
title: MuSiC：反卷积推断组织细胞构成比
tags: []
id: '1895'
categories:
  - - 组织测序
date: 2022-06-28 22:35:20
---

https://occdn.limour.top/1822.html

## 第一步 准备数据

### 组织数据

https://occdn.limour.top/1655.html

```R
library(plyr)
library(SummarizedExperiment)
tmp = load('PRAD.rda')
tmp # 'data'
data
counts <- data@assays@data$unstranded
colnames(counts) <- data@colData$patient
rownames(counts) <- data@rowRanges$gene_name
counts
saveRDS(counts, 'prad_counts.rds')
```

```R
require(Biobase)
f_rm_duplicated <- function(NameL, reverse=F){
    tmp <- data.frame(table(NameL))
    if(reverse){
        tmp <- tmp$NameL[tmp$Freq > 1]
    }else{
        tmp <- tmp$NameL[tmp$Freq == 1]
    }
    which(NameL %in% as.character(tmp))
}
f_name_dedup <- function(lc_exp, rowN = 1){
    if (rowN == 0){
        res <- lc_exp
        rowNn <- rownames(lc_exp)
    }else{
        res <- lc_exp[-rowN]
        rowNn <- lc_exp[[rowN]]
    }
    noDup <- f_rm_duplicated(rowNn)
    tmp <- rowNn[noDup]
    noDup <- res[noDup,]
    rownames(noDup) <- tmp
    Dup <- f_rm_duplicated(rowNn, T)
    rowNn <- rowNn[Dup]
    Dup <- res[Dup,]
    rownames(Dup) <- NULL
    lc_tmp = by(Dup,
         rowNn,
         function(x) rownames(x)[which.max(rowMeans(x))])
    lc_probes = as.integer(lc_tmp)
    Dup = Dup[lc_probes,]
    rownames(Dup) <- rowNn[lc_probes]
    return(rbind(noDup,Dup))
}
prad <- readRDS('~/work_st/zl_liu_new/Bulk/tcga/PRAD/prad_counts.rds')
prad <- prad[,f_rm_duplicated(colnames(prad))]
prad <- f_name_dedup(prad, 0)
metadata <- data.frame(labelDescription=c('SampleID'),
                   row.names=c('SampleID'))
meta <-  data.frame(SampleID = colnames(prad),
                    row.names = colnames(prad))
phenoData <- new("AnnotatedDataFrame",data=meta,varMetadata=metadata, dimLabels = c('sampleNames','sampleColumns'))
bulk.eset <- ExpressionSet(assayData=prad,  phenoData=phenoData)
bulk.eset
saveRDS(bulk.eset, 'bulk.eset.rds')
```

### 单细胞数据

*   conda activate seurat
*   conda install -c bioconda bioconductor-biobase -y

```R
sce <- readRDS("~/upload/zl_liu/data/pca.rds")
metadata <- data.frame(labelDescription=c('SampleID', 'Cell Type Name'),
                   row.names=c('SampleID', 'cellType'))
meta <-  sce[[c('orig.ident', 'cell_type_fig3')]]
colnames(meta) <- c('SampleID', 'cellType')
require(Biobase)
phenoData <- new("AnnotatedDataFrame",data=meta,varMetadata=metadata, dimLabels = c('sampleNames','sampleColumns'))
sc.eset <- ExpressionSet(assayData=as.matrix(sce@assays$RNA@counts),  phenoData=phenoData)
saveRDS(sc.eset, 'sc.eset.rds')
```

## 第二步 运行music\_prop

```R
library(MuSiC)
bulk.eset <- readRDS('bulk.eset.rds')
sc.eset <- readRDS('sc.eset.rds')
output <-  music_prop(bulk.eset = bulk.eset, sc.eset = sc.eset, clusters = 'cellType',
                               samples = 'SampleID', select.ct = c('Luminal', 'Endothelial', 'Basal cell',
                                                                   'myCAF', 'iCAF'), verbose = T)
output$Est.prop.weighted
saveRDS(output, 'MuSiC_output.rds')
```