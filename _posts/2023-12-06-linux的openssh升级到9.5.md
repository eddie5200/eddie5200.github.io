---
layout:     post
title:      linux常用操作
subtitle:   openssh升级到9.5
date:       2023-04-11
author:     eddie
header-img: img/home-bg-o.jpg
catalog: true
tags:
    - linux
---


```
centos7默认openssh是7.4 升级到9.5需升级openssl

1.查看版本
	ssh -V
	openssl version

2.安装openssl  #这里我的目录选择了/usr 是因为系统最初始的openssl的目录就是/usr 这样可以省去的软连接、更新链接库的问题

	tar -xzvf openssl-1.1.1w.tar.gz
	cd openssl-1.1.1w/
	./config --prefix=/usr
	make && make install

3.验证openssl
	whereis openssl
	openssl version

4.升级openssh  
	1. 安装telnet-server  
        #有防火墙记得关闭防火墙，并关闭SELinux
		yum install telnet* -y
		systemctl  start telnet.socket
		systemctl enable telnet.socket
		mv /etc/securetty /etc/securetty.bak   #临时关闭安全登录，否则无法进行远程telnet连接

	2. 安装依赖包
		yum install -y gcc pam-devel rpm-build wget zlib-devel openssl-devel net-tools

	3.备份
		mv /etc/ssh /etc/ssh.bak
		mv /usr/bin/ssh /usr/bin/ssh.bak
		mv /usr/sbin/sshd /usr/sbin/sshd.bak
		mv /etc/pam.d/sshd  /etc/pam.d/sshd.old


	4. 安装新版opensssh
		wget https://cdn.openbsd.org/pub/OpenBSD/OpenSSH/portable/openssh-9.5p1.tar.gz
		tar -xzvf openssh-9.5p1.tar.gz
		cd openssh-9.5p1
		./configure --prefix=/usr --sysconfdir=/etc/ssh  --with-pam   --with-ssl-dir=/usr/local/lib64/
		make
		make install
        cd /etc/pam.d/
        mv sshd.old sshd
		
	5. 恢复ssh pam认证
		cd openssh-9.5p1/
		cp contrib/redhat/sshd.init /etc/init.d/sshd
		chkconfig --add sshd
		systemctl  enable sshd
		
	6.修改sshd配置文件：
		vi /etc/ssh/sshd_config

		修改以下选项
		PermitRootLogin yes
		SyslogFacility AUTHPRIV 
		Subsystem sftp /usr/libexec/openssh/sftp-server
		
	7.重启sshd
		systemctl restart sshd
	
	8.验证
		ssh -V
		openssl version
		
```





















