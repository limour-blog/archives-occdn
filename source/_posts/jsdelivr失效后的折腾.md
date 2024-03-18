---
title: Jsdelivr失效后的折腾
tags: []
id: '1853'
categories:
  - - 运维
date: 2022-05-26 00:36:39
---

## php相关

*   安装php8.0
*   安装fileinfo、opcache、redis、imagemagick、exif拓展
*   设置display\_errors为off

![](https://img.limour.top/archives_2023/blog/20220526001647.webp)

## Nginx相关

*   网站允许重写，设置替换规则
*   反向代理JSD

![](https://img.limour.top/archives_2023/blog/202205260049.jpg)

```nginx
    sub_filter_once off;
    sub_filter_types *;
    sub_filter "cdn.js删掉我delivr.net" "jscdn.limour.top";
    location / {
      if (!-e $request_filename) {
      rewrite (.*) /index.php;
      }
    }
```

![](https://img.limour.top/archives_2023/blog/20220526002139.webp)

```nginx
#PROXY-START/

location ^~ /
{
    expires 30d;
    valid_referers limour.top *.limour.top;
    if ($invalid_referer){
        return 403;
    }
    gzip_proxied any;
    proxy_pass https://cdn.js删掉我delivr.net;
    proxy_set_header Host cdn.js删掉我delivr.net;
    proxy_ssl_server_name on;
    proxy_ssl_name cdn.js删掉我delivr.net;
    resolver 8.8.8.8;
    sub_filter_once off;
    sub_filter_types *;
    sub_filter "cdn.js删掉我delivr.net" "jscdn.limour.top";
    
}

```

## WordPress相关

*   给网站套CDN打补丁

![](https://img.limour.top/archives_2023/blog/20220526002358.webp)

```php
// 判断是否访问请求经过代理
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

## Sakura主题相关

*   简单使用代码块，自定义html
*   替换JSD

```python
import pyperclip
import html
code = pyperclip.paste()
print(code)
language = input('\n\n语言：')
prefix = f'<pre><code class="language-{language}" lang="{language}">'
suffix = f'</code></pre>'
pyperclip.copy(prefix + html.escape(code) + suffix)
```

```shell
sed -i "s/jscdn.limour.top/jsdelivr.2heng.xin/g" `grep -rl 'jscdn.limour.top' Sakura`
```

## iptables相关

```bash
#!/bin/bash
/usr/sbin/iptables -P INPUT ACCEPT
/usr/sbin/iptables -P FORWARD ACCEPT
/usr/sbin/iptables -P OUTPUT ACCEPT
/usr/sbin/iptables -F
/usr/sbin/iptables -A INPUT ! -s 127.0.0.1 -p tcp --dport 54321 -j DROP
/usr/sbin/iptables -A INPUT ! -s 127.0.0.1 -p tcp --dport 8888 -j DROP
/usr/sbin/iptables -A INPUT ! -s 127.0.0.1 -p tcp --dport 3306 -j DROP
/usr/sbin/iptables -A INPUT ! -s 127.0.0.1 -p tcp --dport 5080 -j DROP
/usr/sbin/iptables -A INPUT ! -s 127.0.0.1 -p tcp --dport 6379 -j DROP
/usr/sbin/iptables -A INPUT ! -s 127.0.0.1 -p tcp --dport 888 -j DROP
/usr/sbin/ip6tables -A INPUT -p tcp -m multiport --dports 54321,8888,5080,3306,6379,888 -j DROP
/usr/sbin/iptables -I INPUT -p tcp -m multiport --dports 80,443 -s 173.245.48.0/20 -j ACCEPT
/usr/sbin/iptables -I INPUT -p tcp -m multiport --dports 80,443 -s 103.21.244.0/22 -j ACCEPT
/usr/sbin/iptables -I INPUT -p tcp -m multiport --dports 80,443 -s 103.22.200.0/22 -j ACCEPT
/usr/sbin/iptables -I INPUT -p tcp -m multiport --dports 80,443 -s 103.31.4.0/22 -j ACCEPT
/usr/sbin/iptables -I INPUT -p tcp -m multiport --dports 80,443 -s 141.101.64.0/18 -j ACCEPT
/usr/sbin/iptables -I INPUT -p tcp -m multiport --dports 80,443 -s 108.162.192.0/18 -j ACCEPT
/usr/sbin/iptables -I INPUT -p tcp -m multiport --dports 80,443 -s 190.93.240.0/20 -j ACCEPT
/usr/sbin/iptables -I INPUT -p tcp -m multiport --dports 80,443 -s 188.114.96.0/20 -j ACCEPT
/usr/sbin/iptables -I INPUT -p tcp -m multiport --dports 80,443 -s 197.234.240.0/22 -j ACCEPT
/usr/sbin/iptables -I INPUT -p tcp -m multiport --dports 80,443 -s 198.41.128.0/17 -j ACCEPT
/usr/sbin/iptables -I INPUT -p tcp -m multiport --dports 80,443 -s 162.158.0.0/15 -j ACCEPT
/usr/sbin/iptables -I INPUT -p tcp -m multiport --dports 80,443 -s 104.16.0.0/13 -j ACCEPT
/usr/sbin/iptables -I INPUT -p tcp -m multiport --dports 80,443 -s 104.24.0.0/14 -j ACCEPT
/usr/sbin/iptables -I INPUT -p tcp -m multiport --dports 80,443 -s 172.64.0.0/13 -j ACCEPT
/usr/sbin/iptables -I INPUT -p tcp -m multiport --dports 80,443 -s 131.0.72.0/22 -j ACCEPT
/usr/sbin/ip6tables -I INPUT -p tcp -m multiport --dports 80,443 -s 2400:cb00::/32 -j ACCEPT
/usr/sbin/ip6tables -I INPUT -p tcp -m multiport --dports 80,443 -s 2606:4700::/32 -j ACCEPT
/usr/sbin/ip6tables -I INPUT -p tcp -m multiport --dports 80,443 -s 2803:f800::/32 -j ACCEPT
/usr/sbin/ip6tables -I INPUT -p tcp -m multiport --dports 80,443 -s 2405:b500::/32 -j ACCEPT
/usr/sbin/ip6tables -I INPUT -p tcp -m multiport --dports 80,443 -s 2405:8100::/32 -j ACCEPT
/usr/sbin/ip6tables -I INPUT -p tcp -m multiport --dports 80,443 -s 2a06:98c0::/29 -j ACCEPT
/usr/sbin/ip6tables -I INPUT -p tcp -m multiport --dports 80,443 -s 2c0f:f248::/32 -j ACCEPT
/usr/sbin/iptables -A INPUT -p tcp -m multiport --dports 80,443 -j DROP
/usr/sbin/ip6tables -A INPUT -p tcp -m multiport --dports 80,443 -j DROP
```