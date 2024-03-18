---
title: 建站记录（七）设置CF规则
tags: []
id: '1510'
categories:
  - - 运维
date: 2022-02-22 17:16:23
---

## 第一步 禁用WP顶部管理员工作条

*   外观-主题文件编辑器
*   在functions.php中增加一行代码
*   show\_admin\_bar(false);

*   添加自定义css
*   隐藏某些干扰访客的登录后显示
*   .logged-in-as{display: none}
*   .header-user-avatar{display: none}

## 第二步 修改WP登录页面地址

*   修改wordpress程序网站根目录下wp-login.php的文件名，修改为wp-admin-login.php
*   该文件wp-admin-login.php中出现的字符wp-login.php全部改为wp-admin-login.php
*   找到根目录下的wp-includes/general-template.php文件
*   该文件内的字符wp-login.php均替换为wp-admin-login.php
*   **目的是节省接下来的CF规则数量**

## 第三步 设置CF规则

![](https://img.limour.top/archives_2023/blog/20220222180442.webp)

[https://cloud.tencent.com/developer/article/1774198](https://cloud.tencent.com/developer/article/1774198)

## 第四步 禁用WP-CRON

*   删除 建站记录（五）WordPress开启HTTPS 中的第四步
*   在 `wp-config.php` 添加下面的代码禁用 WP-Cron：
*   define('DISABLE\_WP\_CRON', true);
*   使用宝塔面板创建计划任务
*   _\*/15 \*_ \* \* \* php /home/public\_html/wp-cron.php