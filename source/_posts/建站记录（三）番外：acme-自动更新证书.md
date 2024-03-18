---
title: 建站记录（三）番外：acme 自动更新证书
tags: []
id: '1468'
categories:
  - - 运维
date: 2022-02-17 18:43:47
---

## 第一步 安装 acme.sh

*   安装 proxychans4

```shell
# https://github.com/rofl0r/proxychains-ng/releases 上传源码
tar -zxvf proxychains-ng-4.15.tar.gz 
cd proxychains-ng-4.15
yum groupinstall "Development Tools" "Development Libraries" # apt install build-essential 
./configure --prefix=/usr --sysconfdir=/etc
make && make install
make install-config
nano -K /etc/proxychains.conf
# socks5 127.0.0.1 20808
```

*   安装 xray

```shell
# https://github.com/XTLS/Xray-core/releases 上传 Xray-linux-64.zip
unzip Xray-linux-64.zip
```

*   开启 xray

```shell
# 上传xray配置文件
# 修改格式
vi ./xui2.json
:set ff
:set ff=unix
:wq
# 运行
./xray run -c ./xui2.json &
# 查看
jobs
```

*   xray的常见配置文件示例

```json
{
  "dns": {
    "hosts": {
      "domain:googleapis.cn": "googleapis.com"
    },
    "servers": [
      "1.1.1.1"
    ]
  },
  "inbounds": [
    {
      "port": 20808,
      "protocol": "socks",
      "settings": {
        "auth": "noauth",
        "udp": true,
        "userLevel": 8
      },
      "sniffing": {
        "destOverride": [
          "http",
          "tls"
        ],
        "enabled": true
      },
      "tag": "socks"
    },
    {
      "port": 20809,
      "protocol": "http",
      "settings": {
        "userLevel": 8
      },
      "tag": "http"
    }
  ],
  "log": {
    "loglevel": "warning"
  },
  "outbounds": [
    {
      "mux": {
        "concurrency": 8,
        "enabled": false
      },
      "protocol": "vless",
      "settings": {
        "vnext": [
          {
            "address": "your.vless.com",
            "port": 443,
            "users": [
              {
                "encryption": "none",
                "flow": "",
                "id": "12345678-1234-1234-1234-12345678abcd",
                "level": 8,
                "security": "auto"
              }
            ]
          }
        ]
      },
      "streamSettings": {
        "grpcSettings": {
          "multiMode": false,
          "serviceName": "yourservicepathname"
        },
        "network": "grpc",
        "security": "tls",
        "tlsSettings": {
          "allowInsecure": false,
          "serverName": "your.vless.com"
        }
      },
      "tag": "proxy"
    },
    {
      "protocol": "freedom",
      "settings": {},
      "tag": "direct"
    },
    {
      "protocol": "blackhole",
      "settings": {
        "response": {
          "type": "http"
        }
      },
      "tag": "block"
    }
  ],
  "policy": {
    "levels": {
      "8": {
        "connIdle": 300,
        "downlinkOnly": 1,
        "handshake": 4,
        "uplinkOnly": 1
      }
    },
    "system": {
      "statsOutboundUplink": true,
      "statsOutboundDownlink": true
    }
  },
  "routing": {
    "domainStrategy": "IPIfNonMatch",
    "rules": [
      {
        "ip": [
          "1.1.1.1"
        ],
        "outboundTag": "proxy",
        "port": "53",
        "type": "field"
      }
    ]
  },
  "stats": {}
}
```

*   安装 acme.sh

**proxychains 只会代理 TCP 连接，而 ping 使用的是 ICMP。记住这一点即可。**

```shell
proxychains4 bash
curl https://get.acme.sh  sh
```

## 第三步 停止代理

```shell
jobs
fg
^C
```

## 第四步 查看cron

```shell
crontab -l
51 0 * * * "/root/.acme.sh"/acme.sh --cron --home "/root/.acme.sh" > /dev/null
```

## 第五步 创建证书

```shell
export LE_WORKING_DIR="/root/.acme.sh"
export CF_Key=""
export CF_Email=""
alias acme.sh="/root/.acme.sh/acme.sh"
acme.sh --register-account -m limour@limour.top
acme.sh --issue --dns dns_cf -d *.limour.top -d limour.top -d *.frp.limour.top --server https://acme-v02.api.letsencrypt.org/directory
```

## 第六步 配置证书

*   修改httpd配置

```apache
SSLCertificateFile /etc/letsencrypt/live/fullchain.pem
SSLCertificateKeyFile /etc/letsencrypt/live/privkey.pem
```

*   安装证书

```shell
acme.sh --install-cert -d *.limour.top \
--key-file       /etc/letsencrypt/live/privkey.pem  \
--fullchain-file /etc/letsencrypt/live/fullchain.pem \
--reloadcmd     "systemctl restart httpd"
```

![](https://img.limour.top/archives_2023/blog/image-3.webp)