---
title: VM安装OpenWrt做旁路由
tags: []
id: '1797'
categories:
  - - 运维
date: 2022-05-07 18:47:34
---

## 第一步 下载镜像

[https://www.right.com.cn/forum/thread-4053752-1-1.html](https://www.right.com.cn/forum/thread-4053752-1-1.html)

## 第二步 解压得到img文件

## 第三步 建立虚拟机

## 第四步 使用kali虚拟机挂载第三步的空白磁盘

修改启动顺序：[https://jingyan.baidu.com/article/fa4125ac0da02c28ac709280.html](https://jingyan.baidu.com/article/fa4125ac0da02c28ac709280.html)

## 第五步 共享文件到kali

python.exe -m httpserver

## 第六步 dd到磁盘

dd if=20220401-Ipv6-Plus-5.4-x86-64-generic-squashfs-combined-efi.img of=/dev/sdb

## 第七步 启动OpenWRT

[https://zhuanlan.zhihu.com/p/334419721](https://zhuanlan.zhihu.com/p/334419721)