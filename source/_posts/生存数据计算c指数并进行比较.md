---
title: 生存数据计算C指数并进行比较
tags: []
id: '2271'
categories:
  - - 统计学
date: 2022-08-20 09:34:13
---

## 安装补充包

*   [conda activate ggsurvplot](https://occdn.limour.top/2267.html)
*   conda install -c bioconda bioconductor-survcomp -y

## 计算C指数

```R
library(survcomp)
C_index1 <- concordance.index(x=test_data$GS, surv.time=test_data$time, surv.event=test_data$status, method="noether")
C_index2 <- concordance.index(x=test_data$T, surv.time=test_data$time, surv.event=test_data$status, method="noether")
C_index3 <- concordance.index(x=test_data$Risk_Score_Ridge, surv.time=test_data$time, surv.event=test_data$status, method="noether")
```

## 比较C指数

```R
cindex.comp(C_index1, C_index3)
cindex.comp(C_index2, C_index3)
```