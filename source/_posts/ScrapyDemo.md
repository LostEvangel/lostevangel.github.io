---
title: ScrapyDemo
date: 2017-11-30 12:20:49
tags: [Python, scrapy]
categories: 爬虫
---


### 建立工程

>scrapy startproject scrapydemo

### 目录分析

- scrapy.cfg: 项目的配置文件
- scrapytest/: 该项目的python模块。之后您将在此加入代码。
- scrapytest/items.py: 项目中的item文件.
- scrapytest/pipelines.py: 项目中的pipelines文件.
- scrapytest/settings.py: 项目的设置文件.
- scrapytest/spiders/: 放置spider代码的目录.

### Tips

#### 1. spider name

/spiders/目录下创建一个文件
-name: 用于区别Spider。 该名字必须是唯一的，您不可以为不同的Spider设定相同的名字。

#### 2.多级抓取 
scrapy 在不同的抓取级别的Request之间传递参数的办法，下面的范例中，parse()通过meat传递给了parse_more()参数item，这样就可以再parse_more()抓取完成所有的数据后一次返
```python
def parse(self, response):
	……
	item['url'] = url.extract()[0]
	for item in items:
		yield Request(url = item['url'],meta = {'item':item}, callback = self.parse_more,dont_filter = True)
def parse_more(self,response):
	item = response.meta['item']
	……
	yield item
```
### 总结需要修改demo的地方

demo地址:https://github.com/CherryKeinz/ScrapyDemo

#### 1. items.py

修改类demoItem()里需要的字段


#### 2. /spiders/MySpider.py

import 要改为items.py中定义的类名
from SpiderDemo.items import demoItem

- allowed_domains = ["http://10.3.210.84:4237/home/"]
这个是 spider要求设置的一个变量,表示允许爬虫爬的网站
- start_urls.append(url) url改为要爬的网址
- 如需保存为JSON，修改custom_settings

#### 3.pipelines.py

如需要，修改类JsonWithEncodingPipeline

### 常见错误

1. scrapy 报错 no module named win32api 的解决方案
原因是缺少win32,到 http://sourceforge.net/projects/pywin32/files/
找到对应的版本进行下载，直接安装即可
