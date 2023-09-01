---
title: scVelo准备3：从Seurat输出标注和UMAP
tags: []
id: '1803'
categories:
  - - 拟时序
  - - 生信
date: 2022-05-08 01:21:21
---

```r
library(Seurat)
library(tidyverse)
library(stringr)
sce <- readRDS("~/upload/zl_liu/data/pca.rds")
```

```r
f_scVelo_group_by <- function(df, groupN){
    res <- list()
    for(n in unique(as.character(df[[groupN]]))){
        res[[n]] <- df[df[[groupN]] == n,]
    }
    res
}
f_scVelo_get_reduction <- function(dfl, cell.embeddings){
    for(n in names(dfl)){
        dfl[[n]] <- cbind(dfl[[n]], cell.embeddings[rownames(dfl[[n]]),])
    }
    dfl
}
f_scVelo_str_extract_rowN <- function(dfl, grepP='(?=.{10})([AGCT]{16})(?=-1)'){
    for(n in names(dfl)){
        rownames(dfl[[n]]) <- str_extract(rownames(dfl[[n]]), grepP)
    }
    dfl
}

test <- f_scVelo_group_by(sce[[c('patient_id','cell_type_fig3')]], 'patient_id')
test <- f_scVelo_get_reduction(test, sce@reductions$umap@cell.embeddings)
test <- f_scVelo_get_reduction(test, sce@reductions$pca@cell.embeddings)
test <- f_scVelo_str_extract_rowN(test)
```

```r
f_scVelo_label_reduction <- function(dfl, workdir, groupN, outDir){
    outDir = file.path(workdir, outDir, 'velocyto', 'metadata.csv')
    write.csv(dfl[[groupN]], outDir)
}

work='/home/jovyan/upload/zl_liu/data/data/res'
f_scVelo_label_reduction(test, work, 'patient1', 'hPB003')
f_scVelo_label_reduction(test, work, 'patient3', 'hPB004')
f_scVelo_label_reduction(test, work, 'patient4', 'hPB006')
f_scVelo_label_reduction(test, work, 'patient5', 'hPB007')
```