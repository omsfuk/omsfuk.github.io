-----------------------
title: Spring基础
tags: Spring
-----------------------
# Spring基础

## Spring简史
* xml配置
* 注释配置
* Java配置

## Spring四大原则
* 使用POJO进行轻量级和最小侵入式开发
* 通过依赖注入和基于接口编程实现松耦合。
* 通过AOP和默认习惯进行声明式编程
* 使用AOP和模板减少模式化代码

## 依赖注入
在Spring环境下，IOC与DI是同样的概念，控制反转是通过依赖注入实现的。所谓依赖注入指的是`容器负责创建对象和维护对象间的依赖关系，而不是通过对象本身负责自己的创建的解决自己的依赖`。
依赖注入的主要目的是为了`解耦`。

无论是xml配置、注解配置还是Java配置，都被称为配置元数据，所谓元数据即描述数据的数据。元数据本身不具备任何可执行的能力，只能通过外界代买来对这些元数据进行解析后进行一些有意义操作。

声明Bean的注解
* @Component 组件，没有明确的角色
* @Service 在业务逻辑层（Service层）使用
* @Repository 在数据访问层（dao层）使用
* @Controller 在展现层（MVC->Spring MVC）使用

注入Bean的注解，一般情况下通用。
@Autowired Spring提供的注解
@Inject JSR-330提供的注解
@Resource JSR-250提供的注解

## Java配置
Java配置是Spring4.x推荐的配置方式，可以完全替代xml配置；Java配置也是SpringBoot推荐的配置方式

* @Configuration声明当前类是一个配置类，相当于一个Spring配置的xml文件
* @ComponentScan("com.wisely.hightlight_spring4.chh1.di")自动扫描包名下所有使用@Service、@Component、@Repository、和@Controller的类，并注册为Bean
* Bean注解在方法上，声明当前方法的返回值为一个Bean。Bean的名称是方法名。Bean的参数中若有其他Bean，会自动注入

## AOP
AOP：面向切面变成，相对于OOP面向对象编程。
Spring的AOP的存在目的是为了解耦。AOP可以让一组类共享相同的行为。在OOP中只能通过继承类和实现接口，来使代码的耦合度增强，且类继承只能为单继承，阻碍更多行为添加到一组类上，AOP弥补了OOP的不足。
Spring支持AspectJ的注解式切面编程。
* 使用@AspectJ声明是一个切面
* 使用@After、@Before、@Around定义建言（advice），可直接将拦截规则（切点）作为参数。
* 其中参数的拦截规则为切点(PointCut)，为了使切点复用，可使用`@PointCut`专门定义拦截规则，然后在@After、@Before、@Around的参数中调用。
* 其中符合条件的每一个被拦截处为连接点。
* 在配置类中使用@EnableAspectJAutoProxy注解开启Spring对AspectJ的支持。

# Spring常用配置

## Bean 的 Scope
* Singleton 一个Spring容器中只有一个Bean的实例，此为Spring的默认配置，全容器共享一个实例。
* Prototype 每次调用新建一个Bean的实例
* Request Web项目中，给每一个http request新建一个Bean实例
* Session Web项目中，给每一个http session新建一个bean实例
* GlobalSession 这个只在portaal应用中有用，给每一个global http session新建一个Bean实例。

## Spring EL-Spring表达式语言
Spring EL-Spring表达式语言，支持在xml和注解中使用表达式，类似于JSP的EL表达式语言。
Spring开发中经常涉及调用各种资源的情况，包含普通文件、网址、配置文件、系统环境变量等，我们可以使用Spring的表达式语言实现资源的注入。
Spring主要在@Value参数中使用表达式。
`common-io`可以简化文件操作
示例
```java
@Configuration
@ComponentScan("com.wisely.highlight_spring4.ch2.el")
@PropertySource("classpath:com/wisely/highlight_spring4/ch4/el/test/propertise")
public class ElConfig {
@Value("I love you!")
private String normel;

    @Value("#{systemProperties['os.name']}")
    private String osName;

    @Value("#{T(java.lang.Math).random * 100.0}")
    private double randomNumber;

    @Value("#{demoService.another}")
    private String fromAnother

    @Value("classpath:com/wisely/highlight_spring4/ch2/el/test.txt")
    private Resource testFile;

    @Value("http://www.baidu.com")
    private Resource testUrl;

    @Value("bookName")
    private String bookName;

    @Autowired
    private String bookName;

    @Bean
    public static PropertySourcesPlaceholderConfigurer propertyConfigure() {
        return new PropertySourcesPlaceholderConfigurer();
    }
    public void outputResource() {
        try {
            System.out.println(normal);
            System.out.println(osName);
            System.out.println(randomNumber);
            System.out.println(fromAnother);

            System.out.println(IOUtil.toString(testFile.getInputStream(()));
            Systemout.println(IOUtil.toString(testUrl.getInputStream()));
            System.out.println(bookName);
            System.out.println(environment.getProperty("book.author"));
        } catch (Exception e) {
            e.printStactTrace();
        }
    }
}
```
