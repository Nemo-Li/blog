---
title: http相关知识点
date: 2019-07-20 16:28:23
tags: http
---

# 前言

HTTP是基于TCP/IP协议的应用层协议，用于客户端和服务器之间的通信，默认80端口。

## 1. HTTP/1.1

1997年1月提出，现在使用最广泛的。

### 1.1 特性

1. `长连接`:  TCP连接默认不关闭，可以被多个请求复用。对于同一个域名，大多数浏览器允许同时建立6个持久连接。默认开启Connection：keep-alive。
2. `管道机制`: 在同一个TCP连接里，可以同时发送多个请求。但是服务器还是要按照请求的顺序进行响应，会造成“队头阻塞”。

### 1.2 报文

设置 Cookie 的响应报文：

```http
HTTP/1.1 200 OK
Date: Thu, 12 Jul 2012 07:12:20 GMT
Server: Apache
<Set-Cookie: sid=1342077140226724; path=/; expires=Wed,
10-Oct-12 07:12:20 GMT>
Content-Type: text/plain; charset=UTF-8
```

携带有 Cookie 信息的请求报文：

```http
GET /image/ HTTP/1.1
Host: hackr.jp
Cookie: sid=1342077140226724
```

#### 1.2.1 报文首部

![HTTP 请求报文的结构](/Users/nemoli/Blog/source/images/报文组成.jpg)

请求报文首部信息实例：

```http
GET / HTTP/1.1
Host: hackr.jp
User-Agent: Mozilla/5.0 (Windows NT 6.1; WOW64; rv:13.0) Gecko/2010010
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*; q=0
Accept-Language: ja,en-us;q=0.7,en;q=0.3
Accept-Encoding: gzip, deflate
DNT: 1
Connection: keep-alive
If-Modified-Since: Fri, 31 Aug 2007 02:02:20 GMT
If-None-Match: "45bae1-16a-46d776ac"
Cache-Control: max-age=0
```

响应报文的首部实例：

```http
HTTP/1.1 304 Not Modified
Date: Thu, 07 Jun 2012 07:21:36 GMT
Server: Apache
Connection: close
Etag: "45bae1-16a-46d776ac"
```

