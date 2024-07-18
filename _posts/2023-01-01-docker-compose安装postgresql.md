---
layout:     post
title:      docker-compose
subtitle:   docker-compose安装pgsql
date:       2023-01-01
author:     eddie
header-img: img/home-bg-o.jpg
catalog: true
tags:
    - docker-compose
---
```shell

version: '3'
services:
  pgsql:
    image: postgres:14.8
    container_name: pgsql
    restart: always
    environment:
      POSTGRES_DB: charge
      POSTGRES_USER: postgresql
      POSTGRES_PASSWORD: charge0417@.+
      TZ: Asia/Shanghai
      PGDATA: /var/lib/postgresql/data/pgdata
    ports:
      - 5432:5432
    volumes:
      - /weio/devsoft/pgsql/data:/var/lib/postgresql/data/pgdata
      - /weio/devsoft/pgsql/conf/postgresql.conf:/var/lib/postgresql/data/postgresql.conf
```