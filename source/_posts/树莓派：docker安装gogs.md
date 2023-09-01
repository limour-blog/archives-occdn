---
title: 树莓派：Docker安装gogs
tags: []
id: '1883'
categories:
  - - 树莓派
date: 2022-06-23 19:24:09
---

## 来源

[https://github.com/gogs/gogs/tree/main/docker](https://github.com/gogs/gogs/tree/main/docker)

[https://www.composerize.com/](https://www.composerize.com/)

## 部署

```yml
version: '3.3'
services:
    gogs:
        container_name: gogs
        ports:
            - '11022:22'
            - '11880:3000'
        volumes:
            - './gogs:/data'
        image: gogs/gogs
```

*   mkdir gogs
*   cd gogs
*   nano -K docker-compose.yml
*   \# dos2unix docker-compose.yml
*   docker-compose up -d

## 反代

```ini
[common]
server_addr = frp.limour.top
server_port = 21000
tls_enable = true
token = ***
user = rasp_app

[gogs_ssh]
type = tcp
local_ip = 127.0.0.1
local_port = 11022
remote_port = 21423

[gogs_web]
type = http
local_ip = 127.0.0.1
local_port = 11880
use_compression = true
subdomain = gogs
```

*   docker restart frpc\_app

```yml
version: '3.3'
services:
    frpc:
        container_name: frpc_app
        restart: always
        volumes:
            - '/home/pi/frp_app/frpc.ini:/frp/frpc.ini'
        network_mode: host
        image: stilleshan/frpc
```

## 演示

[https://gogs.frp.limour.top/limour/qndxx](https://gogs.frp.limour.top/limour/qndxx)