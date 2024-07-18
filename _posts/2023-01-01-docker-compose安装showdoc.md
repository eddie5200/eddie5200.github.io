---
layout:     post
title:      docker-compose
subtitle:   docker-compose安装showdoc
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
      version: '3.5'
      services:
        showdoc:
          image: star7th/showdoc
          container_name: showdoc
          hostname: showdoc
          restart: always
          logging:
            driver: 'json-file'
            options:
              max-size: '5g'
          ports:
            - 4999:80
          volumes:
            - /data/html:/var/www/html
          privileged: true
          user: root
  ```

####
3.下载镜像
```
  # 原版官方镜像安装命令(中国大陆用户不建议直接使用原版镜像，可以用后面的加速镜像)
    docker pull star7th/showdoc 
  
  # 中国大陆镜像安装命令（安装后记得执行docker tag命令以进行重命名）
    docker pull registry.cn-shenzhen.aliyuncs.com/star7th/showdoc
    docker tag registry.cn-shenzhen.aliyuncs.com/star7th/showdoc:latest star7th/showdoc:latest


```


####
4.创建数据目录
```
      mkdir /data/html
      chmod  -R 777  /data/html
```


####
5.启动
```
    docker-compose up -d 
```

####
6.访问
```
    访问地址： http://192.168.10.106:4999/

    默认用户名：showdoc

    默认密码： 123456
```