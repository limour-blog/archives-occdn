---
title: Docker：部署轻量的代理客户端
tags: []
id: '2467'
categories:
  - - 开源
date: 2022-11-13 21:31:26
---

```yml
version: '3.3'
services:
    socks5:
        ports:
            - '20170:20170'
        container_name: socks5
        restart: always
        volumes:
            - './config.json:/etc/xray/config.json'
        image: teddysun/xray:1.6.1
```

*   mkdir -p ~/socks5 && cd ~/socks5

*   nano docker-compose.yml

*   nano ./config.json

*   sudo docker-compose up -d