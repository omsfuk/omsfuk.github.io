-----------------------
title: CentOS基本环境配置
tags: centos,linux
-----------------------

# CentOS基本环境配置

## xftp

* 先用`rpm -qa | grep vsftpd`测试下看看是不是装了FTP服务

* 要是没有的话，安装并启动ftp服务
```
yum -y install vsftpd
chkconfig vsftpd on
```

* 然后可以用xftp连接了，注意，要选sftp，端口22

## xshell
直接在windows上下载安装xshell，输入地址、用户名、密码就可以连接了。CentOS默认安装了SSH服务，所以CentOS不需要配置（最起码CentOS7是这样的）。

## JDK
openJDK感觉慢一点，果断入oracle jdk

* 下载rpm，我下的 1.8

* `rpm -ivh jdk-8.rpm` 我用sodoer身份竟然安装失败了，然后切换到root，安装成功

* 设置环境变量，在`/etc/profile`中添加如下内容
```
JAVA_HOME=/usr/java/jdk1.7.0_79
JRE_HOME=/usr/java/jdk1.7.0_79/jre
CLASS_PATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:$JRE_HOME/lib
PATH=$PATH:$JAVA_HOME/bin:$JRE_HOME/bin
export JAVA_HOME JRE_HOME CLASS_PATH PATH
```

* 更新`source /etc/profile`

## mysql
* 下载`mysql-5.7.9-1.el7.x86_64.rpm-bundle.tar.gz`

* 只需要安装
```
mysql-community-common-5.7.9-1.el7.x86_64.rpm
mysql-community-libs-5.7.9-1.el7.x86_64.rpm --（依赖于common）
mysql-community-client-5.7.9-1.el7.x86_64.rpm --（依赖于libs）
mysql-community-server-5.7.9-1.el7.x86_64.rpm --（依赖于client、common）
```

* 如果报错

```
libnuma.so.1()(64bit) is needed by mysql-community-server-5.7.17-1.el7.x86_64
    libnuma.so.1(libnuma_1.1)(64bit) is needed by mysql-community-server-5.7.17-1.el7.x86_64
    libnuma.so.1(libnuma_1.2)(64bit) is needed by mysql-community-server-5.7.17-1.el7.x86_64

```
安装`numactl`即可`yum install numactl`


## 注意事项
CentOS4 采用`firewall`防火墙（以前使用`iptables`）。因此要想外网访问一定要打开端口。
* 查询9200端口是否打开
`firewall-cmd --query-port=9200/tcp`
返回yes 表示打开
* 打开9300端口
`firewall-cmd --add-port=9300/tcp`
返回success表示打开成功

* 启动防火墙
`systemctl start firewalld.service`

* 停止防火墙
`systemctl stop firewalld.service`

* 查看防火墙状态
`systemctl status firewalld.service`


## CentOS下的服务管理
* 启动一个服务：
`systemctl start firewalld.service`
* 关闭一个服务：
`systemctl stop firewalld.service`
* 重启一个服务：
`systemctl restart firewalld.service`
* 显示一个服务的状态：
`systemctl status firewalld.service`
* 在开机时启用一个服务：
`systemctl enable firewalld.service`
* 在开机时禁用一个服务：
`systemctl disable firewalld.service`
* 查看服务是否开机启动：
`systemctl is-enabled firewalld.service`
* 查看已启动的服务列表：
`systemctl list-unit-files|grep enabled`

## 改变文件编码
#### vim
* 设置读取编码
`:set encoding=utf8`

* 改变编码格式
`:set fileencoding=utf8`
