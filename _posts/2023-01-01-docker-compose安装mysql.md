---
layout:     post
title:      docker-compose
subtitle:   docker-compose安装mysql
date:       2023-01-01
author:     eddie
header-img: img/home-bg-o.jpg
catalog: true
tags:
---

```shell
version: '3.9'  # 使用Docker Compose的版本

services:
  mysql:
    image: mysql:5.7  # 使用官方MySQL镜像的最新版本，也可以指定具体版本，如 mysql:8.0
    container_name: mysql  # 自定义容器名称（可选）
    restart: always  # 保证容器在停止或退出时自动重启
    ports:
      - "3306:3306"  # 映射宿主机的3306端口到容器内的MySQL端口
    environment:  # 设置必要的环境变量
      MYSQL_ROOT_PASSWORD: eddie  # 设置MySQL root用户的密码
      MYSQL_DATABASE: devops  # 创建的初始数据库名称（可选）
      MYSQL_USER: eddie  # 创建的非root用户（用于应用程序连接）
      MYSQL_PASSWORD: eddie520  # 非root用户的密码
    command: [
        '--character-set-server=utf8mb4',
        '--collation-server=utf8mb4_general_ci',
        '--max_connections=3000'
     ]
    volumes:  # 使用数据卷持久化MySQL数据
      - ./mysql_data:/var/lib/mysql  # 定义名为mysql_data的数据卷，并挂载到容器内的MySQL数据目录
      - ./log:/var/log/mysql
      - ./my.cnf:/etc/mysql/my.cnf
```
```shell
###my.cnf
[client]
#password    = your_password
port        = 3306
socket        = /tmp/mysql.sock
 
[mysqld]
port        = 3306
socket        = /tmp/mysql.sock
datadir = /data/
default_storage_engine = InnoDB
performance_schema_max_table_instances = 400
table_definition_cache = 400
skip-external-locking
key_buffer_size = 128M
max_allowed_packet = 100G
table_open_cache = 512
sort_buffer_size = 2M
net_buffer_length = 4K
read_buffer_size = 2M
read_rnd_buffer_size = 256K
myisam_sort_buffer_size = 32M
thread_cache_size = 64
query_cache_size = 64M
tmp_table_size = 64M
sql-mode=NO_ENGINE_SUBSTITUTION,STRICT_TRANS_TABLES
 
explicit_defaults_for_timestamp = true
#skip-name-resolve
max_connections = 500
max_connect_errors = 100
open_files_limit = 65535
 
log-bin=mysql-bin
binlog_format=mixed
server-id = 1
expire_logs_days = 10
slow_query_log=1
slow-query-log-file=/data/mysql-slow.log
long_query_time=3
#log_queries_not_using_indexes=on
early-plugin-load = ""
 
 
innodb_data_home_dir = /data/
innodb_data_file_path = ibdata1:10M:autoextend
innodb_log_group_home_dir = /data/
innodb_buffer_pool_size = 512M
innodb_log_file_size = 256M
innodb_log_buffer_size = 64M
innodb_flush_log_at_trx_commit = 1
innodb_lock_wait_timeout = 50
innodb_max_dirty_pages_pct = 90
innodb_read_io_threads = 4
innodb_write_io_threads = 4
 
[mysqldump]
quick
max_allowed_packet = 500M
 
[mysql]
no-auto-rehash
 
[myisamchk]
key_buffer_size = 128M
sort_buffer_size = 2M
read_buffer = 2M
write_buffer = 2M
 
[mysqlhotcopy]
interactive-timeout
```