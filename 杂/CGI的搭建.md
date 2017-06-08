-----------------------
title: CGI的搭建
tags: cgi
-----------------------
> # 使用CGI实现网页交互

## 预备
CGI程序可以用任何脚本语言或者是完全独立编程语言实现，只要这个语言可以在这个系统上运行。
此案例用Python实现
虽然CGI性能极差，而且还有安全漏洞。平时用来做一些小网页（例如课设作业。。）还是不错的

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


print 'content-type:text/html'
print    # 空行，告诉服务器结束头部
print '<html>'
print '<head>'
print '<meta charset="utf-8">'
print '<title>Hello World</title>'
print '</head>'
print '<body>'
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
