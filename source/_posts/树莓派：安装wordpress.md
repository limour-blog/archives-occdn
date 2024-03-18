---
title: 树莓派：安装wordpress
tags: []
id: '2024'
categories:
  - - 树莓派
date: 2022-07-09 13:07:12
---

用[Docker安装好了宝塔面板](https://occdn.limour.top/2020.html)，[外网机器反代了Jsdeliver](https://occdn.limour.top/2013.html)，接下来可以安装wordpress了。顺便通过gitee中转镜像了[一个主题](https://j11.fun/sakurairo)，一个[sqlite插件](https://j11.fun/wpsqlitedb)。

## 准备工作

*   宝塔面板软件商店-运行环境里，安装php8.0、Nginx，并开启首页显示
*   首页php8.0安装fileinfo、opcache、imagemagick、exif拓展
*   首页php8.0配置修改，是否输出详细错误信息，改成关闭

## 新建站点

*   新建站点，域名写127.0.0.1:15080，php版本选php80
*   站点设置-配置文件里添加以下内容

![](https://img.limour.top/archives_2023/blog/202205260049.jpg)

```nginx
    sub_filter_once off;
    sub_filter_types *;
    sub_filter "<jsdelivr的地址>" "jscdn.limour.top";
    location / {
      if (!-e $request_filename) {
      rewrite (.*) /index.php;
      }
    }
```

## 安装wordpress

*   宝塔面板点击网页根目录，进入对应文件管理页
*   远程下载以下文件 https://cn.wordpress.org/latest-zh\_CN.tar.gz
*   使用宝塔解压操作解压到当前目录
*   回到站点设置-网站目录，选择运行目录为/wordpress，保存

## 安装**SQLite Integration**插件

*   进入 wp-content 目录
*   远程下载以下文件 https://gogs.frp.limour.top/limour/wp-sqlite-db/raw/master/src/db.php
*   重命名 wp-config-sample.php 为 wp-config.php
*   访问 https://api.wordpress.org/secret-key/1.1/salt/ 修改对应的salt
*   按说明修改数据库位置
*   访问网站完成安装

```php
define('DB_TYPE', 'sqlite');
define('DB_DIR', '/www/wwwroot/127.0.0.1/');
define('DB_FILE', '.ht.sqlite');
if(isset($_SERVER['HTTP_X_FORWARDED_FOR'])) {
    $list = explode(',',$_SERVER['HTTP_X_FORWARDED_FOR']);
    $_SERVER['REMOTE_ADDR'] = $list[0];
    $_SERVER['HTTPS']='on';   
    $_SERVER["SERVER_PORT"] = 443;
    define('WP_HOME', 'https://'.$_SERVER['HTTP_HOST']);
    define('WP_SITEURL', 'https://'.$_SERVER['HTTP_HOST']);
    define('WP_CONTENT_URL', 'https://'.$_SERVER['HTTP_HOST'].'/wp-content');
    define('FORCE_SSL_LOGIN', true);
    define('FORCE_SSL_ADMIN', true);
}
```

## 安装实用插件

*   Autoptimize
*   Easy WP SMTP
*   Simple Local Avatars
*   WP-Optimize
*   UpdraftPlus
*   XML 站点地图

## 安装Sakurairo主题

*   [https://iro.tw/](https://iro.tw/)
*   按说明完成Sakurairo安装

## 演示

[https://wp.j11.fun/](https://wp.j11.fun/)