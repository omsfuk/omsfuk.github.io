# Spring Security

```
<?xml version="1.0" encoding="UTF-8"?>
<beans:beans xmlns="http://www.springframework.org/schema/security"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xmlns:beans="http://www.springframework.org/schema/beans"
             xsi:schemaLocation="
        http://www.springframework.org/schema/security
        http://www.springframework.org/schema/security/spring-security.xsd
        http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">

	// 启用el表达式，否则hasAnyRole无效，且报错，显示无法解析access="hasAnyRole...
    <http auto-config="true" use-expressions="true">
        <intercept-url pattern="/api/pub/**" access="hasAnyRole('ROLE_ADMIN', 'ROLE_USER')"/>
        <intercept-url pattern="/api/**" access="ROLE_ADMIN"/>
        <access-denied-handler error-page="/error.jsp"/>

        // 使用http基本认证，否则会弹出错误
        <http-basic/>
    </http>

    <authentication-manager>
        <authentication-provider>
            <user-service>
                <user name="admin" password="admin" authorities="ROLE_ADMIN"/>
                <user name="tom" password="123456" authorities="ROLE_USER"/>
            </user-service>
        </authentication-provider>
    </authentication-manager>

</beans:beans>
```
