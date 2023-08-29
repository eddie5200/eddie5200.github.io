---
layout:     post
title:      python常用库
subtitle:   map，reduce操作
date:       2023-08-28
author:     eddie
header-img: img/home-bg-o.jpg
catalog: true
tags:
    - python
---
     
	from functools import reduce
	def StrToDict(data):
		'''
		将spring.datasource.druid.password = "xxxxx" 转为{'spring': {'datasource': {'druid': 'password = "xxxxx"'}}}
		'''
		line_dic=reduce(lambda res,curl:{curl:res},reversed(data.replace('\n','').split(".")))
		return line_dic

---