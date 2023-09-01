---
title: 甲骨文云，搭建Docker专用机
tags: []
id: '1898'
categories:
  - - 运维
date: 2022-06-29 04:25:25
---

## 第一步 申请新实例

*   映像选 Canonical-Ubuntu
*   添加 SSH 密钥中 为我生成密钥对 保存私钥和公钥
*   添加 SSH 密钥中 上载公共密钥文件 (.pub)，选择刚刚保存的公钥文件
*   安全列表 清除全部规则

## 第二步 CF域名解析并配置iptables

*   域名解析，开启小云朵
*   ssh登录服务器
*   更改iptables

```bash
#!/bin/bash
/usr/sbin/iptables -P INPUT ACCEPT
/usr/sbin/iptables -P FORWARD ACCEPT
/usr/sbin/iptables -P OUTPUT ACCEPT
/usr/sbin/iptables -F
/usr/sbin/iptables -I INPUT -p tcp -m multiport --dports 80,443 -s 173.245.48.0/20 -j ACCEPT
/usr/sbin/iptables -I INPUT -p tcp -m multiport --dports 80,443 -s 103.21.244.0/22 -j ACCEPT
/usr/sbin/iptables -I INPUT -p tcp -m multiport --dports 80,443 -s 103.22.200.0/22 -j ACCEPT
/usr/sbin/iptables -I INPUT -p tcp -m multiport --dports 80,443 -s 103.31.4.0/22 -j ACCEPT
/usr/sbin/iptables -I INPUT -p tcp -m multiport --dports 80,443 -s 141.101.64.0/18 -j ACCEPT
/usr/sbin/iptables -I INPUT -p tcp -m multiport --dports 80,443 -s 108.162.192.0/18 -j ACCEPT
/usr/sbin/iptables -I INPUT -p tcp -m multiport --dports 80,443 -s 190.93.240.0/20 -j ACCEPT
/usr/sbin/iptables -I INPUT -p tcp -m multiport --dports 80,443 -s 188.114.96.0/20 -j ACCEPT
/usr/sbin/iptables -I INPUT -p tcp -m multiport --dports 80,443 -s 197.234.240.0/22 -j ACCEPT
/usr/sbin/iptables -I INPUT -p tcp -m multiport --dports 80,443 -s 198.41.128.0/17 -j ACCEPT
/usr/sbin/iptables -I INPUT -p tcp -m multiport --dports 80,443 -s 162.158.0.0/15 -j ACCEPT
/usr/sbin/iptables -I INPUT -p tcp -m multiport --dports 80,443 -s 104.16.0.0/13 -j ACCEPT
/usr/sbin/iptables -I INPUT -p tcp -m multiport --dports 80,443 -s 104.24.0.0/14 -j ACCEPT
/usr/sbin/iptables -I INPUT -p tcp -m multiport --dports 80,443 -s 172.64.0.0/13 -j ACCEPT
/usr/sbin/iptables -I INPUT -p tcp -m multiport --dports 80,443 -s 131.0.72.0/22 -j ACCEPT
/usr/sbin/iptables -A INPUT -p tcp -m multiport --dports 80,443 -j DROP
```

*   sudo nano /etc/systemd/system/rc-local.service

```service
[Unit]
Description=/etc/rc.local Compatibility 
ConditionPathExists=/etc/rc.local 

[Service]
Type=forking 
ExecStart=/etc/rc.local start 
TimeoutSec=0 
StandardOutput=tty 
RemainAfterExit=yes 
SysVStartPriority=99 

```

*   sudo nano /etc/rc.local
*   sudo chmod +x /etc/rc.local
*   sudo systemctl enable rc-local
*   sudo systemctl start rc-local.service
*   sudo systemctl status rc-local.service
*   重启服务器
*   sudo iptables -L 查看是否生效

```sh
#!/bin/sh -e 
## rc.local
#start script
/home/ubuntu/c_iptables.sh
#end script
echo "added sucessfully!" > /tmp/added_script.log 
exit 0
```

## 第三步 安装docker

*   sudo apt update
*   sudo apt install apt-transport-https ca-certificates curl gnupg-agent software-properties-common
*   curl -fsSL https://download.docker.com/linux/ubuntu/gpg sudo apt-key add -
*   sudo add-apt-repository "deb \[arch=amd64\] https://download.docker.com/linux/ubuntu $(lsb\_release -cs) stable"
*   sudo apt update
*   sudo apt install docker-ce
*   sudo systemctl status docker
*   sudo curl -L "https://github.com/docker/compose/releases/download/v2.6.1/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
*   sudo chmod +x /usr/local/bin/docker-compose
*   docker-compose --version

## 第四步 安装 Nginx Proxy Manager

*   mkdir ngpm
*   cd ngpm
*   nano docker-compose.yml
*   sudo docker-compose up -d

```yml
version: '3'
services:
  app:
    image: 'jc21/nginx-proxy-manager:latest'
    restart: unless-stopped
    ports:
      - '80:80'
      - '81:81'
      - '443:443'
    volumes:
      - ./data:/data
      - ./letsencrypt:/etc/letsencrypt
```

*   登录到 http://ip:81
*   Email: admin@example.com
*   Password: changeme
*   反代 Nginx Proxy Manager
*   示例：https://npm.j11.fun/