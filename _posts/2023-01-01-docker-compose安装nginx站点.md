---
layout:     post
title:      docker-compose
subtitle:   docker-compose安装nginx
date:       2023-01-01
author:     eddie
header-img: img/home-bg-o.jpg
catalog: true
tags:
    - docker-compose
---
####
1.安装docker-compose


####
2.创建docker-compose.yml 文件

  ```
version: '3'
services:
  nginx:
    restart: always
    container_name: nginx
    image: nginx
    ports:
      - 80:80
      - 443:443
    volumes:
      - /nginx/html:/usr/share/nginx/html
      - /nginx/www:/var/www
      - /nginx/logs:/var/log/nginx
      - /nginx/nginx.conf/:/etc/nginx/nginx.conf
      - /nginx/etc/cert:/etc/nginx/cert
      - /nginx/conf.d:/etc/nginx/conf.d
    environment:
      - NGINX_PORT=80
      - TZ=Asia/Shanghai
    privileged: true

  ```

####
3.创建挂载目录
```
      mkdir -p /data/nginx/{html www logs  etc/cert conf.d}
      mkdir -p /data/nginx/nginx.conf

```


####
4.配置nginx站点密码

```
        yum install -y httpd-tools
        htpasswd -c /nginx/etc/cert/htpasswd/passwd.db admin

```

###
5.配置nginx.conf
```
        user  root;
        worker_processes  auto;

        error_log  /var/log/nginx/error.log notice;
        pid        /var/run/nginx.pid;


        events {
            worker_connections  1024;
        }


        http {
            include       /etc/nginx/mime.types;
            default_type  application/octet-stream;

            log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                            '$status $body_bytes_sent "$http_referer" '
                            '"$http_user_agent" "$http_x_forwarded_for"';

            access_log  /var/log/nginx/access.log  main;

            sendfile        on;
            tcp_nopush     on;
            keepalive_timeout  65;
            gzip  on;
            include /etc/nginx/conf.d/*.conf;
}
```

####
6.配置conf.d/default.conf
```
server{
       listen 80;
       server_name localhost ;
       charset utf-8;

       location / {
          root   /usr/share/nginx/html;
          try_files $uri $uri/ =404;
          index  index.html index.htm;
          autoindex on;
          autoindex_format html;
          autoindex_exact_size off;
          autoindex_localtime on;
          auth_basic 'admin';   
          auth_basic_user_file /etc/nginx/cert/passwd.db; 
       }
       
       error_page   500 502 503 504  /50x.html;
       location = /50x.html {
           root   html;
       }
    }
```

####
7.启动服务

```
    docker-compose up -d
```
