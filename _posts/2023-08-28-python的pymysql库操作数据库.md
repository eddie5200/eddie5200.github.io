---
layout:     post
title:      python常用库
subtitle:   pymysql增删改查数据库
date:       2023-08-28
author:     eddie
header-img: img/home-bg-o.jpg
catalog: true
tags:
    - python
---
	import pymysql
	from datetime import datetime
	class Mysql(object):

		def __init__(self,host,port,user,password,database):
			self.host = host
			self.port = port
			self.user = user
			self.password = password
			self.database = database

		def connect(self):
			conn=pymysql.connect(
				host=self.host,
				port=int(self.port),
				user=self.user,
				password=self.password,
				database=self.database,
				read_timeout=10
			)

			return conn


		def query_one_data(self,sql,flag=None):
			'''
			:param sql: 查询的sql
			:param sql: 返回的数据个数 默认None返回元组，dict 返回字典
			:return: 返回一条查询数据
			'''
			conn = self.connect()
			if flag == "dict":
				curs = conn.cursor(cursor=pymysql.cursors.DictCursor)
			else:
				curs = conn.cursor()
			curs.execute(sql)
			data = curs.fetchone()
			curs.close()
			conn.close()
			return data

		def query_all_data(self,sql,flag=None):
			'''
			:param sql: 查询的sql
			:param sql: 返回的数据个数 默认None返回元组，dict 返回字典
			:return: 返回所有的查询数据
			'''
			conn=self.connect()
			if flag == "dict":
				curs = conn.cursor(cursor=pymysql.cursors.DictCursor)
			else:
				curs = conn.cursor()
			curs.execute(sql)
			data=curs.fetchall()
			curs.close()
			conn.close()
			return data

		def query_many_data(self,sql,num,flag=None):
			'''
			:param sql: 查询的sql
			:param sql: 返回的数据个数 默认None返回元组，dict 返回字典
			:return: 返回指定数量的查询数据
			'''
			conn=self.connect()
			if flag == "dict":
				curs = conn.cursor(cursor=pymysql.cursors.DictCursor)
			else:
				curs = conn.cursor()
			curs.execute(sql)
			data=curs.fetchmany(size=num)
			curs.close()
			conn.close()
			return data

		def add_many_data(self,sql,data):
			'''

			:param sql: sql
			:param data: 序列化的序列化
			:return: 此函数作为参考，写入多条数据，实际根据sql和data来写
			'''

			conn = self.connect()
			curs = conn.cursor()

			# 定义要执行的sql语句
			sql = 'insert into userinfo(user,pwd) values(%s,%s);'
			data = [
				('july', '147'),
				('june', '258'),
				('marin', '369')
			]

			curs.executemany(sql, data)
			curs.executemany()
			conn.commit()
			curs.close()
			conn.close()

		def add_one_data(self,sql,data):
			'''

			:param sql: sql
			:param data: 序列化的序列化
			:return: 此函数作为参考，写入单条数据，实际根据sql和data来写
			'''

			conn = self.connect()
			curs = conn.cursor()

			# 定义要执行的sql语句
			sql = 'insert into userinfo (user,pwd) values (%s,%s);'
			name = 'wuli'
			pwd = '123456789'
			curs.execute(sql, [name, pwd])
			curs.executemany()
			conn.commit()
			curs.close()
			conn.close()


---