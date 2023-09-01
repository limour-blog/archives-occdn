---
title: SingleR注释免疫细胞亚群
tags: []
id: '1554'
categories:
  - - 分群
  - - 生信
date: 2022-02-24 01:28:28
---

## 第一步 下载自带的参考数据库（仅需一次，已经运行过了，跳过）

```r
library(celldex)
ref_HPCD <- HumanPrimaryCellAtlasData() 
ref_BFD <- BlueprintEncodeData() 
ref_MRD <- MouseRNAseqData() 
ref_IGD <- ImmGenData() 
ref_DICED <- DatabaseImmuneCellExpressionData() 
ref_NHD <- NovershternHematopoieticData() 
ref_MID <- MonacoImmuneData()
save(list = c('ref_HPCD', 'ref_BFD', 'ref_MRD', 'ref_IGD', 'ref_DICED', 'ref_NHD', 'ref_MID'), file = '~/upload/zl_liu/celldexData/celldex.rdata')
```

*   **BlueprintEncodeData**（人）   
    Blueprint (Martens and Stunnenberg 2013) and Encode (The ENCODE Project Consortium 2012)
*   **DatabaseImmuneCellExpressionData** （人）  
    The Database for Immune Cell Expression(/eQTLs/Epigenomics)(Schmiedel et al. 2018)
*   **HumanPrimaryCellAtlasData**（人）  
    The Human Primary Cell Atlas (Mabbott et al. 2013)
*   **MonacoImmuneData** （人）  
    Monaco Immune Cell Data - GSE107011 (Monaco et al. 2019)
*   **NovershternHematopoieticData** （人）  
    Novershtern Hematopoietic Cell Data - GSE24759
*   **ImmGenData**（鼠）  
    The murine ImmGen (Heng et al. 2008)
*   **MouseRNAseqData**（鼠）  
    A collection of mouse data sets downloaded from GEO (Benayoun et al. 2019).

## 第二步 进行自动注释

*   加载参考数据集

```r
library(celldex)
load(file = '~/upload/zl_liu/celldexData/celldex.rdata')
```

*   读入待注释对象

```r
library(SingleR)
library(Seurat)
sce <- readRDS('Myeloid.rds')
sce@meta.data <- readRDS('myeloid_metadata.rds')
DimPlot(sce, reduction = 'umap', label = T, repel = T)
```

*   进行分群

```r
require(dplyr)
f_FindNeighbors_integrated_s <- function(scRNA){
    DefaultAssay(scRNA) <- 'integrated'
    scRNA <- ScaleData(scRNA)
    scRNA <- RunPCA(scRNA)
    scRNA <- RunUMAP(scRNA, dims = 1:30)
}
f_FindNeighbors_integrated <- function(scRNA, resolution = 0.5, reduction='pca'){
    scRNA <- scRNA %>% FindNeighbors(reduction = reduction) %>%  FindClusters(resolution = resolution)
    scRNA[[paste0('h_resolution_', resolution)]] <- Idents(scRNA)
    scRNA
}
```

```r
sce <- f_FindNeighbors_integrated_s(sce)
sce <- f_FindNeighbors_integrated(sce, 1.5)
DimPlot(sce, reduction = 'umap', label = T, repel = T)
```

*   进行预测

```r
sce_for_SingleR <- GetAssayData(sce, slot="data")
clusters <- sce[['h_resolution_1.5']][[1]]
pred_MID <- SingleR(test = sce_for_SingleR, ref = ref_MID, labels = ref_MID$label.main,
                          method = "cluster", clusters = clusters, 
                          assay.type.test = "logcounts", assay.type.ref = "logcounts")
pred_NHD <- SingleR(test = sce_for_SingleR, ref = ref_NHD, labels = ref_NHD$label.main,
                          method = "cluster", clusters = clusters, 
                          assay.type.test = "logcounts", assay.type.ref = "logcounts")
cellType=data.frame(ClusterID=levels(sce@meta.data$seurat_clusters),
                    pred_MID=pred_MID$labels,
                    pred_NHD=pred_NHD$labels )
head(cellType)
sce@meta.data$pred_MID <- cellType[match(clusters,cellType$ClusterID), 'pred_MID']
sce@meta.data$pred_NHD <- cellType[match(clusters,cellType$ClusterID), 'pred_NHD']
```

## 第三步 查看注释质量

*   肉眼观察

```r
DimPlot(sce, reduction = 'umap', group.by = 'pred_MID', label = T, repel = T)
DimPlot(sce, reduction = 'umap', group.by = 'pred_NHD', label = T, repel = T)
DimPlot(sce, reduction = 'umap', group.by = 'majority_voting', label = T, repel = T)
DimPlot(sce, reduction = 'umap', group.by = 'immune_annotion', label = T, repel = T)
```

*   plotScoreHeatmap(pred\_MID, show.labels = T, show.pruned = T, show\_colnames = T)
*   plotDeltaDistribution(pred\_MID)