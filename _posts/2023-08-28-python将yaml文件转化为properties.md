---
layout:     post
title:      python常用功能
subtitle:   yaml文件转为properties文件
date:       2023-08-28
author:     eddie
header-img: img/home-bg-o.jpg
catalog: true
tags:
    - python
---

	def YmlToProper(infile,oufile):
		'''
		describe :将yaml文件转化为propertise
		infile:需要转化的yaml文件
		outfile:转化后的propertise文件
		'''
		
		formatted = ''
		prop = []
		output = ''
		array_object = False
		
		if not os.path.isfile(infile):
		   raise ValueError(infile + ' is not file') 
			
		
		with open(infile,'+r',encoding='utf-8') as f:
			lines = f.readlines()
		
		for line in lines:
			ignore = re.search(r'^\s*#', line)    #\s 匹配不可见字符，空格 tab
			array_line = re.search(r'^\s*-\s', line) is not None  #
			
			
			if not array_line:
				array_index = -1

			if ignore or not line.strip() or not ':' in line and not array_line:
				output += '\n' if len(output) else ''
				continue

			tabs = re.findall(r'(' + (TAB_SIZE * '\s') + ')', line.split(':')[0])

			index = len(tabs) if tabs else 0

			result_prop = re.search(r'.+(?=:\s)', line)

			if index == 0:
				prop = []
				prop.append(result_prop.group().strip())
			else:
				if array_line:
					array_index += 1
				else:
					prop_name = result_prop.group(0).strip()

				while array_index < 0 and prop and index < len(prop):
					prop.pop()

				if array_index < 0:
					prop.append(prop_name)

			value = re.search(r'(?<=:).+', line) if array_index < 0 else re.search(r'(?<=-\s).+', line)

			if value and value.group().strip():
				p = '%(key)s%(idx)s%(spc)s=%(spc)s%(value)s\n' % {
					'key': '.'.join(prop),
					'idx': ('[%d]' % array_index) if array_index >= 0 else '',
					'spc': ' ' if USE_SPACES else '',
					'value': value.group().strip()
				}
				output += p

		# Write file
		file_props = open(oufile,'w+')
		file_props.write(re.sub(r'\n\n\n+', '\n\n', output))
		file_props.close()

---