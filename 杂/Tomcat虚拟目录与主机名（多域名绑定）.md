---------------------
title: Tomcat虚拟目录与主机名
tags: 杂记
---------------------

## 虚拟目录的 Why&& How
#### 场景
* 你写了个webApp，项目名叫`ASimpleWebApp`，然后你访问的话，需要敲`localhost:8080/ASimpleWebApp`，好麻烦是吧，名字好长。

#### 对策
* 第一种方法，最简单的方法，你可以将`webapps`目录下的`ASimpleWebApp`文件夹改个简单名，
* 第二种，高明的方法（或许高明一点），直接对Tomcat说：以后你将对`localhost:8080/A`的访问全都映射到ASimpleWebApp目录下。怎么实现呢？
 在`<host></host>`标签中加入（假设ASimpleWebApp所在的目录是`C:\apache-tomcat-7\webapps\ASimpleWebApp`)
```
 <Context path="/A" docBase="D:\test" reloadable="true"></Context>
 ```
**注意，这个ASimpleWebApp文件夹是可以随便放的，也就是说不一定要放在Tomcat的webapps目录下**
也就是说你完全可以将ASimpleWebApp文件夹放到D盘，然后改为
```
 <Context path="/A" docBase="D:\ASimpleWebApp" reloadable="true"></Context>
```

#### 总结

* 仔细推敲下`虚拟目录`这个词，你会发现它的用途
> 虚拟目录就是将其他目录以映射的方式虚拟到该FTP服务器的主目录下，这样，一个FTP服务器的主目录实质上就可以包括很多不同盘符、不同路径的目录，而不会受到所在盘空间的限制了。
* 还有一种比较好玩的玩法，例如我可以将`localhost:8080`直接映射到一个webapp，以后，只要敲上`localhost:8080`就可以直接访问那个webapp了，然后改下端口，`8080`也可以去掉，server.xml下有一个
```
<Connector port="8080" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443" />
```
将8080改成80，然后以后直接敲localhost就可以了


## 主机名的 Why 与 How
#### 场景
* 只有一台服务器，上边运行着两个webapp：Blog和Novel。访问不同app要敲`www.kitrst.cn/Novel`或`www.kitrst.cn/Blog`。没感觉这种url比较“土”吗？
* 需求来了：（假设你有两个域名，并且都映射到这个服务器的IP上）
 * 要将`novel.kitrst.cn`映射到一个名为Novel的webapp
 * 要将`blog.kitrst.cn`映射到一个名为Blog的webapp。
 
看起来好像高大上了一点
#### 对策
* HTTP协议中包含访问的域名，也就是说，你可以对Tomcat说：
** *将访问`novel.kitrst.cn`的请求踢到Novel去，将访问`blog.kitrst.cn`的请求踢到Blog去* **。
然后就搞定了-_-
怎么对Tomcat说呢？ 小二， 再来两斤`Host`，上代码
```
<Host name="novel.kitrst.cn" debug="0" appBase="webapps" unpackWARs="true" autoDeploy="true">
        <Context path="" docBase="C:\apache7.0.72\webapps\Novel" debug="0" reloadable="false"/>
      </Host>

      <Host name="blog.kitrst.cn" debug="0" appBase="webapps" unpackWARs="true" autoDeploy="true">
        <Context path="" docBase="C:\apache7.0.72\webapps\Blog" debug="0" reloadable="false"/>
      </Host>
```

#### 总结
通过主机名可以实现多域名映射

OK，收摊走人

