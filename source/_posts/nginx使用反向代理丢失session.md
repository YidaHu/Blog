---
title: 解决nginx使用proxy_pass反向代理时,session丢失的问题
date: 2018-08-23 20:57:48
categories: 综合应用
tags:
- Nginx
- 会话保持
---

Nginx作为反向代理到Tomcat应用时，session丢失的问题,此处使用Nginx代理转发来进行会话保持 。 

<!--MORE-->

**nginx.conf 配置** 

```
server{
                listen 8888;  #端口
                server_name localhost;
                root /var/www/html/dist;   #网站路径
                index index.html;
                location / {
                        root /var/www/html/dist;
                        index index.html index.htm;
                        try_files $uri $uri/ /index.html =404;
                }
                location /apis {   #代理url
                        #rewrite  ^.+apis/?(.*)$ /$1 break;   #正则，apis后所有转发
                        include  uwsgi_params;
                        proxy_pass   http://127.0.0.1:8081/examonline;    #转发地址
                        proxy_set_header Host $http_host;      
                        proxy_cookie_path /examonline /apis;    #会话保持
                }
        }
```

