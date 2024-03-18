---
title: 树莓派：Docker安装openwebrx
tags: []
id: '1885'
categories:
  - - 树莓派
date: 2022-06-23 21:08:53
---

## 来源

[https://github.com/jketterl/openwebrx/wiki/Getting-Started-using-Docker](https://github.com/jketterl/openwebrx/wiki/Getting-Started-using-Docker)

## 部署

```sh
 proxychains4 docker pull jketterl/openwebrx:stable
 docker volume create openwebrx-settings
 docker run --device /dev/bus/usb -p 8073:8073 \
 --tmpfs=/tmp/openwebrx \
 -v openwebrx-settings:/var/lib/openwebrx \
 jketterl/openwebrx:stable
 docker exec -it [container] /bin/bash
 python3 /opt/openwebrx/openwebrx.py admin adduser limour
```

## 演示

[https://j11.fun/radio](https://j11.fun/radio)

![](https://img.limour.top/archives_2023/blog/20220623210838.webp)