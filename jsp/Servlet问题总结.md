-----------------------
title: Servlet问题总结
tags: java,servlet
-----------------------
# Serlvet问题总结
## `/*`与`/`的区别
* `/`和`/*`均会拦截所有路径的请求，无论那一层目录
* `/*`是拦截所有请求，包括静态资源
* 剩下的是`/`

## ContextPath、Servlet Path、Path Info
重要的是要注意，除了请求 URI 和路径部分的 URL 编码差异外，下面的等式永远为真：
`requestURI = contextPath + servletPath + pathInfo`

假设servlet的webservlet配置为`/index/*`，
则`Servlet Path`为`index`，
`Pathinfo`为`*`部分

## Http Only Cookie
Cookie都是通过document对象获取的，我们如果能让cookie在浏览器中不可见就有效阻止XSS攻击，那HttpOnly就是在设置cookie时接受这样一个参数，一旦被设置，在浏览器的document对象中就看不到cookie了。而浏览器在浏览网页的时候不受任何影响，因为Cookie会被放在浏览器头中发送出去(包括Ajax的时候)，应用程序也一般不会在JS里操作这些敏感Cookie的，对于一些敏感的Cookie我们采用HttpOnly，对于一些需要在应用程序中用JS操作的cookie我们就不予设置，这样就保障了Cookie信息的安全也保证了应用。

## servlet中的url匹配
* 以‘ /’字符开始、以‘ /*’后缀结尾的字符串用于路径匹配。
* 以‘ *.’开始的字符串用于扩展名映射。
* 空字符串“”是一个特殊的 URL 模式， 其精确映射到应用的上下文根，即， `http://host:port/<context-root>/`
请求形式。在这种情况下，路径信息是‘ /’且 servlet 路径和上下文路径是空字符串（“”）。
* 只包含“ /”字符的字符串表示应用的“ default” servlet。在这种情况下， servlet 路径是请求 URL 减去上下文路径且路径信息是 null

## 配置application参数，例如提供一个名为rootURL的全局字符串常量

1. 在web.xml中

```
<context-param>
    <param-name>rootURL</param-name>
    <param-value>/Blog</param-value>
</context-param>
```

2.在JSP中访问

```
<%=application.getInitParameter("rootURL") %>
```


貌似解决的不是很优雅（当然也可以将解决国际化的方法拿来使用，总感觉更不合适）
