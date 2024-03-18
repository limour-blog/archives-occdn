---
title: 阿里云OSS + cloudflare 实现自建文件服务器
tags: []
id: '1486'
categories:
  - - 运维
date: 2022-02-18 12:25:45
---

## 第一步 新建OSS Bucket

*   https://oss.console.aliyun.com/overview
*   创建和阿里云服务器同区域的Bucket

## 第二步 cloudflare开启CDN

*   新建A记录 指向阿里云服务器的IP
*   确保开启小云朵

## 第三步 使用阿里云服务器反代OSS资源，避免外网流量

*   反代域名选用"ECS 的经典网络访问"或"ECS 的 VPC 网络访问"的"Bucket 域名"
*   以下是apache反代的示例
*   systemctl reload httpd

```apache
<VirtualHost *:443>
ServerName file-cdn.limour.top
ProxyPreserveHost Off
ProxyRequests Off
ProxyPass / http://limour-file.oss-cn-shanghai-internal.aliyuncs.com/
ProxyPassReverse / http://limour-file.oss-cn-shanghai-internal.aliyuncs.com/
</VirtualHost>
```

## 第四步 开放指定ip的匿名读取权限

*   Bucket-权限管理-**Bucket 授权策略** 指定ip为自己的阿里云服务器
*   **注意：该ip须为内网ip，不能是公网ip，一般为 172 开头**

![](https://img.limour.top/archives_2023/20220217004941.webp)