# Redis

## 简介
Redis是一种基于键值对（key-value）的NoSQL数据库。

与很多键值对数据库不同的是，Redis中的值可以是由string、hash、list、zset、Bitmaps、HyperLogLog、GEO等多种数据结构和算法组成，因此
#### 特性
* 速度快
	Redis的所有数据都是存放在内存中的。
	Redis是采用C语言实现的。
	Redis使用了单线程架构，预防了多线程可能产生的竞争问题。
	作者对于Redis源代码可以说是精打细磨，曾有人评价说Redis是少有的集性能和优雅于一身的开源代码。
* 基于键值对的数据结构服务器
	Redis的全称是Remote Dictionary Server，它主要提供了五种数据结构：字符串，哈希，列表，集合，有序集合，同时在字符串的基础上演变出了位图（Bitmaps）和HyperLogLog两种神奇的“数据结构”，并且随着LBS（Location Based Service， 基于位置服务）的不断发展，Redis3.2 版本中加入有关GEO的功能

* 丰富的功能
提供了键过期功能，可以用来实现缓存
提供了发布订阅功能，可以用来实现消息系统
支持lua脚本功能，可以利用lua创造出新的Redis命令
提供了简单的事务功能，能在一定程度上保证事务特性
提供了流水线功能，这样客户端能将一批命令一次性传到Redis，减少了网络的开销

* 简单稳定
* 客户端语言多
* 持久化
* 主从复制
* 高可用和分布式

#### 使用场景
* 缓存
* 排行榜系统
* 计数器应用
* 社交应用
* 消息队列系统

## 入门
#### 安装
CetnOS
```
yum install redis.x86_64
```
搞定，就这么简单

#### 运行
###### 启动
redis的启动方式有三种，
第一种，默认配置
```
redis-server # 此种方式会以前台方式运行
```
第二种，命令行中指定参数
```
redis-server --daemonize yes # 设置为守护进程，以后台方式运行
```
第三种，配置文件启动
有时候参数太多，所以就需要写到配置文件中，让redis从配置文件中读取启动参数
```
redis-server /opt/redis.conf
```

####  交互
有两种与redis交互的方式，
第一种是通过交互式命令行，
```
redis-cli -h {host} -p {port}
```
也可以直接输入
```
redis-cli
```
第二种是通过单条命令行的方式
```
redis-cli -h 127.0.0.1 -p 6379 get hello
```

#### 停止
```
redis-cli shutdown
```
关闭redis时，会发生如下操作：断开与客户端的连接，持久化文件生成，是一种相对优雅的关闭方式
当然，你也可以通过kill强制关闭，不过这就属于不优雅的关闭方式了
可以取消生成持久化文件
```
redis-cli shutdown nosave
```
