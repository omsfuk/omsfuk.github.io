---------------------
title: Property配置文件
tags: 杂记
---------------------

## .property概述
.property 是一个配置文件类型，能够保存一些自定义的配置信息。例如线程数，数据库的连接参数、用户名、密码、等等。
很多语言都有自己支持的配置文件。所谓的支持是在核心代码中已经内置了对配置文件的支持。例如C#的app.config（实际上是XML），python的ini。。。至于.property则是java支持的配置文件类型。
## .property的格式
.property 是以键值对（key-value）的形式保存的。
```
# 以下为服务器、数据库信息
dbPort = localhost
databaseName = mydb
dbUserName = root
dbPassword = root
# 以下为数据库表信息
dbTable = mytable
# 以下为服务器信息
ip = 192.168.0.9
```

## 利用java对.property进行操作
JDK 中的 Properties 类 Properties 类存在于包 Java.util 中，该类继承自 Hashtable ，它提供了几个主要的方法：

* getProperty ( String key) ， 用指定的键在此属性列表中搜索属性。也就是通过参数 key ，得到 key 所对应的 value。 
* load ( InputStream inStream) ，从输入流中读取属性列表（键和元素对）。通过对指定的文件（比如说上面的 test.properties 文件）进行装载来获取该文件中的所有键 - 值对。以供 getProperty ( String key) 来搜索。 
* setProperty ( String key, String value) ，调用 Hashtable 的方法 put 。他通过调用基类的put方法来设置 键 - 值对。 
* store ( OutputStream out, String comments) ， 以适合使用 load 方法加载到 Properties 表中的格式，将此 Properties 表中的属性列表（键和元素对）写入输 
出流。与 load 方法相反，该方法将键 - 值对写入到指定的文件中去。 
5． clear () ，清除所有装载的 键 - 值对。该方法在基类中提供。 
