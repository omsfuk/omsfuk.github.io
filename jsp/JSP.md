-----------------------
title: JSP
tags: jsp,读书笔记
-----------------------
## JSP简介
#### JSP的生命周期
* 转换阶段
* 执行阶段
#### JSP的工作原理
* 用户第一次请求 => JSP页面转换为Servlet => 编译为字节码 => 返回HTML
#### 提高Tomcat运行效率（提高第一次访问的效率）
* 进行预编译
* 使用Tomcat的生产模式 


        web.xml
        <servlet>
            <servlet-name>jsp</servlet-name>
            <servlet-class>org.apache.jasper.servlet.JspServlet</servlet-class>
            <init-param>
                <param-name>fork</param-name>
                <param-value>false</param-value>
            </init-param>
            <init-param>
                <param-name>xpoweredBy</param-name>
                <param-value>false</param-value>
            </init-param>
            <load-on-startup>3</load-on-startup>
        </servlet>


    这是配置文件中的一段代码，可以仿照此格式，调整checkInterval、development、modificationTestInterval参数。
#### JSP的基本元素
* 指令元素 
    * page指令 


            <%@ page attribute="value" %>
        - `lanauage`属性 只能为java，所以可以省略
        - `info`属性 关于页面的一些信息
        - `import`属性
        - `contentType`属性 text/html; charset=utf-8
        - `pageEncoding`属性
        - `session`属性 时候使用session
        - `errorPage`属性
        - `isErrorPage`属性
        - `buffer`属性
        - `autoFlash`属性
        - `isELIgnored`
        - `isScriptingEnabled`


    * include指令 


            编译时指令
            <%@ include file="相对路径的url"%>
    * taglib指令 


            <%@ taglib prefix="prefixOfTag" uri=""tagLibraryURI" %>
            <%@ taglib prefix="prefixOfTag" tagdir="tagDir" %>
* 动作元素 
所有的动作标签都有两个共有属性：`id`属性和`scpoe`属性 
scope有4个可能的值：page、request、session和application
    * `<jsp:include>`和`<jsp:param>`动作元素标签 
    `<jsp:include>`动作相似于`<%@ include%>`指令。但是他们的重要区别是执行的时间。


            <jsp:include page="relative URL" flush="true" >


            <jsp:include page="relative URL" flush="true" >
                <jsp:param name="param_name" value="param_value" />
                <jsp:param name="param_name" value="param_value" />
            </jsp:include>
    *  `<jsp:useBean>`、`<jsp:setProerty>`和`<jsp:getProperty>`动作元素 


            <jsp:useBean id="name" class="package.className" />


            <jsp:useBean id="myName" class="package.className">
                // 当把此语句放在jsp:usebean之外时，无论是找到现有Bean还是创建新的Bean，都会被执行
                <jsp:setProperty name="myName" preperty="someProperty">
            </jsp:useBean>


            <jsp:getProperty name="myName" property="someProperty" />
    * `<jsp:forward>`动作元素 
    作用为转发


            <jsp:forward page="relative URL" />


* 脚本元素 
    JSP有4种脚本元素类型：生命、小脚本、表达式和EL表达式
    * 声明 
    声明的内容会被转换为属性或成员方法 


            <% declaration1; declaration2; ... %>


            <%! int i = 0; %>
            <%! int a, b, c; %>
            <%! int num = 100; %>
            <%!
                int doubleNum() {
                    num = num << 1;
                    return num;
                }
            %>
    * 小脚本 


            <% Java语句; %>
    * 表达式 


            <%= 表达式 %>
    * EL表达式


#### JSP的注释
* HTML格式注释


        <!-- 这是HTML格式注释 -->
* JSP代码注释


        <%-- --%>
* Java语言注释




        <%
            // 这是Java语言注释
        %>
## JSP对象
#### 对象的有效范围 
按照作用范围的不同，可以分为 


* 页内有效 pageContext
* 请求有效 request
* 会话有效 session
* 应用有效 application
#### JSP的隐含对象 
* request
* response
* out
* session
* application
* config
* pageContext 提供对JSP页面所有对象以及命名空间的访问
* page Object类的实例，类似于this关键字
* exception
