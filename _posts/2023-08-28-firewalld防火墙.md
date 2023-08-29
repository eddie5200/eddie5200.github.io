---
layout:     post
title:      firewalld防火墙
subtitle:   firewalld防火墙的命令
date:       2023-08-28
author:     eddie
header-img: img/home-bg-o.jpg
catalog: true
tags:
    - firewalld
---

	#展示当前配置的firewall规则
	firewall-cmd --list-all

	#重载防火墙
	firewall-cmd --reload


	# 查询端口是否开放
	firewall-cmd --query-port=8080/tcp


	# 新建永久规则，开放8080端口（TCP协议）
	firewall-cmd --permanent --add-port=8080/tcp


	# 移除上述规则
	firewall-cmd --permanent --remove-port=8080/tcp


	# 新建永久规则，批量开放一段端口（TCP协议）
	firewall-cmd --permanent --add-port=9001-9100/tcp


	# 新建永久规则，开放192.168.1.1单个源IP的访问
	firewall-cmd --permanent --add-source=192.168.1.1


	# 新建永久规则，开放192.168.1.0/24整个源IP段的访问
	firewall-cmd --permanent --add-source=192.168.1.0/24


	# 移除上述规则
	firewall-cmd --permanent --remove-source=192.168.1.1


	# 允许指定IP访问本机8080端口
	firewall-cmd --permanent --add-rich-rule='rule family="ipv4" source address="192.168.1.1" port protocol="tcp" port="8080" accept'


	# 允许指定IP段访问本机8080-8090端口
	firewall-cmd --permanent --add-rich-rule='rule family="ipv4" source address="192.168.1.0/24" port protocol="tcp" port="8080-8090" accept'


	# 禁止指定IP访问本机8080端口
	firewall-cmd --permanent --add-rich-rule='rule family="ipv4" source address="192.168.1.1" port protocol="tcp" port="8080" reject'


---