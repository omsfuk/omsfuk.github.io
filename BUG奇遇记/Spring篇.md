# OpenEntityManagerInViewFilter

前天发现使用`OpenEntityManagerInViewFilter`无效，
一顿断点追踪进去，发现生成了两个WebApplicationContext，
并且还有两个EntityManagerFactory，
两个WebApplicationContext可能是因为一些比较奇葩的容器继承，
后者呢？正常的话再怎么搞也不应该吧Bean也给我搞两份吧。。。。

一顿折腾，一度以为Spring Data Jpa自己又搞了一个ApplicationContext

各种加断点，看日志（我TM的2M的日志啊。。。）

后来，问题解决了，web.xml里多了个`ContextLoaderListener`，导致配置文件被加载了两遍，WTM。。。


# ContextLoaderListener
ContextLoaderListener中配置Controller无效
只有在DispatcherServlet中配置的Controller才有效
