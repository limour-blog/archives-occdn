---
title: Docker：rstudio-server
tags: []
id: '1677'
categories:
  - - 环境
  - - 生信
date: 2022-04-14 00:32:01
---

## 第一步 拉取RStudio

```shell
docker image pull dceoy/rstudio-server
```

## 第二步 编写自动内网穿透脚本

```shell
#!/usr/bin/sh
HOME=/home/rstudio
nohup $HOME/dev/frp_0.38.0_linux_amd64/frpc -c $HOME/etc/frpc.ini > $HOME/log/frp.log 2>&1 &
/usr/lib/rstudio-server/bin/rserver --server-daemonize=0 --server-app-armor-enabled=0
/bin/bash
```

/home/gene/zl\_liu/rstudio有以上start\_frp.sh，赋予可执行权限

```ini
[common]
server_addr = ***.limour.top
server_port = ***
protocol = kcp
tls_enable = true
token = ***
user = zyy

[web02]
type = http
local_ip = 127.0.0.1
local_port = 8787
use_compression = true
subdomain = z**
```

/home/gene/zl\_liu/rstudio下有etc、log文件夹；etc里有以上frpc.ini配置文件；权限开放

## 第三步 编写docker启动脚本

```shell
#!/bin/sh
docker run -d -p 57022:8787 --name Rstudio \
--restart always \
-m 60416M --memory-swap -1 \
-c 1024 \
--cpus 16 \
-v /home/gene/zl_liu/rstudio:/home/rstudio \
-v /home/gene/upload:/home/rstudio/upload \
-v /home/gene/zl_liu/jupytera/dev:/home/rstudio/dev \
-w /home/rstudio \
--entrypoint /home/rstudio/start_frp.sh \
dceoy/rstudio-server
```