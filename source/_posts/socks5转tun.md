---
title: SOCKS5转TUN
tags: []
id: '2695'
categories:
  - - 运维
date: 2023-04-20 23:54:57
---

tun是操作系统上的一种虚拟网络设备，可以让用户处理网络中的三层数据包(例如IP包)。与此差不多的还有tap，但tap处理的是二层数据包。只处理三层数据包这就决定了`tun`性能往往比`tap`要好一些。

## 配置TUN

```yml
tun:
  enable: true
  stack: gvisor
  device: utun0
  dns-hijack:
    - 'any:53'
  auto-route: true
  auto-detect-interface: true
    
dns:
  enable: true
  listen: 0.0.0.0:1053
  ipv6: true
  enhanced-mode: redir-host
  default-nameserver:
  - 114.114.114.114
  - 1.1.1.1
  - 8.8.8.8
  nameserver:
  - 'tls://8.8.8.8#socks'
  - 'tls://1.1.1.1#socks'
    
proxies:
  - name: "socks"
    type: socks5
    server: 127.0.0.1
    port: 1580
    udp: true
    
rules:
  - DOMAIN-SUFFIX,limour.top,DIRECT
  - DOMAIN-SUFFIX,j11.fun,DIRECT
  - IP-CIDR,127.0.0.0/8,DIRECT,no-resolve
  - IP-CIDR,你.的.I.P/32,DIRECT,no-resolve
  - MATCH,socks
```

*   下载[TUN](https://ghproxy.com/https://github.com/MetaCubeX/Clash.Meta/releases/download/v1.14.3/clash.meta-windows-amd64-cgo-v1.14.3.zip)（有问题可以查看对应的[WIKI](https://wiki.metacubex.one/)和[项目地址](https://github.com/MetaCubeX/Clash.Meta)）
*   在可执行文件的同一目录下新建config.yaml，内容如上，根据自己的情况修改对应项
*   以管理员身份在目录下运行.`\clash.meta-windows-amd64-cgo.exe -d .`
*   TUN启动后，再启动SOCKS5
*   访问[测试网址](https://ipleak.net)，确定配置无误

## DNS配置选项

*   dns-hijack: DNS劫持，拦截规则对应的DNS请求
*   default-nameserver: **仅**用于解析 nameserver 的DNS服务器
*   nameserver: 真正的DNS服务器，`#socks`为特殊写法，表示DNS请求将经`socks`隧道传递