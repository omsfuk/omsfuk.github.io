------------
title: mybatis总结
tags: mybatis
------------


[TOC]
# 引言
## 传统的JDBC编程
#### 概述
JDBC是由sun公司提出的一系列规范，但是它只定义了接口规范，而具体的实现由各个数据库厂商去实现。JDBC是一种典型的桥接模式。

#### 使用步骤
* 连接数据库，注册驱动和数据库信息
* 打开statement对象
* 通过statement执行sql，返回结果到resultset对象
* 使用resultset读取数据，然后通过代码转化为具体的POJO对象

#### 缺点
工作量大，对可能产生的异常进行捕获太麻烦

## ORM模型（对象关系映射）
简单的说，ORM模型就是数据库的表和简单的Java对象的映射关系模型，它主要解决数据库数据和POJO对象的相互映射。

#### Hiberinate
###### 概述
Hiberinate是建立在若干POJO通过XML映射文件（或注解）提供的规则映射到数据库表上的。换句话说，我们可以通过POJO直接操作数据库的数据。
它提供的是一种全表映射的模型。Hiberinate对JDBC的封装程度比较高，消除了代码的映射规则，全部被分离到了XML或注解里去配置，无需再管理数据库连接。
一个会话中，不需要操作多个对象，只要操作session对象即可。关闭资源只需要关闭一个session。
Hiberinate还提供了级联、映射、一堆多等功能。

###### 缺点
* 全表映射带来的不便，比如更新时需要发送所有的字段
* 无法根据不同的条件组装不同的sql
* 对多表关联和复杂sql查询支持较差
* 不能有效支持存储过程
* 虽然由HQL，但是性能较差（致命问题）

#### MyBatis
在大型互联网中，灵活，SQL优化，减少数据的传递是最基本的优化方法，显然，Hiberinate是无法满足我们的要求的。
为了解决Hiberinate的不足，一个半自动的映射框架MyBatis应运而生，只所以称它为半自动，是因为它需要手工匹配提供POJO、SQL、映射关系，而全表映射Hiberinate只需要提供POJO和映射关系即可。

不单单要我们提供映射关系，还需要提供SQL语句，MyBatis所需要提供的映射文件包含一下三个部分：SQL、映射规则、POJO。
MyBatis具有自动映射功能。换句话说，在注意一些规则的基础上，MyBatis可以帮我们完成自动映射，而无需任何映射规则。大大提高了开发的效率和灵活性。

## 各种技术的应用场景
* JDBC几乎不用
* Hiberinate场景不太复杂，要求性能不太苛刻
* 大型互联项目的首选框架

# MyBatis入门
## MyBatis核心组件
* SqlSessionFactoryBuilder
* SqlSessionFactory
* SqlSession既可以发送SQL并返回结果集，也可以获得mapper接口
* SqlMapper mybatis新设计的组件，由一个Java接口和XML文件（或注解）构成的，需要给出对应的sql映射规则，负责发送sql去执行，并返回结果

## SqlSessionFactory的构建
#### xml方式
会自动生成一个单实例的Configuration，存在于整个mybatis的生命周期中，一遍重复获取和使用。
configuration中的environments可以配置一堆environment，通过default来制定默认用哪一个environment

#### 代码方式
构建一个工厂方法，用构建的SqlSessionFactoryBuilder构建SqlSessionFactory

## 创建SqlSession
通过openSession来获取session，用close管理
SqlSession主要用途
* 获取映射器
* 直接通过命名信息去执行sql返回结果
* 支持事务，通过commit、rollback方法

## 映射器
映射器由Java接口和XML文件（或注解）共同组成
作用：定义参数类型和描述缓存，描述sql语句，定义查询结果和POJO的映射关系
#### XML方式
#### 注解方式
`@Select(value = "select ...")`
虽然简单，但是当语句特别复杂时，会造成可读性下降。
mybatis新增了mapper，单仍然保留着ibatis时代的通过命名 + sql id的方式发送sql并返回数据的形式
那么mapper究竟好在哪里？mapper是一个接口，相对而言它可以进一步屏蔽SqlSession，但它具有更强的业务可读性。
利用mapper方式，IDE可以检查Java语法，避免了不必要的错误
## 生命周期
#### SqlSessionFactoryBuilder
它的作用就是一个构建器，一旦构建了SqlSessionFactory，他的作用就已经完结，失去了存在的意义。所以他的生命周期只在于方法的局部。

#### SqlSessionFactory
SqlSessionFactory的作用是创建SqlSession。SqlSession就是一个会话，相当于JDBC中的Connection对象。SqlSessionFactory应该在MyBatis应用的整个生命周期中，采用单例模式。

#### SqlSession
一个会话，相当于JDBC中的一个Connection对象，它的生命周期应该是在请求数据库处理事务的过程中。它是一个线程不安全的对象，在涉及多线程时要特别当心。操作数据库需要注意其隔离级别，数据库锁等高级特性，需要及时关闭。

#### Mapper
Mapper的最大生命周期和SqlSession相同。

# 配置
```
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
	<properties/>属性
	<setting/>设置
	<typeAliases/>类型命名
	<typeHandlers/>类型处理器
	<objectFactory/>对象工厂
	<plugins/>插件
	<environments>配置环境
		<environment>环境变量
			<transactionManager/>事务管理器
			<dataSource/>数据源
		</environment>
	</environments>
	<databaseIdProvider/>数据库厂商标识
	<mappers/>映射器
</configuration>
```
这就是全部mabatis的配置文件

## properties元素
#### property子元素
可在配置文件中以`${}`形式调用

#### properties配置文件
将配置写在propeties文件中
<properties resource="jdbc.properties"/>

#### 程序参数传递
有时候数据库的用户名和密码是以密文的形式保存的。这时侯要通过编码进行配置。

#### 不同properties优先级
当三种配置同时存在的时候，有可能会出现properties冲突（优先级依次升高，后边的覆盖前边的）
* 先读property子元素
* 根据resource或url属性读取指定属性
* 读取作为方法参数传递的属性，并覆盖已读取的同名属性

## 设置
setting是mybatis中最复杂的配置，它会改变mybatis运行时的行为，即使不配置settings，mybatis也会正常工作

## 别名
系统已经定义了一些别名，要想自定义别名可以通过
* typeAliases配置
* 扫描 + @Alias注解。不加注解也可以，默认别名是类名的首字母小写。
```
<typeAliases>
	<package name="com.learn.po1"/>
	<package name="com.learn.po2"/>
</typeAliases>
```

## typeHandler类型处理器
typeHandler用于在Java Type和JDBC Type之间的转化。一般来说系统定义的就够用了。

## ObjectFactoty
当mybatis在构建一个结果返回的时候，都会使用ObjectFactory（对象工厂）去构建POJO。
大部分情况下，使用系统默认的即可

## 插件
比较负复杂，放在后边几章

## environment
#### 概述
配置环境可以注册多个数据源
每个数据源可以分为两大部分，一个是数据源的配置，另一个是数据库事务的配置
* transactionManager
它的type属性由三种配置方式
	* JDBC
	* MANAGED 采用容器方式管理事务，在JNDI数据源中常用
	* 自定义。适用于特殊应用

* dataSource
type属性提供对数据库连接方式的配置
	* UNPOOLED，非数据库连接池
	* POOLED，连接池数据库
	* JNDI，JNDI数据源
	* 自定义数据源

#### 数据库事务
大部分工作环境下，会使用Spring框架控制它

#### 数据库厂商标识
在相同的数据库厂商的环境下，数据库厂商标识没什么意义。在实际的应用中使用的比较少，因为使用不同厂商数据库的系统还是比较少的。mybatis可能会运行在不同厂商的数据库中，它为此提供了一个数据库厂商标识，并提供自定义它的作用在于指定sql在对应的数据库厂商提供的数据库中运行

## 引入映射器的方法
首先要提供一个mapper接口和一个mapper映射，然后有4种引入方式
#### 用文件路径引入映射器
```
<mappers>
	<mapper resource="..."/>
</mappers>
```

#### 用包名引入
```
<mappers>
	<package name="..."/>
</mappers>
```

#### 用类注册引入映射器
```
<mappers>
	<mapper class="..."/>
</mapper>
```

#### 用userMapper.xml引入
<mappers>
	<mapper url="file://...xml"/>
</mapper>

# 映射器
## 映射器的主要元素
select、insert、update、delete、parameterMap、sql、resultMap、cache、cache-ref

## select元素
```
<select
        <!--  1. id （必须配置）
        id是命名空间中的唯一标识符，可被用来代表这条语句。 
        一个命名空间（namespace） 对应一个dao接口, 
        这个id也应该对应dao里面的某个方法（相当于方法的实现），因此id 应该与方法名一致  -->
     id="selectPerson"
     
     <!-- 2. parameterType （可选配置, 默认为mybatis自动选择处理）
        将要传入语句的参数的完全限定类名或别名， 如果不配置，mybatis会通过ParameterHandler 根据参数类型默认选择合适的typeHandler进行处理
        parameterType 主要指定参数类型，可以是int, short, long, string等类型，也可以是复杂类型（如对象） -->
     parameterType="int"
     
     <!-- 3. resultType (resultType 与 resultMap 二选一配置)
         resultType用以指定返回类型，指定的类型可以是基本类型，可以是java容器，也可以是javabean -->
     resultType="hashmap"
     
     <!-- 4. resultMap (resultType 与 resultMap 二选一配置)
         resultMap用于引用我们通过 resultMap标签定义的映射类型，这也是mybatis组件高级复杂映射的关键 -->
     resultMap="personResultMap"
     
     <!-- 5. flushCache (可选配置)
         将其设置为 true，任何时候只要语句被调用，都会导致本地缓存和二级缓存都会被清空，默认值：false -->
     flushCache="false"
     
     <!-- 6. useCache (可选配置)
         将其设置为 true，将会导致本条语句的结果被二级缓存，默认值：对 select 元素为 true -->
     useCache="true"
     
     <!-- 7. timeout (可选配置) 
         这个设置是在抛出异常之前，驱动程序等待数据库返回请求结果的秒数。默认值为 unset（依赖驱动）-->
     timeout="10000"
     
     <!-- 8. fetchSize (可选配置) 
         这是尝试影响驱动程序每次批量返回的结果行数和这个设置值相等。默认值为 unset（依赖驱动)-->
     fetchSize="256"
     
     <!-- 9. statementType (可选配置) 
         STATEMENT，PREPARED 或 CALLABLE 的一个。这会让 MyBatis 分别使用 Statement，PreparedStatement 或 CallableStatement，默认值：PREPARED-->
     statementType="PREPARED"
     
     <!-- 10. resultSetType (可选配置) 
         FORWARD_ONLY，SCROLL_SENSITIVE 或 SCROLL_INSENSITIVE 中的一个，默认值为 unset （依赖驱动）-->
     resultSetType="FORWARD_ONLY">
```
#### 自动映射
当数据库列名和字段名一致时，只要autoMappingBehaviour参数不设置为NONO的时候（由三个值，PARTIAL、NONE、FULL，默认为PARTIAL），mybatis会自动映射。
若数据库使用下划线，java使用驼峰，要想进行自动映射，
* 方法一：列的别名
* 方法二：在配置文件中开启驼峰命名
设置mapUnderscoreToCamelCase为true

#### 传递多个参数
有三种方法
* 参数类型为map
* 注解方式传递参数
```
public List<Role> findRoleByAnnotation(@Param("roleName") String rolename, @Param("note") String note);
```
在xml中无需定义参数类型
* 使用javaBean传递参数

总结
* map方法，果断废弃 
* @Param方法，当参数<=5，可行，更直观
* 当参数>5，使用JavaBean

## insert元素
```
<insert
      <!-- 1. id （必须配置）
        id是命名空间中的唯一标识符，可被用来代表这条语句。 
        一个命名空间（namespace） 对应一个dao接口, 
        这个id也应该对应dao里面的某个方法（相当于方法的实现），因此id 应该与方法名一致 -->
      
      id="insertUser"
      
      <!-- 2. parameterType （可选配置, 默认为mybatis自动选择处理）
        将要传入语句的参数的完全限定类名或别名， 如果不配置，mybatis会通过ParameterHandler 根据参数类型默认选择合适的typeHandler进行处理
        parameterType 主要指定参数类型，可以是int, short, long, string等类型，也可以是复杂类型（如对象） -->
      
      parameterType="com.demo.User"
      
      <!-- 3. flushCache （可选配置，默认配置为true）
        将其设置为 true，任何时候只要语句被调用，都会导致本地缓存和二级缓存都会被清空，默认值：true（对应插入、更新和删除语句） -->
      
      flushCache="true"
      
      <!-- 4. statementType （可选配置，默认配置为PREPARED）
        STATEMENT，PREPARED 或 CALLABLE 的一个。这会让 MyBatis 分别使用 Statement，PreparedStatement 或 CallableStatement，默认值：PREPARED。 -->
      
      statementType="PREPARED"
      
      <!-- 5. keyProperty (可选配置， 默认为unset)
        （仅对 insert 和 update 有用）唯一标记一个属性，MyBatis 会通过 getGeneratedKeys 的返回值或者通过 insert 语句的 selectKey 子元素设置它的键值，默认：unset。如果希望得到多个生成的列，也可以是逗号分隔的属性名称列表。 -->
      
      keyProperty=""
      
      <!-- 6. keyColumn     (可选配置)
        （仅对 insert 和 update 有用）通过生成的键值设置表中的列名，这个设置仅在某些数据库（像 PostgreSQL）是必须的，当主键列不是表中的第一列的时候需要设置。如果希望得到多个生成的列，也可以是逗号分隔的属性名称列表。 -->
      
      keyColumn=""
      
      <!-- 7. useGeneratedKeys (可选配置， 默认为false)
        （仅对 insert 和 update 有用）这会令 MyBatis 使用 JDBC 的 getGeneratedKeys 方法来取出由数据库内部生成的主键（比如：像 MySQL 和 SQL Server 这样的关系数据库管理系统的自动递增字段），默认值：false。  -->
      
      useGeneratedKeys="false"
      
      <!-- 8. timeout  (可选配置， 默认为unset, 依赖驱动)
        这个设置是在抛出异常之前，驱动程序等待数据库返回请求结果的秒数。默认值为 unset（依赖驱动）。 -->
      timeout="20">
```

#### 主键回添和自定义
用keyProperty属性指定哪个是主键字段，同时使用useGeneratedkeys属性去告诉mybatis这个主键是否使用数据库内置策略生成。它会自动回填JavaBean的id值

实际工作中往往没有这么简单，需要根据一些特殊的关系设置主键id的值。假设取消掉表的t_role的id自增规则，要求是，如果表t_role没有记录，则需要设置id=1，否则我们就取最大的id+2来设置新的主键。
```
<insert id="insertRole" parameterType="role" useGeneratedKeys="true" keyProperty="id">
	<selectKey keyProperty="id" resultType="int" order="BEFORE">
		select if(max(id) is null, 1, max(id) + 2) as newId from t_role
	</selectKey>
	insert into t_role(id, role_name, note) values(#{id}, #{roleName}, #{note})
</insert>
```

# mybatis 注意事项

## 事务
Mybatis的事务管理器选择jdbc的话，默认是不会自动commit的。
但是需要非常注意的一点就是select语句是不需要commit的
因为我的测试过select就算不commit也能准确的得到查到的结果。
但是Insert等则必须commit了才能成功插入，否则不会报异常数据库也没有成功插入。
2016-01-12 答案：
在JavaBean数据库操作中，一项事务是指由一条或多条对数据库更新的sql语句所组成的一个不可分割的工作单元。只有当事务中的所有操作都正常完成了，整个事务才能被提交到数据库，如果有一项操作没有完成，就必须撤消整个事务。我们通过提交commit()或是回滚rollback（）来结束事务的操作。关于事务操作的方法都位于接口java.sql.Connection中

## mybatis返回列表
mybatis不支持返回列表类型的返回值，需要自己写映射
```
<resultMap id="departMent" type="Department">
    <id column="dp_id" property="dp_id" />
    <result column="dp_name" property="dp_name" />
    <result column="cost_center" property="cost_center" />
</resultMap>
```
然后在select标签中设置resultMap为自定义的类型的id

## 输入日志
mybatis使用了slf4j，依次查找
```
SLF4J
Apache Commons Logging
Log4j 2
Log4j
JDK logging
```

如果都没找到，则不显示日志

## OGNL
mybatis支持OGNL，具体去查资料。
使用`${}`或`#{}`来获得数据，后者可以方式sql注入。但是貌似不支持运算
前者需要get方法，后者支持自动转换。
