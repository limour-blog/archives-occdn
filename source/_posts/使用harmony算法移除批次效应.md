---
title: 使用Harmony算法移除批次效应
tags: []
id: '2232'
categories:
  - - 分群
comments: false
date: 2022-08-13 00:08:41
---

## 安装补充包

*   [conda activate seurat](https://occdn.limour.top/2227.html)
*   conda install -c bioconda r-harmony -y

## 读取数据

```r
root_path = "."
f_read10x <- function(dirN, ...){
    tp_samples <- list.files(file.path(root_path, dirN))
    tp_dir <- file.path(root_path, dirN, tp_samples)
    names(tp_dir) <- tp_samples
    scRNA <- list()
    for (lc_ba in names(tp_dir)){
        counts <- Read10X(data.dir = tp_dir[[lc_ba]])
        scRNA[[lc_ba]] <- CreateSeuratObject(counts, project = lc_ba, ...)
        scRNA[[lc_ba]][["percent.mt"]] <- PercentageFeatureSet(scRNA[[lc_ba]], pattern = "^MT-")
        scRNA[[lc_ba]][["percent.ERCC"]] <- PercentageFeatureSet(scRNA[[lc_ba]], pattern = "^ERCC-")
    }
    scRNA
}
```

## 质量控制

```r
options(repr.plot.width=12, repr.plot.height=6)
options(ggrepel.max.overlaps = Inf)
library(ggplot2)
for (scRNAo in scRNA){
    print(VlnPlot(scRNAo, features = c("nFeature_RNA", "nCount_RNA", "percent.mt"), ncol = 3) + labs(title = scRNAo@project.name))
}

scRNA$APAP1 <- subset(scRNA$APAP1, subset = nFeature_RNA > 200 & nFeature_RNA < 3000)

scRNA <- Reduce(function(...) merge(...), scRNA)
```

## 预处理函数

```r
f_ScaleData_RunPCA <- function(scRNA){
    scRNA <- FindVariableFeatures(scRNA, selection.method = "vst", nfeatures = 2000)
    lc_all.genes <- rownames(scRNA)
    scRNA <- ScaleData(scRNA, features = lc_all.genes)

    scRNA <- RunPCA(scRNA, features = VariableFeatures(object = scRNA))
    print(ElbowPlot(scRNA, ndims = 40))
    scRNA
}
require(harmony)
f_RunHarmony <-  function(scRNA, dims=1:30, batchN="batch"){
    scRNA = scRNA %>% RunHarmony(batchN, plot_convergence = TRUE, max.iter.harmony = 30)
    scRNA <- scRNA %>% RunUMAP(reduction = "harmony", dims = dims)
    scRNA
}
```

## 分群函数

```r
f_FindNeighbors <- function(scRNA, resolution = 0.5){
    scRNA <- scRNA %>% FindNeighbors(reduction = "harmony") %>%  FindClusters(resolution = resolution)
    scRNA[[paste0('h_resolution_', resolution)]] <- Idents(scRNA)
    scRNA
}
```

## 查看分群质量

```r
f_metaG2G <- function(metaG, matrixN=F){
    res  <- list()
    alltype <- unique(metaG[[1]])
    for(type in alltype){
        res[[type]] <- rownames(metaG)[metaG[[1]] == type]
        if (matrixN){
            res[[type]] <- gsub('-','.',res[[type]])
        }
    }
    res
}

f_br_cluster_f <- function(sObject, lc_groupN){
    lc_filter <- unlist(unique(sObject[[lc_groupN]]))
    lc_filter <- lc_filter[!is.na(lc_filter)]
    lc_filter
}

f_br_cluster <- function(sObject, lc_groupN, lc_labelN, lc_prop = F){
    lc_g <- f_metaG2G(sObject[[lc_groupN]])
    lc_l <- sObject[[lc_labelN]]
    lc_l[[1]] <- as.character(lc_l[[1]])
    res <- data.frame(row.names = f_br_cluster_f(lc_l, lc_labelN))
    if(lc_prop){
        for(Nm in names(lc_g)){
            tmp <- prop.table(table(lc_l[lc_g[[Nm]],]))
            res[[Nm]] <- tmp[rownames(res)]
        }
    }else{
        for(Nm in names(lc_g)){
            tmp <- table(lc_l[lc_g[[Nm]],])
            res[[Nm]] <- tmp[rownames(res)]
        }
    }
    res[is.na(res)] = 0
    res
}
```

## 标注函数

```r
f_add_annotation <- function(sObject, lc_ids,lc_metaName){
    Idents(sObject) <- sObject[[lc_metaName]]
    names(lc_ids) <- levels(sObject)
    sObject <- RenameIdents(sObject, lc_ids)
    sObject[[lc_metaName]] <- Idents(sObject)
    sObject
}

f_mergeMeta_pre <- function(scRNA, metaN, baseMetaN){
    scRNA[[metaN]] <- as.character(scRNA[[baseMetaN]][[1]])
    scRNA
}
f_mergeMeta <- function(scRNA, metaN, metaData){
    scRNA[[metaN]][rownames(metaData),] <- as.character(metaData[[1]])
    scRNA
}
f_mergeMeta_end <- function(scRNA, metaN){
    scRNA[[metaN]][[1]] <- as.factor(scRNA[[metaN]][[1]])
    scRNA
}
```