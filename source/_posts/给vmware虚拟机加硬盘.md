---
title: 给VMware虚拟机加硬盘
tags: []
id: '2375'
categories:
  - - 运维
date: 2022-10-02 14:27:33
---

买了一台[零刻 SER5 Pro 32G](https://zhongce.sina.com.cn/article/view/146429/)作为虚拟机的宿主机，用了一个月[感觉还行](https://tz.limour.eu.org/)，准备加装一个[铠侠TC10 1T SATA3固态](https://item.jd.com/100007080971.html)。小心排线接口上有个黑色的东东，先用镊子挑起来再拔。然后虚拟机设置里添加磁盘。

![](https://img.limour.top/archives_2023/2022/10/02/633912609a4e0.webp)

核心参数

![](https://img.limour.top/archives_2023/2022/10/02/633912aeb6600.jpg)

机器外观

![](https://img.limour.top/archives_2023/2022/10/02/63391364f3844.jpg)

固态外观

## 挂载磁盘并设置开机自动mount

*   sudo su
*   fdisk -l #i查看可挂载磁盘
*   mkfs.ext4 /dev/sdb #格式化为ext4
*   \# apt install util-linux
*   blkid #获取磁盘的uuid和属性
*   mkdir /home/limour/upload #新建一个目录
*   chown limour:limour /home/limour/upload
*   nano /etc/fstab
*   UUID=8efc6ca9-229c-4aaf-b39c-77b55bf0d615 /home/limour/upload ext4 defaults 0 2
*   reboot 重启查看是否成功
*   sudo chown limour:limour /home/limour/upload