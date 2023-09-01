---
title: 树莓派：smb挂载光猫上的U盘
tags: []
id: '1905'
categories:
  - - 树莓派
date: 2022-06-30 00:58:07
---

## 测试

*   sudo apt install smbclient
*   sudo smbclient -L 192.168.1.1 -U 用户名%密码，里面会显示Sharename，第一个可能是<samba>
*   **忽略Failed to connect with SMB1 -- no workgroup available错误，这个坑死我了，一直以为错了，其实已经告诉我Sharename里有<samba>**
*   sudo smbclient //192.168.1.1/<samba> -U 用户名%密码
*   其中的<samba>可能的值：samba、share、public、home、usbshare等等，为Sharename显示的结果

![](https://img-cdn.limour.top/blog/20220630004511.png)

√的是需要的，×的地方忽略它

## 挂载

*   sudo apt install cifs-utils
*   sudo mkdir /home/share/
*   sudo chmod 777 /home/share/
*   sudo nano /etc/rc.local 添加以下启动命令
*   sudo /bin/mount -t cifs -o username=用户名,password=密码,file\_mode=0777,dir\_mode=0777,vers=1.0 //192.168.1.1/<samba> /home/share/
*   sudo reboot 查看效果