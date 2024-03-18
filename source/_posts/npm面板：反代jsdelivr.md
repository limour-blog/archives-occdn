---
title: NPM面板：反代Jsdelivr
tags: []
id: '2013'
categories:
  - - 运维
date: 2022-07-08 13:34:09
---

## 来源

[https://blog.csdn.net/king\_way/article/details/94831420](https://blog.csdn.net/king_way/article/details/94831420)

[https://blog.csdn.net/weixin\_34067980/article/details/91465895](https://blog.csdn.net/weixin_34067980/article/details/91465895)

[https://blog.csdn.net/aotemana/article/details/119315392](https://blog.csdn.net/aotemana/article/details/119315392)

https://www.xiaoc.cn/article/2022\_03\_11/437.html

## 第一步 建立代理

![](https://img.limour.top/archives_2023/blog/20220708105620.webp)

关闭缓存，不强制SSL

## 第二步 建立正常的反向代理

![](https://img.limour.top/archives_2023/blog/20220708132859.webp)

注意NGINX的‘/’

```nginx
expires 30d;
valid_referers limour.top *.limour.top *.j11.fun j11.fun;
if ($invalid_referer){
    return 403;
}
gzip off;
gzip_http_version 1.0;
proxy_set_header Host jscdn.limour.top;
proxy_ssl_server_name on;
proxy_ssl_name jscdn.limour.top;
resolver 8.8.8.8;
```

## 第三步 建立替换的反向代理

![](https://img.limour.top/archives_2023/blog/20220708133144.webp)

```nginx
expires 30d;
valid_referers limour.top *.limour.top *.j11.fun j11.fun;
if ($invalid_referer){
    return 403;
}
proxy_set_header Accept-Encoding '';
gzip_http_version 1.0;
sub_filter_once off;
sub_filter_types *;
sub_filter "jscdn.limour.top" "jscdn.limour.top";
```

## 第四步 建立压缩的反向代理

![](https://img.limour.top/archives_2023/blog/20220708133315.webp)

```nginx
expires 30d;
gzip on;
gzip_min_length  1k;
gzip_buffers     4 16k;
gzip_http_version 1.0;
gzip_comp_level 2;
gzip_types *;
gzip_vary off;
gzip_proxied any;
```