---
title: 建站记录（五）WordPress开启HTTPS
tags: []
id: '1476'
categories:
  - - 运维
date: 2022-02-17 19:38:38
---

## 第一步 修改WordPress的地址设置

*   管理后台-设置-常规

![](https://img-cdn.limour.top/blog/20220217192801.png)

## 第二步 通过宝塔修改 wp-config.php

*   在 "<?php" 后添加以下代码
*   效果如下图，用于解决以下两个报错
*   登录后台显示 ["抱歉，您不能访问此页面"](https://www.cainiao.io/archives/1037)
*   访问wp-admin页面时显示 ["Too many redirects"](https://wordpress.stackexchange.com/questions/302965/too-many-redirects-only-when-trying-to-access-wp-admin-page)

```php
if (strpos($_SERVER['HTTP_X_FORWARDED_PROTO'], 'https') !== false){
   $_SERVER['HTTPS']='on';
   define('FORCE_SSL_ADMIN', true);
   define('FORCE_SSL_LOGIN', true);
   define('CONCATENATE_SCRIPTS', false);
}
```

![](https://img-cdn.limour.top/blog/20220217193010.png)

## 第三步 关闭非443端口的公网访问

*   [建站记录（一）甲骨文永久免费服务器基础配置](https://occdn.limour.top/1458.html)
*   第二步 配置开机启动脚本 中添加以下代码
*   /usr/sbin/iptables -A INPUT ! -s 127.0.0.1 -p tcp --dport 5080 -j DROP
*   意思是拒绝所有非localhost来源的对5080端口的tcp访问
*   其他重要的端口依次类推进行添加，添加完成后 reboot 检查是否生效

## 第四步 宝塔计划任务开启wp-cron

*   wget --delete-after http://127.0.0.1:5080/wp-cron.php

![](https://img-cdn.limour.top/blog/20220217193804.png)

## 第五步 解决CDN掩盖访客IP问题

```php
/**获取用户真实IP地址*/
if(isset($_SERVER['HTTP_X_FORWARDED_FOR'])) {
 $list = explode(',',$_SERVER['HTTP_X_FORWARDED_FOR']);
 $_SERVER['REMOTE_ADDR'] = $list[0];
}
```