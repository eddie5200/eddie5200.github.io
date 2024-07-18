---
layout:     post
title:       mysql的GTID主从复制
subtitle:    mysql的GTID主从复制
date:       2023-01-01
author:     eddie
header-img: img/home-bg-o.jpg
catalog: true
tags:
    - mysql
---


## 一：相关概念
```
1. MySQL-5.6.5开始支持的，MySQL-5.6.10后开始完善；
2. GTID (Global Transaction ID) 在整个事务流程中每一个事务 ID 是全局唯一的，且在整个主从复制架构中该 ID 都不会相同。
3. GTID是用来代替传统复制的方法，GTID复制与普通复制模式的最大不同就是不需要指定二进制文件名和位置。
4. 在GTID中【slave】端的binlog是必须开启的，目的是记录执行过的GTID（强制）。
5. GTID用来代替传统复制方法，不再使用MASTER_LOG_FILE+MASTER_LOG_POS开启复制。而是使用MASTER_AUTO_POSTION=1的方式开始复制；
```


## 二：GTID的优缺点

### 优点
```
1. 根据 GTID 可以快速的确定事务最初是在哪个实例上提交的。
2. 更简单的搭建主从复制，确保每个事务只会被执行一次。
3. 一个事务对应一个唯一ID，一个GTID在一个服务器上只会执行一次
4. GTID是用来代替传统复制的方法，GTID复制与普通复制模式的最大不同就是不需要指定二进制文件名和位置
5. 简单的实现 failover故障转移，不用以前那样在需要找 log_file 和 log_pos。
6. 减少手工干预和降低服务故障时间，当主机挂了之后通过软件从众多的备机中提升一台备机为主机
```

### 缺点
```
1. 主从库的表存储引擎必须是一致的
2. 不允许一个SQL同时更新一个事务引擎和非事务引擎的表
3. 不支持create table….select 语句复制（主库直接报错）;
```

## 三：GTID工作原理
```
1. 当一个事务在主库端执行并提交时，产生 GTID，一同记录到 binlog 日志中。
2. binlog 传输到 slave，并存储到 slave 的 relaylog 后，读取这个 GTID 的这个值设置 gtid_next 变量，即告诉 Slave，下一个要执行的 GTID 值。
3. sql 线程从 relay log 中获取 GTID，然后对比 slave 端的 binlog 是否有该 GTID。
4. 如果有记录，说明该 GTID 的事务已经执行，slave 会忽略。
5. 如果没有记录，slave 就会执行该 GTID 事务，并记录该 GTID 到自身的 binlog。
```

## 四：搭建GTID主从复制

### master配置
```
vim /etc/my.cnf    //在[mysqld]下添加如下内容
server-id=1
log-bin = mylog                 //开启binlog日志，不指定绝对路径的话，yum安装方式产生在/var/lib/mysql/,二进制安装方式在/usr/local/mysql/data/
gtid_mode = on                  //在主从服务器上都打开gtid模式
enforce_gtid_consistency=1      //强制使用GTID一致性 consistency：一致性
sync_binlog = 1                 //强制g

##创建用户
mysql> grant replication slave,reload,super on *.*  to 'slave'@'%' identified by 'JiannLt@123';
//注：生产环境中密码采用高级别的密码，实际生产环境中将'%'换成slave的ip
mysql> flush privileges;
```

### slave配置
```
[root@mysql-slave ~]# vim /etc/my.cnf    //[mysqld]添加如下配置
server-id=2    //server-id每台服务器都不能一样，用于标识不同的MySQL服务器实例
gtid_mode = ON    //打开gtid
enforce_gtid_consistency=1        //强制使用GTID一致性
master-info-repository=TABLE    //将主服务器信息保存在mysql.slave_master_info表中
relay-log-info-repository=TABLE    //将中继日志信息保存到mysql.slave_relay_log_info表中
[root@mysql-slave ~]# systemctl restart mysqld
[root@mysql-slave ~]# mysql -uroot -p'JiannLt@123'   //登陆mysql
mysql> change master to            #设置主从关系
master_host='192.168.221.136',     #指定同步的主机
master_user='slave',               #指定同步的账号
master_password='JiannLt@123', #指定slave的密码
master_auto_position=1;            #开启自动同步位置模式
Query OK， 0 rows affected， 2 warnings (0.02 sec)

mysql> start slave;   #启动slave角色
Query OK， 0 rows affected (0.00 sec)
mysql> show slave status\G  #查看状态，验证sql和IO是不是yes。
说明同步成功
Slave_IO_Running： Yes
Slave_SQL_Running： Yes

```

## 五：重设从库
```
#全在从库执行
mysql> stop slave;
mysql> reset slave;
mysql> reset master; 
​
#从库的binlog已经无效了，所以要执行这个命令清空binlog
mysql> change master to 
master_host='192.168.221.136',
master_port=3306,
master_user='slave',
master_password='JiannLt@123',
master_auto_position=1;
​#master_auto_position=1;表示是否自动获取主库的binary log坐标点，设置为1表示是。
​
mysql> start slave;   #启动slave角色
Query OK， 0 rows affected (0.00 sec)
​
mysql> show slave status\G  #查看状态，验证sql和IO是不是yes。

```


## 六：异常处理

### GTID主从异常报错1236

```
 Last_IO_Error: Got fatal error 1236 from master when reading data from binary log: 'The slave is connecting using CHANGE MASTER TO MASTER_AUTO_POSITION = 1, but the master has purged binary logs containing GTIDs that the slave requires. Replicate the missing transactions from elsewhere, or provision a new slave from backup. Consider increasing the master's binary log expiration period. The GTID set sent by the slave is '', and the missing transactions are '6c15ecda-03f3-11ec-987e-6c92bff6906b:1-5,
a75b06d9-03ec-11ec-9ca3-6c92bff6a8fb:1-6'.'
```

### 解决办法
1. 在主节点执行
```
show variables like '%gtid_purged%';
```
2. 从节点执行
```
stop slave;
set global gtid_purged="6c15ecda-03f3-11ec-987e-6c92bff6906b:1-5,
a75b06d9-03ec-11ec-9ca3-6c92bff6a8fb:1-6";
start slave;
show slave status\G
```