---
title: 树莓派：Docker安装宝塔面板（64位系统）
tags: []
id: '2020'
categories:
  - - 树莓派
date: 2022-07-09 01:37:58
---

## 来源

[https://hub.docker.com/r/cyberbolt/baota](https://hub.docker.com/r/cyberbolt/baota)

## 部署

```yml
version: '3.3'
services:
    baota:
        volumes:
            - './www:/www'
        network_mode: host
        restart: always
        container_name: baota
        image: cyberbolt/baota
        command: -port 18888 -username Limour -password abc123456
```

*   mkdir bt\_panel && cd bt\_panel
*   sudo docker run -itd --net=host --name baota-test cyberbolt/baota -port 26756 -username cyberbolt -password abc123456
*   sudo docker cp baota-test:/www ./www
*   sudo docker stop baota-test && sudo docker rm baota-test
*   nano docker-compose.yml
*   sudo docker-compose up -d
*   sudo docker-compose logs

## Debian换源

*   宝塔面板文件里编辑 /etc/apt/sources.list
*   记录下是 buster 还是 stretch
*   注释掉全部，然后添加以下镜像
*   终端执行 sudo docker exec -it baota apt-get update

```txt
deb http://mirrors.aliyun.com/debian/ buster main non-free contrib
deb-src http://mirrors.aliyun.com/debian/ buster main non-free contrib
deb http://mirrors.aliyun.com/debian-security buster/updates main
deb-src http://mirrors.aliyun.com/debian-security buster/updates main
deb http://mirrors.aliyun.com/debian/ buster-updates main non-free contrib
deb-src http://mirrors.aliyun.com/debian/ buster-updates main non-free contrib
deb http://mirrors.aliyun.com/debian/ buster-backports main non-free contrib
deb-src http://mirrors.aliyun.com/debian/ buster-backports main non-free contrib
```