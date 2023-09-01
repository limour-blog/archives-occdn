---
title: Jupyter Docker Stacks 进阶配置
tags: []
id: '1532'
categories:
  - - 环境
  - - 生信
date: 2022-02-22 19:37:06
---

## 第一步 开启代码提示

```shell
pip3 install -i http://pypi.douban.com/simple --trusted-host pypi.douban.com jupyter_contrib_nbextensions
jupyter contrib nbextension install --user
pip3 install -i http://pypi.douban.com/simple --trusted-host pypi.douban.com  jupyter_nbextensions_configurator
jupyter nbextensions_configurator enable --user
pip3 install nbconvert==5.6.1 -i http://pypi.douban.com/simple --trusted-host pypi.douban.com
```

## 第二步 给conda换清华源

[https://mirror.tuna.tsinghua.edu.cn/help/anaconda/](https://mirror.tuna.tsinghua.edu.cn/help/anaconda/)

## 第三步 创建.Rprofile 写入以下内容

```r
options(BioC_mirror="https://mirrors.tuna.tsinghua.edu.cn/bioconductor")
options(CRAN="http://mirrors.tuna.tsinghua.edu.cn/CRAN/")
options(repr.plot.width=12, repr.plot.height=12)
options(ggrepel.max.overlaps = Inf)
Sys.setenv(R_REMOTES_NO_ERRORS_FROM_WARNINGS=TRUE)
```