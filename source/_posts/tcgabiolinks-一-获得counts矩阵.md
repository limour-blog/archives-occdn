---
title: TCGAbiolinks (一) 获得counts矩阵
tags: []
id: '1655'
categories:
  - - 数据库
  - - 生信
date: 2022-03-31 00:09:23
---

## 加载包

*   更新包：
*   BiocManager::install("BioinformaticsFMRP/TCGAbiolinksGUI.data")
*   BiocManager::install("BioinformaticsFMRP/TCGAbiolinks")
*   packageVersion("TCGAbiolinks") # 2.25.0

```r
library(TCGAbiolinks)
library(plyr)
library(SummarizedExperiment)
```

## 查看信息

```r
# 查看癌症类型
TCGAbiolinks:::getGDCprojects()$project_id 
# 查看对应癌症的数据类型
TCGAbiolinks:::getProjectSummary('TCGA-PRAD') # 以前列腺癌为例
```

## 筛选数据

```r
# 一般的前列腺癌 GDC Data Portal 是 hg38 的
PRAD <- GDCquery(project = 'TCGA-PRAD',
                  data.category = "Transcriptome Profiling",
                  data.type = "Gene Expression Quantification", 
                  workflow.type = "STAR - Counts")

# 选择病例列 ，不加cols参数则是完整结果的全部列
PRAD_cases <- getResults(PRAD,cols=c("cases"))

# 选择癌组织数据
PRAD_tp  <- TCGAquery_SampleTypes(barcode = PRAD_cases, typesample = "TP")

PRAD_D <- GDCquery(project = 'TCGA-PRAD',
                  data.category = "Transcriptome Profiling",
                  data.type = "Gene Expression Quantification", 
                  workflow.type = "STAR - Counts",
                  barcode = PRAD_tp)
```

## 获取数据

```r
PRAD_D <- GDCquery(project = 'TCGA-PRAD',
                  data.category = "Transcriptome Profiling",
                  data.type = "Gene Expression Quantification", 
                  workflow.type = "STAR - Counts",
                  barcode = PRAD_tp)

GDCdownload(query = PRAD_D)

PRAD <- GDCprepare(query = PRAD_D, save = TRUE, save.filename = "PRAD.rda")
```

## 获取矩阵

```r
counts <- PRAD@assays@data$unstranded
colnames(counts) <- PRAD@colData$patient
rownames(counts) <- PRAD@rowRanges$gene_name
```