---
layout: mypost
title: 编写一个location，要求index.html文件永远不要缓存
tags:
  - nginx
---

可以使用以下 Nginx 配置，来实现对 index.html 文件的缓存控制：

```
location = /index.html {
  add_header Cache-Control "no-store";
  expires -1;
}
```

在上面的配置中，我们指定了一个 location，它的请求路径是 /index.html。然后，我们使用 add_header 指令添加了一个 Cache-Control 头部，指示浏览器和代理服务器不要缓存该文件。注意，指定 no-store 未必可以强制禁止缓存，但无论如何，它能够将响应尽可能地保持新鲜，即每次都从服务端重新获取。此外，我们还使用了 expires 指令，将 expires 头部设置为一个过去的时间，以确保浏览器会立即过期缓存。

当然，以上配置只会影响 index.html 文件，其他文件的缓存控制还需要根据实际情况进行相应的配置。

> expires -1; 有必要吗？

expires -1 指令的作用是将 expires 头部设置为一个过去的时间。它的作用是让浏览器不缓存该响应，因为响应已经过期了。虽然它并不是必要的，但是在某些情况下可以提供额外的安全保证。这是因为有些浏览器或代理服务器可能不支持或不遵守 Cache-Control 头部中的指令，比如说 no-store 指令，并且如果只设置了 Cache-Control 头部，而没有设置 expires 头部，那么这些浏览器或代理服务器可能会缓存响应。

因此，即使已经通过 Cache-Control 头部指示浏览器不缓存响应，使用 expires -1 指令也能够提供额外的安全保证，确保该响应不会被缓存。此外，expires -1 还能够提高网站的安全性，因为它可以防止用户在一些情况下从缓存中访问过期的敏感数据或信息，从而降低了网站遭受攻击的风险。

所以，虽然 expires -1 指令不是必要的，但是建议在需要强制禁止缓存时，同时使用 add_header 和 expires 指令来控制缓存策略。
