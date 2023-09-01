---
title: Rstudio：ggThemeAssist
tags: []
id: '1682'
categories:
  - - 生信
  - - 绘图
date: 2022-04-14 02:01:16
---

*   conda activate r\_4\_1\_3
*   conda install -c conda-forge r-ggplot2=3.3.5 -y
*   conda install -c conda-forge r-shiny=1.7.1 -y
*   conda install -c conda-forge r-miniui=0.1.1.1 -y
*   conda install -c conda-forge r-biocmanager=1.30.16 -y
*   conda install -c conda-forge r-devtools=2.4.3 -y
*   conda install -c conda-forge wget=1.20.3 -y
*   wget https://github.com/calligross/ggthemeassist/archive/refs/heads/master.zip -O ggthemeassist.zip
*   devtools::install\_local('github-repo/ggthemeassist.zip')
*   docker restart Rstudio