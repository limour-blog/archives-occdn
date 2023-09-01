---
title: zerotier：安装moon节点
tags: []
id: '1893'
categories:
  - - 运维
date: 2022-06-25 18:16:30
---

## 来源

[https://github.com/rwv/docker-zerotier-moon](https://github.com/rwv/docker-zerotier-moon)

[https://github.com/kaaass/ZerotierFix](https://github.com/kaaass/ZerotierFix)

[https://post.smzdm.com/p/adwrepgk](https://post.smzdm.com/p/adwrepgk/)

[https://github.com/docker/compose/releases/](https://github.com/docker/compose/releases/)

[https://rmbz.net/archives/zerotier-moon-orbit-config](https://rmbz.net/archives/zerotier-moon-orbit-config)

## 第一步 安装docker

*   阿里云 centos
*   sudo yum remove docker docker-client docker-client-latest docker-common docker-latest docker-latest-logrotate docker-logrotate docker-engine
*   sudo yum install -y yum-utils device-mapper-persistent-data lvm2
*   sudo yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
*   sudo yum makecache fast
*   sudo yum install docker-ce docker-ce-cli containerd.io
*   systemctl start docker
*   systemctl enable docker
*   DOCKER\_CONFIG=${DOCKER\_CONFIG:-$HOME/.docker}
*   mkdir -p $DOCKER\_CONFIG/cli-plugins
*   sudo curl -L "https://get.daocloud.io/docker/compose/releases/download/v2.6.1/docker-compose-$(uname -s)-$(uname -m)" -o $DOCKER\_CONFIG/cli-plugins/docker-compose
*   chmod +x $DOCKER\_CONFIG/cli-plugins/docker-compose
*   $DOCKER\_CONFIG/cli-plugins/docker-compose --version
*   ln -s $DOCKER\_CONFIG/cli-plugins/docker-compose /usr/local/bin/
*   docker-compose --version

## 第二步 docker安装moon节点

```yml
version: "3"

services:
  zerotier-moon:
    image: seedgou/zerotier-moon
    container_name: "zerotier-moon"
    restart: always
    ports:
      - "9993:9993/udp"
    volumes:
      - ./config:/var/lib/zerotier-one
    entrypoint:
      - /startup.sh
      - "-4"
      - 1.2.3.4
```

*   **1.2.3.4 替换成moon节点的公网ip**
*   mkdir zerotier\_moon
*   cd zerotier\_moon
*   nano -K docker-compose.yml
*   \# dos2unix docker-compose.yml
*   docker-compose up -d
*   docker-compose logs
*   防火墙开放对应端口的udp

## 第三步 openwrt增加moon节点

*   docker-compose logs 获得 zerotier-cli orbit <world ID> <seed> （通常这两个值是一样的）
*   登录openwrt，执行 vi /etc/init.d/zerotier
*   在 start\_service中的最后加入 zerotier-cli orbit <world ID> <seed> 命令
*   执行 zerotier-cli orbit <world ID> <seed>
*   查看moon节点是否添加 zerotier-cli listpeers

## 第四步 安卓增加moon节点

*   安装 [ZerotierFix](https://github.com/kaaass/ZerotierFix)
*   点击右上角三个点，选择"入轨"， 添加moon节点， 通过入轨导入，moon地址为<world ID>，moon种子为<seed>

## 第五步 测试

安卓关闭wifi接入zerotier网络，电脑ping手机在zerotier网络上的ip，看延迟是否降低。