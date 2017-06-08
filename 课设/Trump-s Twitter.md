-----------------------
title: 用Python爬取特朗普的Twitter
tags: python,爬虫
-----------------------

> # Trump's Twitter爬取

## 需求分析
* 对Trump的Twitter进行爬取，以查看Trump的最新动态
* 将爬取的内容呈现在网页上

## 设计

* 用Python来抓取Trump‘Twitter，用正则表达式来解析网页
* 用CGI来搭建一个小型网页。虽然CGI效率低下，但是开发简单。
* 随用户每访问进行更新

## 实现
#### 1. 安装Python2.7
#### 2. Windows下Apache的获取与配置
* Apache2 for Windows 的获取
  * 方法一 ： 目前Apache官网只提供源代码，所以可自行编译后使用
  * 方法二 ： 直接去Apache Haus下载已编译好的Windows版本
* 安装
  * 找到bin目录，运行`httpd -k install`。若出现`No Installed Service...`，则需要去conf目录下将httpd.conf的`Listen 80`改为`Listen 5678`，然后继续`httpd -k install`
  
  
* 配置`httpd.conf`
  找到
  
```
  <Directory "/Apache22/cgi-bin">
    AllowOverride None
    Options ExecCGI
    Order allow,deny
    Allow from all
</Directory>
```
  
  这么一段，改为
```
  <Directory "/Apache22/cgi-bin"> #存放交互程序或脚本的目录，此目录可以改，但是下面脚本的放置位置也需要改
    AllowOverride None
    Options ExecCGI
    Order allow,deny
    Allow from all
</Directory>
AddHandler cgi-script .cgi .py .exe
```
  
  
  
* 编写Python代码match.py
* 首行为python.exe目录，不同的人可能会有不同目录
  
```
#!C:/python27/python.exe
# -*- coding: UTF-8 -*-

import urllib2
import re


print 'content-type:text/html'
print    # 空行，告诉服务器结束头部
print '<html>'
print '<head>'
print '<meta charset="utf-8">'
print '<title>Twtter trump</title>'
print '</head>'
print '<body>'

req = urllib2.Request('https://twitter.com/realdonaldtrump')
try:
        response = urllib2.urlopen(req)
except urllib2.URLError, e:
        print e.code
txt = response.read()

ptn = re.compile('div class=\"js-tweet-text-container(.*?)0\">(.*?)</p>', re.M|re.S)
ans = ptn.findall(txt)
for a in ans:
    print a[1]
    print '</br>'
print '</body>'
print '</html>'

```

* 启动
  * 运行`httpd -k start`
  * 访问`localhost:5678/cgi-bin/match.py`
  
  
#### 2.CentOS下apache的获取与配置
* Apache获取
在CentOS下直接`sudo yum install httpd`就好了
* 配置
其中httpd.conf在/etc/httpd/目录下
默认cgi目录是/var/www/cgi-bin/
* 其余基本和windows一样，就是目录有变化，可用`whereis python`来查找Python路径

## 效果展示
已配置到个人服务器上，
![](https://github.com/Knife037/Images/blob/master/twitter_crawl.png?raw=true)

## 总结

* 界面比较low
* 采用正则表达式来抓取，要抓取复杂的信息是不如使用soup之类的模块简单的。
* 没有将采用Ajax动态刷新的页面（就是只有下拉才会一步步呈现的内容）也获取
* 没有采用Twitter API来进行开发，Twitter API有访问次数限制。当然，采用这种简易爬虫的话也可能被封禁（主要是简单啊-V-）
* 貌似也没什么优点。。。唯一的优点是没有做成APP，所以无论那种客户端后可以访问。
* Python确实容易上手，从入门到配置完本案例只花了两小时。Python功能强大，代码简洁。用Java的话估计代码量至少要翻一番。
* 久仰CGI大名（凡是网络编程的书都要提到的古老的Web解决方案），看老师的飞花令的网页版url中有`cgi-bin`，突然想到的，然后在本案例中试了下。。确实比搭一个框架简单多了。
