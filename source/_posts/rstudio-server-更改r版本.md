---
title: Rstudio-server 更改R版本
tags: []
id: '1680'
categories:
  - - 环境
  - - 生信
date: 2022-04-14 01:42:14
---

## 第一步 安装conda

```shell
wget https://mirrors.tuna.tsinghua.edu.cn/anaconda/miniconda/Miniconda3-py39_4.11.0-Linux-x86_64.sh
./Miniconda3-py39_4.11.0-Linux-x86_64.sh
source ~/.bashrc
```

```shell
conda install -c conda-forge nano=2.9.8 -y
nano -K .condarc
conda clean -i
```

添加清华镜像：[https://mirrors.tuna.tsinghua.edu.cn/help/anaconda/](https://mirrors.tuna.tsinghua.edu.cn/help/anaconda/)

## 第二步 安装R 4.1.3

```shell
conda create -n r_4_1_3 -c conda-forge r-base=4.1.3 -y
conda activate r_4_1_3
whereis R
# /home/rstudio/miniconda3/envs/r_4_1_3/bin/R
```

## 第三步 修改默认R版本

```shell
docker exec -it Rstudio /bin/bash
chmod 777 -R  /etc/rstudio/
exit 
```

```shell
nano -K /etc/rstudio/rserver.conf

# Server Configuration File
rsession-which-r=/home/rstudio/miniconda3/envs/r_4_1_3/bin/R
```

```shell
docker restart Rstudio
```