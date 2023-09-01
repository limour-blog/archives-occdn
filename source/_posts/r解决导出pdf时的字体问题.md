---
title: R解决导出PDF时的字体问题
tags: []
id: '2331'
categories:
  - - 绘图
date: 2022-09-13 18:59:47
---

## 安装包

*   conda activate clusterprofiler
*   conda install -c conda-forge r-sysfonts -y
*   conda install -c conda-forge r-showtext -y

## 绘图

```R
sysfonts::font_add("Arial Narrow", "~/font/Arial Narrow.ttf") # 添加字体
sysfonts::font_families() # 检查是否添加成功
{pdf(file = 'B_bnCov.plot.pdf', width = 12, height = 12)
 showtext::showtext_begin()
 print(bnCov$plot)
 showtext::showtext_end()
dev.off()}
```