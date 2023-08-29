---
layout:     post
title:      python常用库
subtitle:   requests库 下载文件
date:       2023-08-28
author:     eddie
header-img: img/home-bg-o.jpg
catalog: true
tags:
    - python
---
	import requests
	class DownFile(object):

		def __init__(self,user=None,passwd=None):
			self.user=user
			self.passwd=passwd

		def formatszie(self,bytes):
			'''
			字节转换为K，M，G
			:param bytes: 字节大小
			:return: 返回Kb,M，G
			'''
			try:
				bytes=float(bytes)
				Kb=bytes/1024
			except :
				print("字节格式错误")
				return "ERROR"

			if Kb>1024:
				M=Kb/1024
				if M>1024:
					G=M/1024
					return f"{int(G)}G"
				else:
					return f"{int(M)}M"
			else:
				return f"{int(Kb)}Kb"


		def small_file_down(self,url,filename):
			'''
			下载小文件,建议是文件大小小于5M
			:param filename: 下载后的文件名，最好使用全路径
			:return: 返回下载的文件
			'''
			if self.user and self.passwd:
				r=requests.get(url,auth=HTTPBasicAuth(self.user,self.passwd))
			else:
				r = requests.get(url)
			with open(filename,"wb") as f:
				f.write(r.content)
			f.close()
			pwd = os.getcwd()
			path = pwd + '/' + filename
			if len(r.content)==os.path.getsize(path):
				return {"path":pwd+'/'+filename,"size":os.path.getsize(filename),"status":"true"}
			else:
				return {"path": pwd + '/' + filename, "size": os.path.getsize(filename), "status": "false"}

		def big_file_down(self,url,filename):
			'''
			大文件下载，1M 大小的分块下载
			:param url: 要下载的文件url
			:param filename: 下载保存的文件
			:return: 返回下载的文件路径，下载后的文件大小
			'''
			if self.user and self.passwd:
				r=requests.get(url,auth=HTTPBasicAuth(self.user,self.passwd))
			else:
				r = requests.get(url)
			with open(filename,"wb") as f:
				for chunk in r.iter_content(chunk_size=1024 * 1024):
					if chunk:
						f.write(chunk)
				f.close()
			pwd = os.getcwd()
			path = pwd + '/' + filename
			if len(r.content)==os.path.getsize(path):
				return {"path":pwd+'/'+filename,"size":os.path.getsize(filename),"status":"true"}
			else:
				return {"path": pwd + '/' + filename, "size": os.path.getsize(filename), "status": "false"}

		def show_file_down(self,url,filename):
			'''
			显示瞎咋的进度条
			:param url: 下载的url
			:param filename: 下载保存的文件
			:return:
			'''
			if self.user and self.passwd:
				r=requests.get(url,auth=HTTPBasicAuth(self.user,self.passwd))
			else:
				r = requests.get(url)
			with open(filename, "wb") as f:
				total_length=len(r.content)
				for chunk in progress.bar(r.iter_content(chunk_size=1024*1024),expected_size=(total_length/1024+1)):
					if chunk:
						f.write(chunk)
				f.close()
			pwd = os.getcwd()
			path = pwd + '/' + filename
			if len(r.content)==os.path.getsize(path):
				return {"path":pwd+'/'+filename,"size":os.path.getsize(filename),"status":"true"}
			else:
				return {"path": pwd + '/' + filename, "size": 
				
				
	####直接下载文件
	def download_file(down_url, filename, user, passwd):
		print(f"开始下载文件{down_url}")
		resp = requests.get(down_url, auth=HTTPBasicAuth(user, passwd), stream=True)
		total = int(resp.headers.get('content-length', 0))
		with open(filename, 'wb') as file, tqdm(
				desc=filename,
				total=total,
				unit='iB',
				unit_scale=True,
				unit_divisor=1024,
		) as bar:
			for data in resp.iter_content(chunk_size=1024):
				size = file.write(data)
				bar.update(size)
		file.close()
		pwd = os.getcwd()
		path = pwd + '/' + filename
		print(f"下载完成，文件路径{path}")
		return path

---