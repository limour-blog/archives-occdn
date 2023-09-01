---
title: 通过conda安装纯净环境的cBioPortal
tags: []
id: '1577'
categories:
  - - 数据库
  - - 生信
date: 2022-02-27 22:36:33
---

*   conda create -n rcBioPortal -c conda-forge r-base=4.1.2 -y
*   conda activate rcBioPortal
*   conda install -c conda-forge r-cgdsr=1.3.0 -y
*   conda install -c conda-forge r-irkernel=1.3 -y
*   R
*   IRkernel::installspec(name='rcBioPortal', displayname='r-cBioPortal')