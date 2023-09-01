---
title: 建站记录（三）Oracle 宝塔 SSL证书 防源站泄漏
tags: []
id: '1467'
categories:
  - - 运维
date: 2022-02-17 19:02:18
---

## 第一步 托管域名到CloudFlare，并生成证书

*   方案一：直接使用[CF](https://dash.cloudflare.com/)的【SSL/TLS】的【源服务器】生成的证书，此证书不能用于三级及以上的域名
*   方案二：[建站记录（三）番外：acme 自动更新证书](https://occdn.limour.top/1466.html)

## 第二步 Nginx安全设置防源站泄漏

*   CF开启强制HTTPS
*   安全检测：[CDN寻找网站真实IP的方法汇总](https://zhuanlan.zhihu.com/p/33440472)
*   **第一步 申请假证书**

```txt
-----BEGIN CERTIFICATE-----
MIIDITCCAsagAwIBAgIUTcEWLzynkLCFCoAC1iDH2vG3EkYwCgYIKoZIzj0EAwIw
gY8xCzAJBgNVBAYTAlVTMRMwEQYDVQQIEwpDYWxpZm9ybmlhMRYwFAYDVQQHEw1T
YW4gRnJhbmNpc2NvMRkwFwYDVQQKExBDbG91ZEZsYXJlLCBJbmMuMTgwNgYDVQQL
Ey9DbG91ZEZsYXJlIE9yaWdpbiBTU0wgRUNDIENlcnRpZmljYXRlIEF1dGhvcml0
eTAeFw0xOTAxMTMxNDMxMDBaFw0zNDAxMDkxNDMxMDBaMGIxGTAXBgNVBAoTEENs
b3VkRmxhcmUsIEluYy4xHTAbBgNVBAsTFENsb3VkRmxhcmUgT3JpZ2luIENBMSYw
JAYDVQQDEx1DbG91ZEZsYXJlIE9yaWdpbiBDZXJ0aWZpY2F0ZTBZMBMGByqGSM49
AgEGCCqGSM49AwEHA0IABAg/hZ9lDHj/f+0jDRAN23TkNEqIi46mCGnwZVD3glxL
l+a1mpfXLHSEFTipnSyQgmvkPYzQGaEIFD0q6W/ZgMujggEqMIIBJjAOBgNVHQ8B
Af8EBAMCBaAwHQYDVR0lBBYwFAYIKwYBBQUHAwIGCCsGAQUFBwMBMAwGA1UdEwEB
/wQCMAAwHQYDVR0OBBYEFCEZF6Eyem01XPbgwr6DXLZV1qsQMB8GA1UdIwQYMBaA
FIUwXTsqcNTt1ZJnB/3rObQaDjinMEQGCCsGAQUFBwEBBDgwNjA0BggrBgEFBQcw
AYYoaHR0cDovL29jc3AuY2xvdWRmbGFyZS5jb20vb3JpZ2luX2VjY19jYTAjBgNV
HREEHDAaggwqLmRuc3BvZC5jb22CCmRuc3BvZC5jb20wPAYDVR0fBDUwMzAxoC+g
LYYraHR0cDovL2NybC5jbG91ZGZsYXJlLmNvbS9vcmlnaW5fZWNjX2NhLmNybDAK
BggqhkjOPQQDAgNJADBGAiEAnrequCk/QZOOrcPH6C3Hgcy4SPNUy5rQtku/aYkj
qQoCIQCN6IyYNiXuwG+8jUgJrveiirBjiz2jXZSTEfVAyibjTg==
-----END CERTIFICATE-----

-----BEGIN PRIVATE KEY-----
MIGHAgEAMBMGByqGSM49AgEGCCqGSM49AwEHBG0wawIBAQQgK0HE3hTJQDg6p/fj
nS92eSuRKZEZ5F4grT6tWFKNYVmhRANCAAQIP4WfZQx4/3/tIw0QDdt05DRKiIuO
pghp8GVQ94JcS5fmtZqX1yx0hBU4qZ0skIJr5D2M0BmhCBQ9Kulv2YDL
-----END PRIVATE KEY-----
```

![](https://img-cdn.limour.top/blog/image.png)

**第二步 部署假页面并使用假证书**

![](https://img-cdn.limour.top/blog/image-1.png)

**第三步 修改默认站点**

![](https://img-cdn.limour.top/blog/image-2.png)

**第四步 修改默认站点的返回代码为444**