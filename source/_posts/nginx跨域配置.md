---
title: nginx跨域配置
date: 2022-02-10 09:46:47
tags:
  - CORS
  - 跨域
  - nginx
categories: nginx
---

在使用 nginx 存放静态资源时，直接访问不会有问题，但是通过 fetch 去请求资源时，就会触发浏览器跨域限制。如何配置静态资源的 nginx 服务，以避免跨域问题呢？

## 最简配置

```shell
server {
  # 通常不建议在 server 块下直接进行配置，因为 server 块下的 if 块内不支持 add_header，而location块下是支持的。
  location / {
    # 任意 OPTIONS 预检请求，都返回成功
    if ($request_method = 'OPTIONS') {
      # 204表示无返回内容的成功
      return 204;
    }
    # 增加允许跨域主机源的头
    add_header Access-Control-Allow-Origin "*"; # "*" 为任意主机源，生产环境安全起见最好配置为具体的IP或域名
    # 增加允许跨域请求方法的头
    add_header Access-Control-Allow-Methods "GET, POST, OPTIONS";
    # 增加允许跨域请求头字段的头，注意如果有一些自定义增加的头如Authorization等，也要加到这个配置中
    add_header Access-Control-Allow-Headers "DNT,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type,Range,Authorization";
  }
}
```

<!-- more -->

## 分请求方法配置

```shell
location / {
  if ($request_method = 'OPTIONS') {
    add_header 'Access-Control-Allow-Origin' '*';
    add_header 'Access-Control-Allow-Methods' 'GET, POST, OPTIONS';
    add_header 'Access-Control-Allow-Headers' 'DNT,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type,Range';
    # 预检请求（pre-flight）信息有效期为20天
    add_header 'Access-Control-Max-Age' 1728000;
    # 设置内容类型头
    add_header 'Content-Type' 'text/plain; charset=utf-8';
    # 设置内容长度为0
    add_header 'Content-Length' 0;
    return 204;
  }
  if ($request_method ~* "(GET|POST)") {
    add_header 'Access-Control-Allow-Origin' '*' always; # always 参数表示无论返回码（response code）是什么，都会加上该头。
    add_header 'Access-Control-Allow-Methods' 'GET, POST, OPTIONS' always;
    add_header 'Access-Control-Allow-Headers' 'DNT,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type,Range,Authorization' always;
    # 暴露头配置，XMLHttpRequest 2 对象的 getResponseHeader() 方法只能返回简单请求的头信息，如：Cache-Control、Content-Language、Content-Type、Expires、Last-Modified、Pragma
    # 如果需要客户端能获得自定义头与非简单请求的头，就需要把这些头都写在该配置下
    add_header 'Access-Control-Expose-Headers' 'Content-Length,Content-Range,Authorization' always;
  }
}
```

## 分文件类型配置

前面两种配置方法都是全局跨域，如果只想针对特定的文件类型配置跨域，则可按照如下方式配置：

```shell
location ~* \.(eot|ttf|woff|woff2|geojson|json)$ {
  # 其他跨域配置
  add_header Access-Control-Allow-Origin *;
  ...
}
```

> 参考资料
>
> [How do I add Access-Control-Allow-Origin in NGINX?](https://serverfault.com/questions/162429/how-do-i-add-access-control-allow-origin-in-nginx)
>
> [CORS on Nginx](https://enable-cors.org/server_nginx.html)
