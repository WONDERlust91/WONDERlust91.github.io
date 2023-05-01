---
title: nginx在访问链接末尾不加斜杠导致的自动重定向
date: 2023-05-01 11:08:13
tags:
  - nginx
  - 重定向
categories: nginx
---

## 问题描述

在访问网站链接时，我们通常不会注意链接的末尾是否加了`/`，如果我们没有加`/`，那么 Nginx 会自动使用 301 重定向，帮我们加上`/`，以确保访问的是目录。

若 Nginx 前端还有其他服务（如负载均衡），且这个服务的端口与 Nginx 服务端口不一致时，Nginx 的自动重定向就会导致端口错误。

例如`443`端口为负载均衡服务，`8080`端口为 Nginx 服务，对`443`的访问最终都回落到了`8080`下，那么我们访问`https://www.example.com/foo`时，由于链接最后没加`/`，Nginx 会自动重定向补`/`，我们期待的结果是`https://www.example.com/foo/`，但实际结果为`http://www.example.com:8080/foo/`，重定向带上了原始服务的端口。

<!-- more -->

## 解决方案

### 方法一：设置`absolute_redirect`

```properties
server {
  listen 8080;
  server_name www.example.com;
  absolute_redirect off;
}
```

关闭绝对路径重定向，仅对相对路径部分`/foo`进行重定向。

### 方法二：设置`port_in_redirect`

```properties
server {
  listen 8080;
  server_name www.example.com;
  port_in_redirect off;
}
```

保持绝对路径重定向，但重定向时不携带端口号。

### Nginx 重定向配置

| Directives              | Default | Context               | Description                                                                                                                                                                                                                                                                                                            |
| ----------------------- | ------- | --------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| absolute_redirect       | on      | http, server, location  | If disabled, redirects issued by nginx will be relative.                                                                                                                                                                                                                                                               |
| server_name_in_redirect | off     | http, server, location | Enables or disables the use of the primary server name, specified by the server_name directive, in absolute redirects issued by nginx. When the use of the primary server name is disabled, the name from the “Host” request header field is used. If this field is not present, the IP address of the server is used. |
| port_in_redirect        | on      | http, server, location  | Enables or disables specifying the port in absolute redirects issued by nginx.                                                                                                                                                                                                                                         |

## 扩展知识

Nginx 配置中`proxy_pass`的 URL 是否包含子路径，决定了是否会替换`location`中匹配的路径。

如果`proxy_pass`中的 URL 不包含子路径，则`location`中的路径不会被替换，仍被拼接在代理的 URL 后，即`proxy_pass` + `location`。

如果`proxy_pass`中的 URL 包含子路径（哪怕是`/`），则`location`中的路径被替换，即直接使用`proxy_pass`。

```properties
server {
  server_name www.example.com;

  location ~^/foo {
    proxy_pass https://www.test.com;
  }
}
```

上述例子中，访问`www.example.com/foo` 会被代理到`https://www.test.com/foo`。

```properties
server {
  server_name www.example.com;

  location ~^/foo {
    proxy_pass https://www.test.com/;
  }
}
```

上述例子中，访问`www.example.com/foo` 会被代理到`https://www.test.com/`。

```properties
server {
  server_name www.example.com;

  location ~^/foo {
    proxy_pass https://www.test.com/bar;
  }
}
```

上述例子中，访问`www.example.com/foo` 会被代理到`https://www.test.com/bar`。

> 参考资料
>
> [Module ngx_http_core_module](https://nginx.org/en/docs/http/ngx_http_core_module.html)
>
> [Nginx 在 URL 末尾不加斜杠的重定向](https://blog.csdn.net/yk10010/article/details/109586879)
>
> [nginx 反向代理 proxy_pass 配置 URI 引起的思考](https://blog.csdn.net/xiaoxiangzi520/article/details/78737527)
