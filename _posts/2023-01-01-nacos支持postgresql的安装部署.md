---
layout:     post
title:       nacos支持postgresql的安装部署
subtitle:    nacos支持postgresql的安装部署
date:       2023-01-01
author:     eddie
header-img: img/home-bg-o.jpg
catalog: true
tags:
    - pgsql
---

# 背景


    nacos默认使用MySQL保存配置数据，在2.2版本之前，如果想使用其他数据库，需要修改nacos源码。2.2版开始提供了数据源扩展插件，以便让需要进行其他数据库适配的用户自己编写插件来保存数据。


# 编译PostgreSQL数据源插件

```
git clone https://github.com/wuchubuzai2018/nacos-datasource-extend-plugins.git
cd nacos-datasource-extend-plugins/nacos-postgresql-datasource-plugin-ext
mvn package
```
编译成功后，插件包保存在nacos-datasource-extend-plugins/nacos-postgresql-datasource-plugin-ext/target目录下，名字一般是nacos-postgresql-datasource-plugin-ext-1.0.0-SNAPSHOT.jar，复制出来备用。
复制nacos-datasource-extend-plugins\nacos-datasource-plugin-ext\nacos-postgresql-datasource-plugin-ext\src\main\resources\schema目录下的nacos-pg.sql备用

# 在PostgreSQL上创建nacos数据库并导入nacos-pg.sql

```
psql -U postgres
create user nacos;
\password nacos
create database nacos encoding=utf8 owner=nacos;
\c nacos nacos
create schema nacos;
\i nacos-pg.sql
```
sql文件在nacos-datasource-extend-plugins/nacos-datasource-plugin-ext/nacos-postgresql-datasource-plugin-ext/src/main/resources/schema

# 安装nacos

```
wget https://github.com/alibaba/nacos/releases/download/2.2.0/nacos-server-2.2.0.tar.gz
tar zxvf nacos-server-2.2.0.tar.gz
cd nacos
mkdir plugins
# 将上面编译好的PostgreSQL数据源插件复制到plugins目录下
```


# 启动单机
bin/startup.sh -m standalone