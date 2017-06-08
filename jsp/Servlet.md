-----------------------
title: Servlet
tags: jsp,servlet,读书笔记
-----------------------

## Servlet的部署
```
<servlet>
    <servlet-name>AontherServlet</servlet-name>
    <servlet-class>cn.knife037.servlet.AnotherServlet</servlet-class>
</servlet>
<servlet-mapping>
    <servlet-name>AnotherServlet</servlet-name>
    <url-pattern>/AnotherServlet</url-pattern>
<servlet-mapping>
```

## HttpServlet API

#### init
* 给用户一个编写初始化代码的机会。适合耗时较长的初始化处理以提高性能

#### service
* 负责调用对应的doGet()、doPost()方法

#### doGet/doPost
* 开发人员通过重写某个方法来告诉容器支持支持哪个HTTP方法。例如，如果没有重写doPost方法，那就是告诉容器不支持HTTP POST请求。

## 使用GET或POST请求
* POST有body，而GET没有。GET所发送的参数长度受限于请求行。
* POST请求不是幂等的。GET请求是幂等的。
* 超链是GET请求的。表单默认是GET。

## 使用参数
```
getParameter() 获得只有一个值的参数
getParameterValues() 获得具有多个返回值的参数。返回一个字符串数组
getParameterMap() 可以将全部请求的参数名称和值都放到一个Map对象中
```

## 请求头和响应头
```
请求头
getHeader()
getIntHeader()
getHeaderNames()
getHeaderNames()
getContentLength()
响应头
setHeader()
setIntHeader()
setContentType()
setContentLength()
setCharacterEncoding()
```
发送文本类型响应体
```
response.setCharacterEncoding("UTF-8");
response.setHeader("Content-Type","text/html;charset=UTF-8");

PrintWriter out = response.getWriter();

out.println("<html><body>");
out.println("其他HTML");
out.println("</body></html>");
out.flush();
out.close();
```
发送二进制类型响应体
```
request.setCharacterEncoding("UTF-8");
response.setCharacterEncoding("UTF-8");

// 设置文档类型，以便阅读器能够以正确的文档类型来打开
// 应该养成在调用获取输出流对象（即调用getWriter)或getOutputStream()方法）之前，
// 先调用setContentType()方法的习惯。
response.setContentType("application/pdf");
ServletOutputStream os = response.getOutputStream();

while((read=is.read(bytes) != -1) {
os.write(bytes, 0, read);
}

os.flush();
os.close();
```
## 文件上传和下载

## 转发和重定向
* 转发（请求不变） 
用户请求 => 服务器 => 组件1 => 组件2 => …… => 服务器 => 用户
```
RequestDispatcher view = request.getRequestDispatcher("dispatcheresult.jsp");
view.forward(request, response);
```

* 重定向（修改用户请求） 
用户请求 => 服务器 => 组件 => …… => 服务器 => 用户 => 新的请求 
```
sendRedirect(String location)
```
使用此方法来重定向。location可以是绝对URL，也可以是相对URL。 
当含有一个先导/，表示相对当前Servlet容器的根路径 
当含有两个先导/，容器会解释为网络路径引用
当不含有先导/，表示相对当前路径

## Servlet生命周期的几个阶段
Servlet的生命周期包含如下4个阶段： 
1. 加载和实例化。构造函数中一定不要放任何初始化代码，初始化代码只能放在init()中。构造函数构建的仅仅是普通对象，还不是Servlet对象。只有当成为Servlet之后，才会获得Servlet的所有特权。 
2. 初始化 
3. 处理请求。每个请求都在独立的线程中执行。 
4. 服务终止。与init()一样，只能被调用一次。

## "理解Servlet生命周期"对编程的意义
* 不要使用实例变量。容易产生线程安全问题
* 不要使用init()方法获取数据库连接。数据库连接会自动断开。MySQL默认为8h。当然现在有各种框架来解决这个问题
* 数据库连接的数量是有上限的。不能让每个Servlet都获取一个连接。

## 解惑
* download项目中不直接让用户点击一个链接到下载文件的超连接的原因 
如果是物理文件，并且不关心直接暴露文件位置的安全问题，确实可以这么做。 
但是如果是在数据库中，或者需要加一些访问权限，则就需要动态发送了。   
