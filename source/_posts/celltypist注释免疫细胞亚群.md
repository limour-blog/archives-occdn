---
title: CellTypist注释免疫细胞亚群
tags: []
id: '1541'
categories:
  - - 分群
  - - 生信
date: 2022-02-22 22:05:17
---

## 第一步 加载包

```r
Sys.setenv(RETICULATE_PYTHON = "/opt/conda/envs/celltypist/bin/python3.8")
library(reticulate)
scanpy = import("scanpy")
celltypist = import("celltypist")
pandas <- import("pandas")
numpy = import("numpy")
py_config()
```

## 第二步 加载数据

```r
library(Seurat)
sce <- readRDS("~/upload/yy_zhang_data/scRNA-seq/pca.celltype.rds")
Myeloid <- subset(sce, cell_type=='Myeloid')
```

## 第三步 seurat转scanpy

```r
# 数据矩阵, scanpy与Seurat的行列定义相反
adata.X = numpy$array(t(as.matrix(Myeloid[['RNA']]@counts)))
# 对每个细胞的观察
adata.obs = pandas$DataFrame(Myeloid@meta.data[colnames(Myeloid[['RNA']]@counts),])
# 对基因矩阵的注释
adata.var = pandas$DataFrame(data.frame(gene = rownames(Myeloid[['RNA']]@counts), row.names = rownames(Myeloid[['RNA']]@counts)))
 
# 组装AnnData对象
adata = scanpy$AnnData(X = adata.X, obs=adata.obs, var=adata.var)
```

## 第四步 进行预测

```r
model = celltypist$models$Model$load(model = 'Immune_All_AddPIP.pkl')
model$cell_types
scanpy$pp$normalize_total(adata, target_sum=1e4)
scanpy$pp$log1p(adata)
predictions = celltypist$annotate(adata, model = 'Immune_All_AddPIP.pkl', majority_voting = T)
```

## 第五步 预测添加到seurat对象

```r
Myeloid = AddMetaData(Myeloid, predictions$predicted_labels)
```