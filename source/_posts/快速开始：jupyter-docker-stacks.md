---
title: 快速开始：Jupyter Docker Stacks
tags: []
id: '1530'
categories:
  - - 环境
  - - 生信
date: 2022-02-22 19:22:15
---

[https://hub.docker.com/r/jupyter/datascience-notebook/tags](https://hub.docker.com/r/jupyter/datascience-notebook/tags)

## 第一步 拉取 Jupyter Docker Stacks

```shell
docker pull jupyter/datascience-notebook:r-4.1.2
```

## 第二步 编写自动内网穿透脚本

```shell
#!/usr/bin/sh
HOME=/home/jovyan
nohup $HOME/dev/frp_0.38.0_linux_amd64/frpc -c $HOME/etc/frpc.ini > $HOME/log/frp.log 2>&1 &
```

## 第三步 编写docker启动脚本

*   初次启动

```shell
#!/bin/sh
docker run -d -p 57002:8888 --name jupyterR \
--restart always \
-m 60416M --memory-swap -1 \
-c 1024 \
--cpus 16 \
-v /home/gene/upload:/home/jovyan/upload \
-v /home/gene/zl_liu/jupytera/before-notebook.d:/usr/local/bin/before-notebook.d \
-v /home/gene/zl_liu/jupytera/dev:/home/jovyan/dev \
-v /home/gene/zl_liu/jupytera/etc:/home/jovyan/etc \
-v /home/gene/zl_liu/jupytera/log:/home/jovyan/log \
-v /home/gene/zl_liu/jupytera:/home/jovyan/old \
jupyter/datascience-notebook:r-4.1.2 \
start-notebook.sh --NotebookApp.token='***'
```

*   备份opt文件夹

```shell
# 主机上
mkdir ~/upload/zl_liu
chmod 777 ~/upload/zl_liu/

# docker里
cp -r /opt ~/upload/zl_liu/
```

*   后续启动

```shell
#!/bin/sh
docker run -d -p 57002:8888 --name jupyterR \
--restart always \
-m 60416M --memory-swap -1 \
-c 1024 \
--cpus 16 \
-v /home/gene/upload:/home/jovyan/upload \
-v /home/gene/zl_liu/jupytera/before-notebook.d:/usr/local/bin/before-notebook.d \
-v /home/gene/zl_liu/jupytera/dev:/home/jovyan/dev \
-v /home/gene/zl_liu/jupytera/etc:/home/jovyan/etc \
-v /home/gene/zl_liu/jupytera/log:/home/jovyan/log \
-v /home/gene/upload/zl_liu/opt:/opt \
jupyter/datascience-notebook:r-4.1.2 \
start-notebook.sh --NotebookApp.token='***'
```