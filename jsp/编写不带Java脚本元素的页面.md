-----------------------
title: 编写不带Java脚本元素的页面
tags: el,jsp,servlet,读书笔记
-----------------------
## JavaBean与JSP标准动作


#### 编码约定

* 提供一个无参构造器
* 实现Serializable接口
* 所有属性都是私有（private）
* 提供getter和setter方法
* boolean类型的属性可以写将get方法改为isXXXX方法


#### 使用JSP标准动作访问JavaBean
* useBean动作


        <jsp:useBean id="变量名" class="全路径类名" scope="范围" />
        如果没有找到现有对象，那么就会创建一个新的而对象


* setProperty动作


        <jsp:setProperty name="变量名" property="属性名" value="值" />


* getProperty动作


        <jsp:getProperty name="变量名" property="属性名">


#### JSP标准再讨论
* type属性


    相当于父类引用指向子类对象


        <jsp:useBean id="person" type="cn.knife037.bean.PersonBean"
        class="cn.knife037.bean.employeeBean" scope="page" />


* 直接获取表单数据


    当表单提交的属性与JavaBean中的属性一致时，可以直接使用param参数，否则需要使用value参数 + 小脚本来实现。


        <jsp:setProperty name="person" property="name" param="empName"/>
        其中empName是表单提交的一个属性
    如果满足所有的请求参数名称都完全对应Bean的属性名称，还可以有更简便的方法


        <jsp:setProperty name="person" property="*"/>


## 表达式语言（Expression Language）
#### EL剖析
* EL隐含对象


    1. 范围对象 pageScope、requestScope、sessionScope和applicationScope
    2. 参数对象 param和paramValues
    3. cookie
    4. initParam
    5. pageContext




* PageContext是容器中真实的pageContext对象的引用，它实质上是一个JavaBean。其余的隐含对象都是Map对象，保存 `名-值` 对的集合类型。




* 点（.）操作符 
可以直接访问属性，而不是get+属性名。


        ${bean.name}


* []操作符


    能用点号的地方一定可以用[]。[]可以完成对一些不符合Java命名规范的属性的操作。如`aaa.bbb`（这是属性名）、`align-right`。
* EL运算符


    JSP只是用于显示响应的的视图，而不是完成很大运算量的工作。 
    EL运算符只是用于超小规模的运算 
    EL支持三元运算符。
* EL函数 


    编写EL函数的步骤


    1. 编写具有公共静态方法的Java类


            package com.knife037.el;


            public class Functions {
                public static String reserve(String text) {
                    return new StringBuilder(test).reverse().toString();
                }
            }
    2. 编写标签库描述符文件（TLD）
TLD文件为EL函数提供一种描述定义函数的Java类与调用函数的JSP之间的映射关系。这样，函数名称可以不同于真实方法名称。


            <?xml version="1.0" encoding="utf-8" ?>
            <taglib ...>
                <tlib-version>1.0</tlib-version>
                <short-name>myfunctions</short-name>
                <uri>http://www.jeelearning.com/myfunctions</uri>
                <function>
                    <description>将给定的字符串反转</description>
                    <name>reverse</name>
                    <function-class>cn.knife037.el.Functions</function-class>
                    <function-signature>
                        java.lang.String reverse(java.lang.String)
                    </function-signature>
                </function>
            </taglib>
    参数类型与返回值必须是全路径。
* 在JSP中调用函数
    1. 指明在JSP页面使用哪一个标签库


            <%taglib prefix="my" uri="http://www.knife037.cn/myfuntions"%>
    2. 调用函数


            　${my:reverse(poem)}
#### EL的隐含对象
* 请求参数


    对于数组参数可以用数组的写法来获取。


        param.name
        paramValues.name[0]
* 请求头


        header.host
        headerValues.cookie[0]
* Cookie对象


        ${cookie.user.value}
* 初始化参数
* 范围对象


        ${person.name}
    如果不指明范围，容器会按照从大到小的顺序来查找相应的变量。 
    为了避免潜在的命名冲突，最后指定范围。


        ${requestScope.person.name}
* 页面上下文对象
#### Java小脚本和EL的控制
* 禁止Java小脚本


        <web-app ...>
            ...
            <jsp-config>
                <jsp-property-group>
                    <url-pattern>*.jsp</url-pattern>
                    <scripting-invalid>true</script-invalid>
                </jsp-property-group>
            </jsp-config>
        </web-app>
* 忽略EL表达式


    1. 禁用所有EL表达式


            <web-app ...>
                ...
                <jsp-config>
                    <jsp-property-group>
                        <url-pattern>*.jsp</url-pattern>
                        <el-ignored>true</el-ignored>
                    </jsp-property-group>
                </jsp-config>
            </web-app>
    2. 只是禁用某个网页的EL表达式


            <%@ page isElignored="true" %>
## 解惑
* 在JSP页面中显示字符串$


    \S
