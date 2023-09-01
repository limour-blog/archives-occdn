---
title: iptables：仅开放特定端口
tags: []
id: '2009'
categories:
  - - 运维
date: 2022-07-08 06:00:37
---

## 来源

[https://blog.bestucloud.com/tech/iptables-and-docker-conflicts/](https://blog.bestucloud.com/tech/iptables-and-docker-conflicts/)

[https://www.escapelife.site/posts/43256c71.html](https://www.escapelife.site/posts/43256c71.html)

## 无Dokcer

```sh
#!/bin/bash
/usr/sbin/iptables -F
/usr/sbin/ip6tables -F
/usr/sbin/iptables -A INPUT -i lo -j ACCEPT
/usr/sbin/iptables -A OUTPUT -o lo -j ACCEPT
/usr/sbin/ip6tables -A INPUT -i lo -j ACCEPT
/usr/sbin/ip6tables -A OUTPUT -o lo -j ACCEPT
/usr/sbin/iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
/usr/sbin/iptables -A OUTPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
/usr/sbin/ip6tables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
/usr/sbin/ip6tables -A OUTPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
/usr/sbin/iptables -A INPUT ! -s 47.242.159.58 -p tcp ! --dport 22 -j DROP
/usr/sbin/ip6tables -A INPUT -p tcp ! --dport 22 -j DROP
```

## 有Dokcer（无效）

*   sudo ufw show raw
*   开启ufw防火墙 sudo ufw allow 22 && sudo ufw enable && sudo ufw allow 22
*   sudo ufw status
*   查看容器的私有ip：sudo docker exec -it <dockerID> cat /etc/hosts
*   sudo nano /etc/ufw/after.rules 添加下列配置到最后，注意不要删上一个COMMIT
*   sudo reboot 重启服务器
*   sudo ufw default deny
*   sudo systemctl restart ufw

```ini
# BEGIN UFW AND DOCKER
*filter
:ufw-user-forward - [0:0]
:DOCKER-USER - [0:0]
-A DOCKER-USER -j RETURN -s 10.0.0.0/8
-A DOCKER-USER -j RETURN -s 172.16.0.0/12
-A DOCKER-USER -j RETURN -s 192.168.0.0/16

-A DOCKER-USER -p udp -m udp --sport 53 --dport 1024:65535 -j RETURN

-A DOCKER-USER -j ufw-user-forward

-A DOCKER-USER -j DROP -p tcp -m tcp --tcp-flags FIN,SYN,RST,ACK SYN -d 192.168.0.0/16
-A DOCKER-USER -j DROP -p tcp -m tcp --tcp-flags FIN,SYN,RST,ACK SYN -d 10.0.0.0/8
-A DOCKER-USER -j DROP -p tcp -m tcp --tcp-flags FIN,SYN,RST,ACK SYN -d 172.16.0.0/12
-A DOCKER-USER -j DROP -p udp -m udp --dport 0:32767 -d 192.168.0.0/16
-A DOCKER-USER -j DROP -p udp -m udp --dport 0:32767 -d 10.0.0.0/8
-A DOCKER-USER -j DROP -p udp -m udp --dport 0:32767 -d 172.16.0.0/12

-A DOCKER-USER -j RETURN
COMMIT
# END UFW AND DOCKER
```

*   急救
*   停止实例
*   启动实例
*   创建命令 sudo ufw default allow
*   sudo ufw disable
*   sudo ufw status

## 有Dokcer

```sh
#!/bin/bash
/usr/sbin/iptables -F
/usr/sbin/ip6tables -F
/usr/sbin/iptables -A INPUT -i lo -j ACCEPT
/usr/sbin/iptables -A OUTPUT -o lo -j ACCEPT
/usr/sbin/ip6tables -A INPUT -i lo -j ACCEPT
/usr/sbin/ip6tables -A OUTPUT -o lo -j ACCEPT
/usr/sbin/iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
/usr/sbin/iptables -A OUTPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
/usr/sbin/ip6tables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
/usr/sbin/ip6tables -A OUTPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
/usr/sbin/iptables -A INPUT ! -s 47.242.159.58 -p tcp ! --dport 22 -j DROP
/usr/sbin/ip6tables -A INPUT -p tcp ! --dport 22 -j DROP
/usr/sbin/iptables -I INPUT -s 172.16.0.0/12 -j ACCEPT
/usr/sbin/iptables -I OUTPUT -s 172.16.0.0/12 -j ACCEPT
```