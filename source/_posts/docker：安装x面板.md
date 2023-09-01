---
title: Docker：安装X面板
tags: []
id: '2001'
categories:
  - - 运维
date: 2022-07-07 21:19:31
---

## 来源

[https://github.com/vaxilu/x-ui](https://github.com/vaxilu/x-ui)

[https://github.com/Chasing66/beautiful\_docker](https://github.com/Chasing66/beautiful_docker)

## 部署

```yml
version: '3.8'
services:
  nui:
    image: enwaiax/x-ui
    container_name: nui
    volumes:
      - ./db/:/etc/x-ui/
      - ./cert/:/root/cert/
    restart: unless-stopped
    network_mode: host
```

*   mkdir xu && cd xu
*   nano docker-compose.yml
*   sudo docker-compose up -d
*   NPM面板反代 http://docker0:54321
*   账号`admin`密码`admin`登录