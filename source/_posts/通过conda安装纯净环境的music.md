---
title: 通过conda安装纯净环境的MuSiC
tags: []
id: '1822'
categories:
  - - 生信
  - - 组织测序
date: 2022-05-09 22:33:57
---

*   conda create -n music -c conda-forge r-base=4.1.3 -y
*   conda activate music
*   conda install -c conda-forge r-tidyverse=1.3.1 -y
*   conda install -c conda-forge r-irkernel -y
*   conda install -c conda-forge r-devtools=2.4.3 -y
*   conda install -c conda-forge r-biocmanager=1.30.16 -y
*   conda install -c bioconda bioconductor-biobase=2.54.0 -y
*   ~/dev/xray/xray -c ~/etc/xui2.json &
*   wget -e "https\_proxy=http://127.0.0.1:20809" https://github.com/xuranw/MuSiC/archive/refs/heads/master.zip -O MuSiC-master.zip
*   devtools::install\_local('MuSiC-master.zip')
*   IRkernel::installspec(name='music', displayname='r-music')