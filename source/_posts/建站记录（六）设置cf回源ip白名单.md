---
title: 建站记录（六）设置CF回源IP白名单
tags: []
id: '1498'
categories:
  - - 运维
date: 2022-02-20 09:42:03
---

[https://support.cloudflare.com/hc/en-us/articles/201897700-Allowing-Cloudflare-IP-addresses](https://support.cloudflare.com/hc/en-us/articles/201897700-Allowing-Cloudflare-IP-addresses)

[https://www.cloudflare.com/ips/](https://www.cloudflare.com/ips/)

将以下命令添加到 建站记录（一）[甲骨文永久免费服务器基础配置](https://occdn.limour.top/1458.html) 第二步的开机脚本后，并运行一次脚本

```shell
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
/usr/sbin/ip6tables -I INPUT -p tcp -m multiport --dports 80,443 -s 2400:cb00::/32 -j ACCEPT
/usr/sbin/ip6tables -I INPUT -p tcp -m multiport --dports 80,443 -s 2606:4700::/32 -j ACCEPT
/usr/sbin/ip6tables -I INPUT -p tcp -m multiport --dports 80,443 -s 2803:f800::/32 -j ACCEPT
/usr/sbin/ip6tables -I INPUT -p tcp -m multiport --dports 80,443 -s 2405:b500::/32 -j ACCEPT
/usr/sbin/ip6tables -I INPUT -p tcp -m multiport --dports 80,443 -s 2405:8100::/32 -j ACCEPT
/usr/sbin/ip6tables -I INPUT -p tcp -m multiport --dports 80,443 -s 2a06:98c0::/29 -j ACCEPT
/usr/sbin/ip6tables -I INPUT -p tcp -m multiport --dports 80,443 -s 2c0f:f248::/32 -j ACCEPT
/usr/sbin/iptables -A INPUT -p tcp -m multiport --dports 80,443 -j DROP
/usr/sbin/ip6tables -A INPUT -p tcp -m multiport --dports 80,443 -j DROP
```