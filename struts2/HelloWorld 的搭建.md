-----------------------
title: Struts 2 之 Hello World
tags: Struts2
-----------------------

# 所需软件
* eclipse mars
* jdk 1.8
* struts 2.3.31
* apache 7.0

# 开始搭建
## 建立 Dynamic Web Project，名为HelloWorld
* apache 7.0
* 选中`generate web.xml`

## 配置web.xml
添加如下一段

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns="http://java.sun.com/xml/ns/javaee"
    xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd"
    id="WebApp_ID" version="3.0">
    <display-name>HelloWorld</display-name>
    <welcome-file-list>
        <welcome-file>index.html</welcome-file>
        <welcome-file>index.htm</welcome-file>
        <welcome-file>index.jsp</welcome-file>
        <welcome-file>default.html</welcome-file>
        <welcome-file>default.htm</welcome-file>
        <welcome-file>default.jsp</welcome-file>
    </welcome-file-list>

    <display-name>StrutsDemo</display-name>
    <filter>
        <filter-name>struts2</filter-name>
        <filter-class>org.apache.struts2.dispatcher.ng.filter.StrutsPrepareAndExecuteFilter</filter-class>
    </filter>
    <filter-mapping>
        <filter-name>struts2</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
</web-app>
```
## 在WebContent目录下添加Hello.jsp和HelloWorld.jsp
```jsp
Hello.jsp
<%@ page language="java" contentType="text/html; charset=gbk"
    pageEncoding="ISO-8859-1"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=gbk">
<title>Hello</title>
</head>
<body>
<center>
    <h3><a href="hello">Go to HelloWorld.jsp</a></h3>
</center>
</body>
</html>

HelloWorld.jsp
<%@ page language="java" contentType="text/html; charset=gbk"
    pageEncoding="ISO-8859-1"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=gbk">
<title>HelloWorld</title>
</head>
<body>
    <center>
        <h2>Success</h2>
    </center>
</body>
</html>
```
## 导入jar包
需要导入以下jar包，千万不要多导入
* commons-fileupload
* commons-io
* commons-lang3
* commons-logging
* freemarker
* javassist
* ognl
* struts2-core
* xwork-core

## 在`src`目录下建立`cn.knife037.action`包，并添加一个`HelloAction`类

```java
import com.opensymphony.xwork2.ActionSupport;

public class HelloAction extends ActionSupport {
    
    @Override
    public String execute() throws Exception {
        return SUCCESS;
    }
}
```

## 在src目录下创建struts.xml
在其中添加如下代码
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE struts PUBLIC
"-//Apache Software Foundation//DTD Struts Configuration 2.3//EN"
"http://struts.apache.org/dtds/struts-2.3.dtd">

<struts>
    <package name="default" namespace="/" extends="struts-default">
        <action name="hello" class="cn.knife037.action.HelloAction">
            <result name="success">/HelloWorld.jsp</result>
        </action>
    </package>
</struts>
```
## 启动Apache，在浏览器中输入localhost:8080/HelloWorld/Hello.jsp

# 遇到的问题

* struts.xml位置放错。
开始以为struts.xml要放在classes目录下，结果老是出404

* 拦截器名写错
这个是最坑的
struts的拦截器名改了好多次。
  * struts2.3 应该这么写
  ```
  <filter-class>org.apache.struts2.dispatcher.ng.filter.StrutsPrepareAndExecuteFilter</filter-class>
  ```
  * struts2.0 应该这么写
  ```
  <filter-class>org.apache.struts2.dispatcher.FilterDispatcher</filter-class>
  ```
  * struts2.5 应该这么写
  ```
  <filter-class>org.apache.struts2.dispatcher.filter.StrutsPrepareAndExecuteFilter</filter-class>
  ```
* 另外，jar包不要多放，放多了会导致问题，我的会出现404，具体原因暂时不懂。懂的时候会来更新
* struts2.3以前的版本不要用了。之前的版本出了很大的安全漏洞
