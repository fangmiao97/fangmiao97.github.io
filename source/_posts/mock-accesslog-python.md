---
title: 用户行为日志记录方案设计
date: 2019/04/22
categories:
    - 技术总结
tags:
    - 毕业设计
---
模拟用户访问的Python脚本

* generate_log.py

```python
#coding=UTF-8

import random
import time

url_paths = {
	"class/112.html":10,
	"class/128.html":5,
	"class/145.html":8,
	"class/146.html":10,
	"class/131.html":3,
	"class/130.html":2,
	"learn/821":1,
	"course/list":1
}

ip_slices = [132,156,124,10,29,167,143,187,30,46,55,63,72,87,98,168]

http_referers = [
	"http://www.baidu.com/s?wd={query}",
	"https://www.sogou.com/web?query={query}",
	"http://cn.bing.com/search?q={query}",
	"https://search.yahoo.com/search?p={query}",
]

search_keyword = [
	"新近发布",
	"为你推荐",
	"今日歌单",
	"瞩目艺人",
	"今日专辑"
]

status_codes = ["200","404","500"]

def sample_url():
	all_data = []
	for v, w in url_paths.items():
		temp = []
		for i in range(w):
			temp.append(v)
		all_data.extend(temp)

	return random.sample(all_data, 1)[0]

def sample_ip():
	slice = random.sample(ip_slices , 4)
	return ".".join([str(item) for item in slice])

def sample_referer():
	if random.uniform(0, 1) > 0.2:
		return "-"

	refer_str = random.sample(http_referers, 1)
	query_str = random.sample(search_keyword, 1)
	return refer_str[0].format(query=query_str[0])

def sample_status_code():
	return random.sample(status_codes, 1)[0]

def generate_log():
	
	time_str = time.strftime("%Y-%m-%d %H:%M:%S", time.localtime())
	now_hour = time.strftime("%H", time.localtime())
	
	if now_hour < "01":
		count = random.randint(80,100)
	elif now_hour < "02":
		count = random.randint(60,85)
	elif now_hour < "03":
		count = random.randint(50,75)
	elif now_hour < "05":
		count = random.randint(30,60)
	elif now_hour < "06":
		count = random.randint(50,65)
	elif now_hour < "08":
		count = random.randint(70,85)
	elif now_hour < "10":
		count = random.randint(70,95)
	elif now_hour < "11":
		count = random.randint(80,100)
	elif now_hour < "13":
		count = random.randint(95,150)
	elif now_hour < "15":
		count = random.randint(90,130)
	elif now_hour < "17":
		count = random.randint(80,120)
	elif now_hour < "18":
		count = random.randint(85,130)	
	elif now_hour < "19":
		count = random.randint(100,120)
	else:
		count = random.randint(110,160)	

	f = open("/home/hadoop/data/project/logs/access.log","w+")

	while count >= 1:
		query_log = "{ip}\t{local_time}\t\"GET /{url} HTTP/1.1\"\t{status_code}\t{referer}".format(url=sample_url(), ip=sample_ip(), referer=sample_referer(), status_code=sample_status_code(),local_time=time_str)

		f.write(query_log + "\n")

		count = count - 1 

if __name__ == '__main__':
	generate_log()
	
```

* 定时执行脚本
    * Linux crontab
    * [工具网站](https://tool.lu/crontab/)