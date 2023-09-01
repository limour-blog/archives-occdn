---
title: 【转载】WordPress迁移媒体到阿里云OSS
tags: []
id: '2028'
categories:
  - - 转载
date: 2022-07-10 04:54:01
---

## 来源

浪子虎：http://langzihu.com/210.html 

[https://github.com/yiichou/aliyun-oss-support](https://github.com/yiichou/aliyun-oss-support)

[https://help.aliyun.com/document\_detail/153893.html](https://help.aliyun.com/document_detail/153893.html)

## 第一步 安装插件

*   从gihub上下载aliyun-oss-support
*   上传插件完成安装

## 第二步 启用插件

*   去RAM控制台设置子账号及其权限，创建AccessKey：https://ram.console.aliyun.com/users
*   以上步骤并不能将已有的图片传到OSS

## 第三步 迁移现有的媒体

*   mkdir oss\_tmp && cd oss\_tmp
*   **echo** $bucket\_name:$access\_key\_id:$access\_key\_secret > ./passwd-ossfs
*   chmod 600 ./passwd-ossfs
*   mkdir oss
*   ossfs $bucket\_name ./oss -ourl=http://oss-cn-shanghai-internal.aliyuncs.com -o allow\_other -opasswd\_file=./passwd-ossfs
*   cd oss
*   mkdir blog\_wp && cd blog\_wp
*   cp /var/www/html/wp-content/uploads/20\* .

## 第四步 重写链接

![](https://img-cdn.limour.top/blog/20220710045326.png)