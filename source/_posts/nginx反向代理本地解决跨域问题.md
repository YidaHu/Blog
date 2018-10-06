---
title: nginx反向代理本地解决跨域问题
date: 2018-08-06 23:08:59
categories: 综合应用
tags:
- Nginx
- 跨域
---

Nginx可以用来做：静态页面的服务器、静态文件缓存服务器、网站反向代理、负载均衡服务器等等，而且实现这一切，基本只需要改改那万能的配置文件即可。

我们跨域是有时候借助了浏览器对 Access-Control-Allow-Origin 的支持。但有些浏览器是不支持的 。

现在我们来利用nginx 通过**反向代理**满足浏览器的同源策略实现**跨域** 。

<!--MORE-->



**nginx.conf 配置一个反向代理路径** 

```conf
server {
        listen       80;
        server_name  localhost;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

       location / {
            root   html;
            index  index.html index.htm;
        }
        location /apis {
			rewrite  ^.+apis/?(.*)$ /$1 break;
			include  uwsgi_params;
			proxy_pass   http://localhost:8888;
       }
	}

```

配置一个/apis  重写到我们真正的api地址[http://localhost:8888](http://localhost:8888/)  形成一个代理的过程。 