---
title: Nginx Proxy Manager进阶：反代grpc
tags: []
id: '2549'
categories:
  - - uncategorized
date: 2023-01-31 18:24:19
---

```nginx
location /path {
  if ($content_type !~ "application/grpc") {
    return 404;
  }
  client_body_buffer_size 1m;
  client_body_timeout 1h;
  client_max_body_size 0;
  grpc_read_timeout 1h;
  grpc_send_timeout 1h;
  grpc_pass grpc://nui:43216;
}
```

![](https://img.limour.top/archives_2023/2023/01/31/63d8ebf212d75.webp)