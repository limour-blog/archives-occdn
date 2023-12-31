---
title: Docker：部署easyconnect
tags: []
id: '2692'
categories:
  - - 运维
date: 2023-04-15 14:45:42
---

深信服开发的**非自由**的 EasyConnect 代理软件就是依托答辩，想把它运行在 docker 中，并开放 Socks5 供宿主机连接以使用代理，保证不污染环境。使用的项目是[Hagb](https://github.com/Hagb)/**[docker-easyconnect](https://github.com/Hagb/docker-easyconnect)**

```yml
version: '3'
services:
  easyconnect:
    image: hagb/docker-easyconnect:cli
    restart: unless-stopped
    devices:
      - /dev/net/tun
    cap_add:
      - NET_ADMIN
    environment:
      - EC_VER=7.6.7
      - CLI_OPTS=-d 202.120.224.58:443 -u username -p password
      - TZ=Asia/Shanghai
    networks:
      - internal

  gost:
    restart: unless-stopped
    ports:
      - '13442:13443'
    image: ginuerzh/gost
    volumes:
      - ./ssl/fullchain.pem:/bin/cert.pem:ro
      - ./ssl/privkey.key:/bin/key.pem:ro
    command: -L="http+tls://limour:password@:13443?cert=cert.pem&key=key.pem&probe_resist=code:400&knock=www.library.fudan.edu.cn" -F="socks5://easyconnect:1080"
    networks:
      - internal

networks:
  internal:
    attachable: true
```

*   mkdir -p ~/app/easyconnect && cd ~/app/easyconnect && nano docker-compose.yml
*   证书来源：[Docker：使用 Gost 搭建 HTTPS 代理](https://occdn.limour.top/2582.html)
*   ping stuvpn.fudan.edu.cn # 202.120.224.58
*   sudo docker-compose up -d
*   sudo docker-compose logs

## 更新版本

*   sudo docker-compose pull && sudo docker-compose up -d --remove-orphans
*   sudo docker image prune