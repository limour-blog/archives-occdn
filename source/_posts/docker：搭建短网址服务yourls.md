---
title: Docker：搭建短网址服务YOURLS
tags: []
id: '1901'
categories:
  - - 转载
date: 2022-06-29 06:24:06
---

## 来源

[https://blog.laoda.de/archives/docker-compose-install-yourls](https://blog.laoda.de/archives/docker-compose-install-yourls)

https://occdn.limour.top/1898.html

## 第一步 增加swap空间

*   wget -O box.sh https://raw.githubusercontent.com/BlueSkyXN/SKY-BOX/main/box.sh && chmod +x box.sh && clear && sudo ./box.sh
*   大小输入4096，设置4G大小的swap空间

## 第二步 部署YOURLS容器

*   mkdir yourls
*   cd yourls
*   nano docker-compose.yml
*   sudo docker-compose up -d

```yml
version: "3.5"
services:

  mysql:
    image: mysql:5.7.22
    environment:
      - MYSQL_ROOT_PASSWORD=my-secret-pw
      - MYSQL_DATABASE=yourls
      - MYSQL_USER=yourls
      - MYSQL_PASSWORD=yourls
    volumes:
      - ./mysql/db/:/var/lib/mysql
      - ./mysql/conf/:/etc/mysql/conf.d
    restart: always
    container_name: mysql
  
  yourls:
    image: yourls
    restart: always
    ports:
      - "8200:80"
    environment:
      YOURLS_DB_HOST: mysql
      YOURLS_DB_USER: yourls
      YOURLS_DB_PASS: yourls
      YOURLS_DB_NAME: yourls
      YOURLS_USER: admin      # 自己起一个名字
      YOURLS_PASS: admin      # 自己换一个登陆密码
      YOURLS_SITE: https://j11.fun  # 换成你自己的域名
      YOURLS_HOURS_OFFSET: 8
    volumes:
      - ./yourls_data/:/var/www/html   
    container_name: yourls_service
    links:
      - mysql:mysql
```

## 第三步 NPM面板反代YOURLS

*   反代的ip用 sudo ip addr show docker0
*   访问 https://j11.fun/admin 完成安装

## 第四步 更改语言包

*   sudo apt install zip -y
*   wget https://github.com/ZvonimirSun/YOURLS-zh\_CN/archive/refs/tags/v1.7.3.zip
*   unzip v1.7.3.zip
*   sudo mv YOURLS-zh\_CN-1.7.3/\* ./yourls\_data/user/languages/
*   cd ./yourls\_data/user/languages/
*   sudo chown -R www-data:www-data zh\_CN.mo
*   sudo chown -R www-data:www-data zh\_CN.po
*   sudo nano ../config.php
*   define( 'YOURLS\_LANG', getenv('YOURLS\_LANG') ?: 'zh\_CN' );

## 示例

[https://j11.fun/blog+](https://j11.fun/blog+)