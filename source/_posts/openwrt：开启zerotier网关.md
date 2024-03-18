---
title: openwrt：开启zerotier网关
tags: []
id: '1888'
categories:
  - - 运维
date: 2022-06-25 15:45:36
---

## 来源

[https://zhuanlan.zhihu.com/p/433880308](https://zhuanlan.zhihu.com/p/433880308)

## 第一步 openwrt接入zerotier网络

![](https://img.limour.top/archives_2023/blog/20220625153622.webp)

**注意开启准入控制里的两项，否则其他设备无法访问内网，只能访问路由**

## 第二步 zerotier网络配置网关

![](https://img.limour.top/archives_2023/blog/20220625153939.webp)

同时记住网关的zerotier内网ip，这里是192.168.191.198

## 第三步 zerotier网络配置路由表

![](https://img.limour.top/archives_2023/blog/20220625154244.webp)

**Destination**为openwrt内网的ip段，**via**为第二步记住的openwrt网关在zerotier内网的ip

## 第四步 测试

手机接入zerotier网络，关闭wifi，开启热点，访问内网的地址进行测试