-----------------------
title: Tomcat问题汇总
tags: tomcat
-----------------------
## 启动慢，访问转圈，无法访问
#### 问题描述
CentOS7运行Tomcat8时启动慢，访问总是转圈，但是过一会又好了

#### 解决方案
MD，花了老子一天，我TM还以为是什么原因呢。
配置从未用过的nginx花了3分钟一次就成功了，反而配置Tomcat花了2小时，MDMDMDMD

* 使用root。。。虽然总感觉老是用root不好，但是用root简单，因为tomcat启动过程中会用到一些权限，这些权限是root的（例如80端口）
* 启用firewall，允许端口访问
* 腾讯云安全组策略
* 启用熵服务（不然TMD有时灵，有时不灵，准确的说99.99%是不灵的。。）
```
yum install rng-tools
systemctl start rngd
cp /usr/lib/systemd/system/rngd.service /etc/systemd/system
vim /etc/systemd/system/rngd.service
修改其中对应的为：ExecStart=/sbin/rngd -f -r /dev/urandom
systemctl daemon-reload
systemctl restart rngd
```

## 如何将Tomcat的根目录设为自己的目录
修改`tomcat/conf/server.xml`文件
在server.xml文件中，有一段如下：

......
```
<Host name="localhost"  appBase="webapps"
           unpackWARs="true" autoDeploy="true">

       <!-- SingleSignOn valve, share authentication between web applications
            Documentation at: /docs/config/valve.html -->
       <!--
       <Valve className="org.apache.catalina.authenticator.SingleSignOn" />
       -->

       <!-- Access log processes all example.
            Documentation at: /docs/config/valve.html
            Note: The pattern used is equivalent to using pattern="common" -->
       <Valve className="org.apache.catalina.valves.AccessLogValve" directory="logs"
              prefix="localhost_access_log." suffix=".txt"
              pattern="%h %l %u %t "%r" %s %b" />
    <Context path="" docBase="D:\apache\apache-tomcat-7.0.26\webapps\OnlineExam">
            <WatchedResource>WEB-INF/web.xml</WatchedResource>
    </Context>

     </Host>
```

在`value`标签之后添加上：

`<Context path="" docBase="D:\apache- tomcat-7.0.34\webapps\myjsp" debug="0" reloadable="true" />`

`<Host>`元素代表虚拟主机，在同一个`<Engine>`元素下可以配置多个虚拟主机

`path`:指定该Web应用的URL入口

`docBase`:指定Web应用的文件路径，可以给定绝对路径，也可以给定相对于`<Host>`的`appBase`属性的相对路径。如果Web应用采用开放目录结构，则指定Web应用的跟目录；如果Web应用是个WAR文件，则指定WAR文件的路径
`workDir`:指定Web应用的工作目录。Tomcat在运行时会与这个Web应用相关的临时文件放在次目录下

`reloadable`: 如果这个属性设为true，Tomcat服务器在运行状态下会监视在`WEB-INF/classes`和`WEB-INF/lib`目录下class文件的改 动，以及监视Web应用的`WEB-INF/web.xml`文件的改动。如果监测到有class文件被更新的，服务器会自动重新加载Web应用。该属性的默 认值为false。在Web应用的开发和调试阶段，把`reloadable`设为true，可以方便对Web应用的调试。在Web应用正式发布阶段，把 `reloadable`设为false，可以降低Tomcat的运行负荷，提高Tomcat的运行性能
