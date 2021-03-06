---------------------
title: 服务器、Apache、Tomcat、Servlet、JSP概念解析
tags: 杂记
---------------------

以一次JSP请求响应为例，讲解服务器，Apache、Tomcat之间的关系。

* 带着这样一个概念去看：**Apache与Apache Tomcat（简称Tomcat）都是可以独立运行的服务器**。你平时见到的`apache-tomcat-7.0.72`实际上只是Tomcat服务器。**此处整合了Apache和Apache Tomcat**。Apache用来处理静态资源，减少Tomcat的服务开销。

## 先来个热身
一个请求到发出到响应返回的全过程
![HTTP请求到响应](http://img.blog.csdn.net/20170326185645738?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMDAwNTQyNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

* 首先，客户端发出一个请求
看到浏览器上的URL地址了么，那就是你的请求的主要内容。请求以一串文本的形式发送，例如`http://www.baidu.com`代表按照http协议获取`www.baidu.com`这个资源（关于资源的概念请参见URI和URL）。当然实际上你的请求不是这么写的，这么写只是为了让你有一个直观的感受，具体请参见HTTP协议

* 第二步，服务器（硬件）收到这个请求，也就是那一串文本。
服务器的概念之一就是一台电脑。你提交的请求会被发送到这台电脑上。
显然啊，服务器就是一台电脑，没有软件的话它是无法识别请求并做出响应的

* 第三步，服务器（硬件）将请求交付给服务器（软件，例如Apache就是一个常用的服务器软件）。
假设请求的是一个动态页面（动态页面就是需要动态生成的页面，例如QQ空间，每人每时每刻都不同，需要动态生成），在本例中是JSP，那么apache则会将请求继续转给下一个部分

* 第四步，服务器（软件）将请求交给Tomcat
因为Apache本身不提供动态页面访问能力的，所以对jsp的支持要靠其他的东西来实现，这个东西类似于插件（但严格来说Tomcat并不是Apache的一个插件）。在本例中，这个插件就是Tomcat。**要注意：Tomcat本身也是可以单独作为服务器来运行的。Tomcat 有时也被称为Apache Tomcat，这个Apache指的并不是Apache服务器，而是一个“商标”，因为Tomcat是属于Apache这个项目的**
Tomcat是一个运行环境，或者叫容器。Tomcat负责实例化jsp并且处理请求（通俗的讲就是“运行”jsp，搞到对应的响应信息），并且返回一个响应到apache，apache则继续将响应返回到客户端。

* 至此，一个响应完成

## 热身，again
以上内容说白了就是输入和输出，给定一个输入，产生一个输出。根据这个，我们来模拟下web的进化史。

* 最初，人们的需求很低，只要求能够有静态页面就好了。
举个例子，例如要做一个公告网页，上面公示每天的重要通知。怎么做呢？好办啊！
我们用一个简化的输入输出来表示，只要你输入“公告”，我就输出“2017-3-26 XXX写下了第一篇关于几个概念的理解的博客”，不要想得太多，就是一个标准输入，标准输入。
用Java写很简单，写成一个控制台标准输入输出就好了。用伪代码表示如下，
```
if（输入=“公告”） then 
    print “2017-3-26 XXX写下了一篇关于几个概念的理解的博客”
```
这就是满足了需求，就这么简单，然后将他改成网络IO就好了。

* 要是公告变了怎么办？好办，将`print “2017-3-26 XXX写下了第一篇关于几个概念的理解的博客”`该掉就好了。也就是说，只要信息变动，你就要重写消息源，改动源代码。很蠢是吧。这还好说，要是生成的像QQ空间那样的页面怎么搞？好办！套模板啊。一个页面变动的只是少数，有很大一部分是不变的。想填空题一样，将空填起来就好了，像这样

```
date = "2017-3-26"
name = "一只准程序员"
about = "几个概念的理解"
if（输入=“公告”） then 
    print date + name + “写下了一篇关于" + about + "的博客”
```
这几个变量完全可以从数据库中自动获取。也就是说，只要更新数据库，然后页面就会自动更新了。虽然还是很蠢，但是动态页面就是这个原理。

（你写JSP不就是填空吗，在模板上填空。`${name}`这样的不就是“空”吗）

* 总结下，Apache负责请求转发&&静态页面处理，Tomcat来处理动态页面解析

## 现在，进入正题

### 概念阐述
借助以上过程和概念，来谈一谈服务器、Apache、Tomcat之间的关系

* 服务器
经过以上过程，这个概念很熟了吧，有两个主要概念
 * 硬件：一台电脑。这台电脑上运行着至少一个服务器软件，能够处理请求
 * 软件：一个提供网页的服务器程序。处理请求并返回响应
* Apache
Apache就是一个服务器（软件），提供了静态页面的访问能力。它配置上插件（模块）后可以以各种语言实现动态页面。（但是一定要注意Tomcat并不是Apache的插件）
* Tomcat
Apache Tomcat（即平时所说的Tomcat），也是一个服务器。在这个案例中，Tomcat充当了一个插件的作用，这个插件是针对于Java的，或者说是jsp/servlet的。它是一个容器。何为容器？容器么，就是容器。容的是什么，JSP、Servlet。容器管理jsp、servlet的声明周期，包括创建，运行，销毁。它是一个jsp、servlet的运行环境。通俗点说，请求发过来，Tomcat负责执行JSP、Servlet，并返回结果。关于生命周期这东西，在此不做阐述。也就是说，没有Tomcat，Apache是不认你JSP的。懂设计模式的可以想想了。。。当然，整个过程都有设计模式的影子


### Apache与Tomcat比较联系

* apache支持静态页，tomcat支持动态的，比如servlet等。

* 一般使用apache+tomcat的话，apache只是作为一个转发，对jsp的处理是由tomcat来处理的。

* apache可以支持php\cgi\perl,但是要使用java的话，你需要tomcat在apache后台支撑，将java请求由apache转发给tomcat处理。

* apache是web服务器,Tomcat是应用（java）服务器，它只是一个servlet(jsp也翻译成servlet)容器，可以认为是apache的扩展，但是可以独立于**apache**运行。

* 共同点
 1. 两者都是apache组织开发的
 2. 两者都有HTTP服务的功能
 3. 两者都是免费的
* 不同点：
 1. Apache是专门用了提供HTTP服务的，以及相关配置的（例如虚拟主机、URL转发等等）
 2. Tomcat是Apache组织在符合J2EE的JSP、Servlet标准下开发的一个JSP服务器


第一次写博客，如有错误还请不吝赐教

OK, that's all
