---
title: Docker：安装frp服务端与客户端
tags: []
id: '2004'
categories:
  - - 开源
date: 2022-07-07 23:56:58
---

## 来源

[https://hub.docker.com/r/snowdreamtech/frps](https://hub.docker.com/r/snowdreamtech/frps)

[https://github.com/fatedier/frp](https://github.com/fatedier/frp)

## 服务端部署

```yml
version: '3.3'
services:
    frps:
        restart: always
        network_mode: host
        volumes:
            - './frps.ini:/etc/frp/frps.ini'
            - './frps_log:/tmp/frps_log'
        container_name: frps
        image: snowdreamtech/frps
```

*   mkdir frps && cd frps && mkdir frps\_log
*   nano docker-compose.yml
*   nano frps.ini
*   docker-compose up -d
*   tail frps\_log/frps.log

```yml
[common]
tls_only = true
authentication_method = token
token = <token>
bind_port = 21000
bind_udp_port = 20999
kcp_bind_port = 21000
dashboard_port = 11750
dashboard_user = Limour
dashboard_pwd = <passwd>
allow_ports = 21001-21999
subdomain_host = limour.top
vhost_http_port = 21080
vhost_https_port = 21443

log_file = /tmp/frps_log/frps.log
log_level = info
log_max_days = 3
```

## 客户端部署

```yml
version: '3.3'
services:
    frpc_001:
        restart: always
        network_mode: host
        volumes:
            - './frpc.ini:/etc/frp/frpc.ini'
        container_name: frpc_001
        image: snowdreamtech/frpc
```

*   mkdir frpc\_001 && cd frpc\_001
*   nano docker-compose.yml
*   nano frpc.ini
*   sudo docker-compose up -d

```ini
[common]
server_addr = frp.limour.top
server_port = 21000
tls_enable = true
token = <token>
user = rasp4

[ssh]
type = tcp
local_ip = 127.0.0.1
local_port = 22
remote_port = 21122

[V2rayA]
type = http
local_ip = 127.0.0.1
local_port = 2017
use_compression = true
subdomain = v2r4
host_header_rewrite = 127.0.0.1

[CaaS]
type = http
use_compression = true
subdomain = caas4
plugin = http2https
plugin_local_addr = 127.0.0.1:8443
plugin_host_header_rewrite = 127.0.0.1
plugin_header_X-From-Where = frp

[app_web]
type = http
local_ip = 192.168.1.1
local_port = 80
use_compression = true
subdomain = app
http_user = Limour
http_pwd = <APP_PASSWORD>
```