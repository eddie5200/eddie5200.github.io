---
layout:     post
title:      linux常用操作
subtitle:   openssh升级到8.9
date:       2023-04-11
author:     eddie
header-img: img/home-bg-o.jpg
catalog: true
tags:
    - linux
---


```
centos7默认openssh是7.4 升级到8.9无需升级openssl

一、防止ssh升级失败连接不上，首先开启telnet服务：
安装xinetd和telnet-server
yum -y install xinetd yum -y install telnet-server

配置开机启动
systemctl enable xinetd.service systemctl enable telnet.socket

启动服务
systemctl start telnet.socket systemctl start xinetd

查看端口,看到23端口已打开
netstat -tunlp|grep 23

开启防火墙允许访问23端口(没开防火墙跳过此步骤)
firewall-cmd --add-port=23/tcp --permanent firewall-cmd --reload

默认root无法远程访问，修改/etc/securetty
vi /etc/securetty #在末尾添加 pts/0 pts/1 pts/2 pts/3 pts/4

测试用telnet连接
telnet 192.168.139.101 23 输入用户 输入密码
	
查看安装版本
ssh -V
	
1、openssh源码包下载：
wget https://cdn.openbsd.org/pub/OpenBSD/OpenSSH/portable/openssh-8.9p1.tar.gz

2、备份ssh相关文件：
cd /usr/bin/ 
mkdir bak.1021 
cp -arf scp sftp ssh ssh-add ssh-agent ssh-keygen ssh-keyscan  bak.1021/ 
mv /usr/libexec/openssh/sftp-server  bak.1021/ 
ls bak.1021/

cd /usr/sbin/ 
mkdir bak.1021 
cp -arf sshd bak.1021/ 
ls bak.1021/

3、备份ssh配置文件：
mv /etc/ssh /etc/ssh.1021

4、安装依赖：
yum -y install gcc yum -y install zlib yum -y install zlib-devel yum -y install openssl-devel yum -y install pam-devel

5、编译安装：
tar xf openssh-8.9p1.tar.gz 
cd openssh-8.9p1
./configure --prefix=/usr/local/openssh --sysconfdir=/etc/ssh --with-pam --with-zlib --with-md5-passwords --with-tcp-wrappers --without-openssl-header-check
make && make install

6、修改sshd配置文件：
vi /etc/ssh/sshd_config

修改以下选项
PermitRootLogin yes
SyslogFacility AUTHPRIV 
Subsystem sftp /usr/libexec/openssh/sftp-server

7、拷贝新版本ssh：
\cp -arf /usr/local/openssh/sbin/sshd /usr/sbin/
\cp -arf /usr/local/openssh/bin/* /usr/bin/

8、#拷贝新版本sftp-server
\cp /usr/local/openssh/libexec/sftp-server /usr/libexec/openssh/

```





















