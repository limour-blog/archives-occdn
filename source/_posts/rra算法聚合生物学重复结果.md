---
title: RRA算法聚合生物学重复结果
tags: []
id: '1658'
categories:
  - - 未分类
  - - 生信
date: 2022-04-01 17:33:58
---

## 第一步 读取数据

```r
library(Seurat)
library(plyr)
library(dplyr)
library(patchwork)
library(purrr)
f_metadata_removeNA <- function(sObject, lc_groupN){
    sObject@meta.data <- sObject@meta.data[colnames(sObject),]
    sObject <- subset(x = sObject, !!sym(lc_groupN)%in%f_br_cluster_f(sObject, lc_groupN))
    sObject
}
f_br_cluster_f <- function(sObject, lc_groupN){
    lc_filter <- unlist(unique(sObject[[lc_groupN]]))
    lc_filter <- lc_filter[!is.na(lc_filter)]
    lc_filter
}

scRNA_split = readRDS("~/zlliu/R_output/21.09.21.SingleR/scRNA.rds")
scRNA_split <- f_metadata_removeNA(scRNA_split, 'Region')

n_ExN <- c('L4 IT','L5 IT','L5 ET','IT','L6b','L5/6 IT Car3','L6 IT','L2/3 IT','L5/6 NP','L6 IT Car3','L6 CT')
n_InN <- c('Lamp5','Pvalb','Sst','Vip','Sncg','PAX6')
n_NoN <- c('Astro','Endo','Micro-PVM','OPC','Oligo','Pericyte','VLMC')
n_groups <- list(NoN=n_NoN, ExN=n_ExN, InN=n_InN)

f_listUpdateRe <- function(lc_obj, lc_bool, lc_item){
    lc_obj[lc_bool] <- rep(lc_item,times=sum(lc_bool))
    lc_obj
}
f_grouplabel <- function(lc_meta.data, lc_groups){
    res <- lc_meta.data[[1]]
    for(lc_g in names(lc_groups)){
        lc_bool = (res %in% lc_groups[[lc_g]])
        for(c_n in colnames(lc_meta.data)){
            lc_bool = lc_bool  (lc_meta.data[[c_n]] %in% lc_groups[[lc_g]])
        }
        res <- f_listUpdateRe(res, lc_bool, lc_g)
    }
    names(res) <- rownames(lc_meta.data)
    res
}

scRNA_split[['n_groups']] <- f_grouplabel(scRNA_split[[c("hM1_hmca_class")]], n_groups)
sc_Neuron <- subset(x = scRNA_split, n_groups %in% c("InN", "ExN"))

samples <- SplitObject(object = sc_Neuron, split.by = 'orig.ident')
```

## 第二步 计算差异基因

```r
DEGs <- list()
for (name in names(samples)){
    tmp <- samples[[name]]
    Idents(tmp) <- 'Region'
    DEGs[[name]] = FindAllMarkers(tmp, only.pos = T)
}
```

## 第三步 整合数据

```r
f_dflist_subset <- function(dflist, nameN=NULL, ...){
    res <- list()
    for (name in names(dflist)){
        res[[name]] <- subset(dflist[[name]], ...)
    }
    res
}
require(RobustRankAggreg)
f_dflist_RRA <- function(dflist, nameN, N, orderN, decreasing=T){
    res <- list()
    for (name in names(dflist)){
        tmp <- dflist[[name]]
        if (nrow(tmp) == 1){
            res[[name]] <- tmp[1, nameN]
        }else if(nrow(tmp) >=2){
            res[[name]] <- tmp[order(tmp[[orderN]],decreasing = decreasing), nameN]
        }
    }
    if(length(res) < 1){ return(NULL)}
    aggregateRanks(glist = res, N = N)
}
```

```r
res <- data.frame()
for(name in unique(sc_Neuron@meta.data$Region)){
    tmp <- f_dflist_subset(DEGs, name, cluster==nameN)
    tmp <- f_dflist_RRA(tmp, 'gene', N=24223, 'avg_log2FC', decreasing = T)
    if(!is.null(tmp)){
        tmp[['cluster']] <- name
        res <- rbind(res, tmp)
    }
}
```

## 第四步 保存结果

```r
require(openxlsx)
wb <- createWorkbook()
for (name in names(samples)){
    addWorksheet(wb = wb, sheetName = name)
    writeData(wb = wb, sheet = name, x = DEGs[[name]])
}
addWorksheet(wb = wb, sheetName = 'RRA')
writeData(wb = wb, sheet = 'RRA', x = res)
saveWorkbook(wb, "DEG_Brain_Regin_Neuron.xlsx", overwrite = TRUE)
```