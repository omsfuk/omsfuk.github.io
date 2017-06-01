-----------------------
title: Spring杂记
tags: Spring
-----------------------

## MVC和三层架构的关系
* MVC : Model + View + Controller(数据模型+视图+控制器)
* 三层架构 : Presentation tier + Application tier + Data tier（展现层+应用层+数据访问层）
* **实际上MVC只存在三层架构的展现层，M实际上是数据模型，是包含数据的对象。**
* 而三层架构是整个应用的架构，是由Spring框架负责管理的。一般项目结构中都有Service层，DAO层，这两个反馈在应用层和数据访问层


## Spring测试框架的使用
在测试类上加
```
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = "classpath:spring.xml")
```
其中，要是spring.xml放在web-inf目录，要用`file:src/main/webapp/WEB-INF/spring.xml`。

其中如果将xml文件放在resources目录下，可以用classpath访问（即时是放在main的resources目录）

使用xml方式加载properties，environment无法获取属性。
PropertiesSource注解要写在@Configuration注解的类上，不知道非测试环境下行不行了。。。但是EL表达式是正常的

## 利用JMock测试
需要加上servlet-api，否则会出现初始化失败

## Spring mvc 中的路径匹配
可以参见另一片笔记中的关于Servlet的路径匹配，对比一下
* `**`表示多级目录
* `*`表示一级
* ** * 一旦在dispatcher中配置的不是'/'，那么所有的匹配都以此为基础
例如url-pattern设置为`/dispatcher/*`
则，RequestMapping("/test")注解所对应的url是`/dispatcher/test`，而不是`/test` * **
