---
title: 通过conda安装纯净环境的ggsurvplot
tags: []
id: '1820'
categories:
  - - 生信
  - - 统计学
date: 2022-05-09 22:07:29
---

*   conda create -n ggsurvplot -c conda-forge r-base=4.1.3 -y
*   conda activate ggsurvplot
*   conda install -c conda-forge r-tidyverse=1.3.1 -y
*   conda install -c conda-forge r-irkernel -y
*   conda install -c conda-forge r-survival=3.3\_1 -y
*   conda install -c conda-forge r-survminer=0.4.9 -y
*   conda install -c conda-forge r-fdrtool -y
*   R
*   install.packages('survRM2')
*   IRkernel::installspec(name='ggsurvplot', displayname='r-ggsurvplot')