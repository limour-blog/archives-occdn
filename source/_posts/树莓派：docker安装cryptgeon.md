---
title: 树莓派：Docker安装cryptgeon
tags: []
id: '1880'
categories:
  - - 树莓派
date: 2022-06-13 18:26:21
---

**emm，目前尚不支持arm64**

## 来源

[https://github.com/cupcakearmy/cryptgeon](https://github.com/cupcakearmy/cryptgeon)

[https://www.bilibili.com/video/BV1YB4y117b5](https://www.bilibili.com/video/BV1YB4y117b5)

## 部署

```yml
# docker-compose.yml

version: '3.7'

services:
  memcached:
    image: memcached:1-alpine
    entrypoint: memcached -m 128M -I 4M # Limit to 128 MB Ram, 4M per entry, customize at free will.

  app:
    image: cupcakearmy/cryptgeon:latest
    depends_on:
      - memcached
    environment:
      SIZE_LIMIT: 4M
    ports:
      - 5081:5000
```

*   dos2unix docker-compose.yml
*   docker-compose up -d
*   docker-compose logs
*   docker-compose down  

## amd机器上的示例

[https://j11.fun/cryptgeon](https://j11.fun/cryptgeon)