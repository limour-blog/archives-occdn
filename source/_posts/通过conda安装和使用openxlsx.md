---
title: 通过conda安装和使用openxlsx
tags: []
id: '1563'
categories:
  - - 未分类
  - - 生信
date: 2022-02-24 21:35:15
---

*   conda install -c conda-forge r-openxlsx=4.2.5 -y

```r
openxlsx::read.xlsx(xlsxFile = "marker.xlsx", sheet = 1, colNames = F)
```