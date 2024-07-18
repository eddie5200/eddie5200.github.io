---
layout:     post
title:      docker-compose
subtitle:   docker-compose安装mongodb
date:       2023-01-01
author:     eddie
header-img: img/home-bg-o.jpg
catalog: true
tags:
    - docker-compose
---

```shell
version: '3.8'
services:
  mongodb:
    image: mongo:latest
    container_name: mongodb
    restart: always
	environment:                        
      TZ: Asia/Shanghai
      LANG: en_US.UTF-8
      MONGO_INITDB_ROOT_USERNAME: root
      MONGO_INITDB_ROOT_PASSWORD: 123456
    ports:
      - "27017:27017"
    volumes:
      - ./mongodb_data:/data/db
```