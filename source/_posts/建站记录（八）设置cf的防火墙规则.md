---
title: 建站记录（八）设置CF的防火墙规则
tags: []
id: '1548'
categories:
  - - 运维
date: 2022-02-23 23:53:44
---

![](https://img-cdn.limour.top/blog/20220223235010.png)

![](https://img-cdn.limour.top/blog/20220223235135.png)

## 更好的方式

*   将合法机器人爬虫移入第一个允许中
*   质询规则如下

```sql
(ip.geoip.country ne "CN") or (cf.threat_score gt 2) or (not http.user_agent contains "Mozilla") or (not http.request.version in {"HTTP/2" "HTTP/3" "SPDY/3.1"}) or (ip.geoip.asnum in {37963 45090 55990})
```