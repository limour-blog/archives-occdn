---
title: CF：自定义Hotlink Protection
tags: []
id: '1903'
categories:
  - - 运维
date: 2022-06-29 17:56:25
---

由于图床和博客可能使用不同的顶级域名，因此CF自带的Hotlink Protection不能用了，需要在Scrape Shield里关闭它，然后利用Workers来实现可以自定义的Hotlink Protection

## 来源

[https://developers.cloudflare.com/workers/examples/hot-link-protection/](https://developers.cloudflare.com/workers/examples/hot-link-protection/)

## 模板修改

```js
const HOMEPAGE_URL = 'https://occdn.limour.top/';
const PROTECTED_TYPE = 'image/';

async function handleRequest(request) {
  // Fetch the original request
  const response = await fetch(request);

  // If it's an image, engage hotlink protection based on the
  // Referer header.
  const referer = request.headers.get('Referer');
  const contentType = response.headers.get('Content-Type')  '';

  if (referer && contentType.startsWith(PROTECTED_TYPE)) {
    // If the hostnames don't match, it's a hotlink
let refHostname = new URL(referer).hostname 
    if (!refHostname.endsWith('limour.top') && !refHostname.endsWith('j11.fun')) {
      // Redirect the user to your website
      return Response.redirect(HOMEPAGE_URL, 302);
    }
  }

  // Everything is fine, return the response normally.
  return response;
}

addEventListener('fetch', event => {
  event.respondWith(handleRequest(event.request));
});
```