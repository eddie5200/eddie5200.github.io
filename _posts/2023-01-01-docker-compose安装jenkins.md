---
layout:     post
title:      docker-compose
subtitle:   docker-compose安装jenkins
date:       2023-01-01
author:     eddie
header-img: img/home-bg-o.jpg
catalog: true
tags:
    - linux
---
  
```
version: '3'
services:
  jenkins:
    image: jenkins/jenkins:2.440.2-jdk17
    container_name: jenkins
    ports:
      - "8085:8080"
      - "50000:50000"
    volumes:
      - ./jenkins_home:/var/jenkins_home
      - /etc/localtime:/etc/localtime
```

1. 修改挂在目录的权限，否认会报错
```
    chown 1000.1000 jenkins_home
```

1. 启动
```
   docker-compose up -d

```

4. 查看版本
```
docker exec -it <container_name_or_id> /bin/bash

java -jar /usr/share/jenkins/jenkins.war --version

```