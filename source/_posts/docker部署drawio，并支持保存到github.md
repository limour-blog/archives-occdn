---
title: Docker部署drawio，并支持保存到github
tags: []
id: '2670'
categories:
  - - 开源
date: 2023-03-29 17:24:20
---

```yml
version: '3.3'
services:
  drawio:
    container_name: draw
    environment:
      - TZ=Asia/Shanghai
    ports:
      - '7080:8080'
    # volumes:
    #   - ./docker-entrypoint.sh:/docker-entrypoint.sh
    image: jgraph/drawio
```

*   mkdir -p ~/app/draw && cd ~/app/draw && nano docker-compose.yml

*   sudo docker-compose up -d && sudo docker-compose logs

*   sudo docker-compose cp drawio:/docker-entrypoint.sh ./docker-entrypoint.sh

*   \# 将"urlParams\['gh'\] = '0';改成"urlParams\['gh'\] = '1';

*   #取消volumes的注释

*   sudo docker-compose down && sudo docker-compose up -d