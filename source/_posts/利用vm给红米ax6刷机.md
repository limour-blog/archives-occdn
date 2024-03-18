---
title: 利用VM给红米AX6刷机
tags: []
id: '1875'
categories:
  - - 运维
date: 2022-06-08 17:19:43
---

## 第一步 VM安装OpenWrt

https://occdn.limour.top/1797.html

## 第二步 下载必须的文件

[https://www.right.com.cn/forum/thread-4560135-1-1.html](https://www.right.com.cn/forum/thread-4560135-1-1.html)

[https://qust.me/post/hong-mi-ax6-jie-suo-ssh-an-zhuang-shi-yong-shellclash-jiao-cheng/](https://qust.me/post/hong-mi-ax6-jie-suo-ssh-an-zhuang-shi-yong-shellclash-jiao-cheng/)

## 第三步 准备 openwrt 服务

```lua
module("luci.controller.admin.xqsystem", package.seeall)


function index()
    local page   = node("api")
    page.target  = firstchild()
    page.title   = ("")
    page.order   = 100
    page.index = true
    page   = node("api","xqsystem")
    page.target  = firstchild()
    page.title   = ("")
    page.order   = 100
    page.index = true
    entry({"api", "xqsystem", "token"}, call("getToken"), (""), 103, 0x08)
end

local LuciHttp = require("luci.http")

function getToken()
    local result = {}
    result["code"] = 0
    result["token"] = "; nvram set ssh_en=1; nvram commit; echo -e 'admin\nadmin'  passwd root; sed -i 's/channel=.*/channel=\"debug\"/g' /etc/init.d/dropbear; /etc/init.d/dropbear start;"
    LuciHttp.write_json(result)
end
```

*   vim /usr/lib/lua/luci/controller/admin/xqsystem.lua
*   写入上面的lua脚本
*   访问 http://192.168.108.129/cgi-bin/luci/api/xqsystem/token 确认一下无报错

## 第四步 准备虚拟网络服务

![](https://img.limour.top/archives_2023/blog/20220608154418.webp)

1、确保openwrt虚拟机网络在VMnet8上

![](https://img.limour.top/archives_2023/blog/20220608155404.webp)

2、配置VMnet8的子网到169.254.31.0 (这里DHCP忘勾上了，也勾上)

![](https://img.limour.top/archives_2023/blog/20220608163144.webp)

3、配置电脑网络适配器的VMnet8，留出169.254.31.1的地址

*   uci set network.lan.proto=dhcp
*   uci commit network
*   /etc/init.d/network restart
*   通过ifconfig查看br-lan的ip地址，或者其他设置静态ip的方法，确保openwrt的ip为169.254.31.1
*   浏览器访问 http://169.254.31.1/cgi-bin/luci/api/xqsystem/token 再次确认无报错

## 第五步 电脑热点共享VMnet8

*   手机开热点，电脑连接，确保有互联网访问
*   开启电脑热点

![](https://img.limour.top/archives_2023/blog/20220608164112.webp)

修改VMnet8的适配器选项，将其通过前面电脑的热点进行共享

*   用其它设备，如pad连接电脑热点
*   用其它设备，访问 http://169.254.31.1/cgi-bin/luci/api/xqsystem/token 再次确认无报错

## 第六步 还是用桥接吧，前面跑不通

*   虚拟网络编辑器还原默认设置
*   VMnet0改成桥接模式，桥接到 电脑热点对应的适配器
*   电脑热点对应的适配器的属性里关闭ipv4和ipv6
*   openwrt的硬件-网络适配器-自定义从VMnet8改成VMnet0
*   重复步骤五的验证步骤

## 第七步 固件降级

*   网线LAN口连接AX6
*   登录后台
*   常用设置-系统状态-手动升级

## 第八步 按以下教程至成功备份mtd9

[https://qust.me/post/redmi\_ax6\_openwrt/](https://qust.me/post/redmi_ax6_openwrt/)

## 第九步 按以下教程固化SSH，注意记下密码

[https://www.right.com.cn/forum/thread-4560135-1-1.html](https://www.right.com.cn/forum/thread-4560135-1-1.html)

## 第十步 按以下教程至第五步完成

[https://www.right.com.cn/forum/thread-4111331-1-1.html](https://www.right.com.cn/forum/thread-4111331-1-1.html)

注意，第四步：拨电源重新启动路由器  
启动起来浏览器192.168.1.1，进入qsdk固件  
此时的账号和密码均为`ZXDSL` 或 均为 `admin`（不好意思，到上游去了，请拔掉WAN口）

## 第十一步 按以下教程完成全部工作

[https://www.right.com.cn/forum/thread-4560135-1-1.html](https://www.right.com.cn/forum/thread-4560135-1-1.html)