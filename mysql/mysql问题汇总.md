-----------------------
title: MySQL问题汇总
tags: mysql
-----------------------

## 修改数据库默认编码
#### 问题描述
使用locale命令看到的是LANG=C，，，毛线
因为系统开始时就莫名其妙的分配了错误的编码，导致mysql也乱了，具体表现就是无法识别utf-8。
#### 解决方案
1. 更改/etc/locale.conf改为LANG=zh_CN.UTF-8
2. 修改mysql默认编码
打开`/etc/my.cnf`，在mysqld下添加`character-set-server=utf8。不要多写，写错了会导致mysql启动不了
4. 然后重启mysql
`service mysqld restart`

## 数据库连接超时问题
#### 问题描述
Novel网站时间久了不登录后，首次登录会出现505，再次登录就好了。
#### 解决方案
因为mybatis使用了数据库连接池，所以连接是不会自动关闭的。
mysql的默认超时时间是28800，也就是8小时。
可通过`show global variables like '%timeout%'`查看
设置超时时间`set global wait_timeout=200000000`，实际上最大只能取到31536000，也就是365天。

感觉365天应该够了，如果存在这种极端情况，这代表这网站几乎废了（实际上8小时也算废了）。为了解决这种极端情况。可以进一步对mybatis中进行配置。
在
```xml

<environment id="product">
            <transactionManager type="JDBC" />
            <dataSource type="POOLED">
                    <property name="driver" value="com.mysql.jdbc.Driver"/>
                    <property name="url" value="jdbc:mysql://localhost:3306/prometheus?useUnicode=true&amp;characterEncoding=utf8" />
                    <property name="username" value="root"/>
                    <property name="password" value="zhiyisoft"/>
            </dataSource>
    </environment>
```
中增加
```xml
<property name="poolPingEnabled" value="true"/>
<property name="poolPingQuery" value="select 10000 as salary"/>
<property name="poolPingConnectionsNotUsedFor" value="14400000"/>
```
poolPingQuery 是用来检查连接状态的SQL语句，随意写。。只要能正常执行。设置为大约半年Ping一次，感觉应该就不错了。


## 开放数据库远程连接
```
UPDATE user SET host = '%' WHERE user = 'root'; # 更新允许登录的ip
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'root' WITH GRANT OPTION # 赋予远程登录权限
FLUSH PRIVILEGES # 刷新权限
```
可以在客户端通过`mysql -h omsfuk.com -uroot -proot`连接
