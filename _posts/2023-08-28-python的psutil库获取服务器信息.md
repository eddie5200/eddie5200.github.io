---
layout:     post
title:      python常用库
subtitle:   python获取服务器基本监控信息
date:       2023-08-28
author:     eddie
header-img: img/home-bg-o.jpg
catalog: true
tags:
    - python
---
	import psutil
	from datetime import datetime
	class BaseInfo(object):

		def __init__(self):
			self.check_time=datetime.datetime.now()

		def get_cpu_info(self):
			cpu_data=psutil.cpu_percent(interval=1,percpu=False)
			return cpu_data
			print(cpu_data)

		def get_men_info(self):
			men_data=psutil.virtual_memory()
			total=int(men_data.total)/1024/1024/1024
			available=int(men_data.available)/1024/1024/1024
			percent = int(men_data.percent)
			used=int(men_data.used)/1024/1024/1024
			free=int(men_data.free)/1024/1024/1024
			active=int(men_data.active)/1024/1024/1024
			inactive=int(men_data.inactive)/1024/1024/1024
			buffers=int(men_data.buffers)/1024/1024/1024
			cached=int(men_data.cached)/1024/1024/1024
			shared=int(men_data.shared)/1024/1024/1024
			slab=int(men_data.slab)/1024/1024/1024
			check_time=datetime.datetime.now()
			all_data=[
				{"total":total},
				{"available": available},
				{"percent": percent},
				{"used": used},

			]
			data={"type":"men","data":all_data,"check_time":self.check_time}
			print(data)


		def get_disk_info(self):
			all_data=[]
			disks=psutil.disk_partitions()
			for i in disks:
				disk_name=i.mountpoint
				device=i.device
				file_type=i.fstype
				u=disk_usage(disk_name)
				total=u.total
				used=u.total
				free=u.free
				percent=u.percent
				all_data.append({"device": device, "total": total,
								 "used": used, "free": free, "disk_name": disk_name,
								 "percent": percent, "file_type": file_type
								 })
			data={"type":"disk","data":all_data,"check_time":self.check_time}
			print(data)


		def get_users(self):
			'''
			:return: 获取登录用户
			'''
			users_list=psutil.users()
			all_data=[]
			for user in users_list:
				name=user.name
				terminal = user.terminal
				host = user.host
				started = user.started
				pid = user.pid
				all_data.append({
					"name":name,
					"terminal":terminal,
					"host":host,
					"started":started,
					"pid":pid
				})

			data={"type":"user_info","data":all_data,"check_time":self.check_time}
			print(data)


		def get_pids(self):
			'''
			:return: 获取进程相关的信息
			'''
			pids_list=psutil.pids()
			all_data=[]
			for i in pids_list:
				if i > 1024:
					p=psutil.Process(i)
					all_data.append({
						"name":p.name(),
						"exe":p.exe(),
						"cwd":p.cwd(),
						"status":p.status(),
						"username":p.username(),
						"create_time":p.create_time(),
						"cmdline":p.cmdline(),
						"num_threads":p.num_threads(),
						"men_percent":p.memory_percent()
					})


			data={"type":"process","data":all_data,"check_time":self.check_time}
			print(data)



---
