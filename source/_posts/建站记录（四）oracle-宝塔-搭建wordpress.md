---
title: 建站记录（四）Oracle 宝塔 搭建WordPress
tags: []
id: '1473'
categories:
  - - 运维
date: 2022-02-17 19:25:08
---

## 第一步 宝塔一键部署

*   宝塔面板>软件商店>一键部署>一键部署WordPress
*   端口号设置为非80和443端口，如5080

## 第二步 新建一个纯静态站点

![](https://img.limour.top/archives_2023/blog/20220217191358.webp)

**域名为博客域名，如 occdn.limour.top**

## 第三步 给纯静态站点设置SSL证书

*   [建站记录（三）Oracle 宝塔 SSL证书 防源站泄漏](https://occdn.limour.top/1464.html)
*   证书为第一步获得的证书
*   同时点上右上角的强制HTTPS

## 第四步 在纯静态站点添加反向代理

*   目标URL填 第一步 中部署的WordPress站点，如：http://127.0.0.1:5080
*   在反向代理的配置文件的 "#PROXY-START/" 前添加需要分流的服务，如grpc服务
*   以同样的方法反代宝塔面板本身

```nginx
location /yourservicepathname {
  if ($content_type !~ "application/grpc") {
    return 404;
  }
  client_max_body_size 0;
  client_body_timeout 1071906480m;
  grpc_read_timeout 1071906480m;
  grpc_pass grpc://127.0.0.1:2003;
}
```