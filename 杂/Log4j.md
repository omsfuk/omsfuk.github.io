---------------------
title: Log4J入门
tags: 杂记
---------------------

# Log4j简介
## 在应用程序中添加日志记录的目的：
* 监视代码中变量的变化情况，周期性的记录到文件中供其他应用进行统计分析工作；
* 跟踪代码运行时轨迹，作为日后审计的依据；
* 担当集成开发环境中的调试器的作用，向文件或控制台打印代码的调试信息。

最普通的做法就是在代码中嵌入许多的打印语句，这些打印语句可以输出到控制台或文件中，比较好的做法就是构造一个日志操作类来封装此类操作，而不是让一系列的打印语句充斥了代码的主体。
## Log4j的作用
* Log4j 是 Apache 的一个开放源代码项目，通过使用 Log4j，我们可以控制日志信息输送的目的地是控制台、文件、 GUI 组件、甚至是套接口服务器、 NT 的事件记录器、 UNIX Syslog 守护进程等；我们也可以控制每一条日志的输出格式；通过定义每一条日志信息的级别，我们能够更加细致地控制日志的生成过程。最令人感兴趣的就是，这些可以通过一个配置文件来灵活地进行配置，而不需要修改应用的代码。

## Log4j构成
log4j支持xml和.property文件配置。在配置文件中需要配置三方面的内容
* 根目录（级别和目的地）
* 目的地（控制台、文件等）
* 输出样式

#### Log4j类图
![Log4j类图](https://github.com/Knife037/Images/blob/master/log4j%E7%B1%BB%E5%9B%BE.png?raw=true)

* Logger - 日志写出器，供程序员输出日志信息
* Appender - 日志目的地，把格式化好的日志信息输出到指定的地方去
* ConsoleAppender - 目的地为控制台的 Appender
* FileAppender - 目的地为文件的 Appender
* RollingFileAppender - 目的地为大小受限的文件的 Appender
* Layout - 日志格式化器，用来把程序员的 logging request 格式化成字符串
* PatternLayout - 用指定的 pattern 格式化 logging request 的 Layout

## Log4j使用方法
Log4j 由三个重要的组件构成：日志信息的优先级，日志信息的输出目的地，日志信息的输出格式。
* 日志信息的优先级从高到低有 ERROR、 WARN、 INFO、 DEBUG，分别用来指定这条日志信息的重要程度；
* 日志信息的输出目的地指定了日志将打印到控制台还是文件中；
* 而输出格式则控制了日志信息的显示内容。

#### properties 配置文件详解
* 配置根 Logger，其语法为：
```
log4j.rootLogger = level,appenderName1,appenderName2, ...
```
其中， level 是日志记录的优先级，分为 `OFF、 FATAL、 ERROR、 WARN、 INFO、 DEBUG、 ALL` 或者您定义的级别。 Log4j 建议只使用四个级别，优先级从高到低分别是 `ERROR、 WARN、 INFO、 DEBUG`。通过在这里定义的级别，您可以控制到应用程序中相应级别的日志信息的开关。比如在这里定义了 INFO 级别，则应用程序中所有DEBUG 级别的日志信息将不被打印出来。 appenderName 就是指 B 日志信息输出到哪个地方。您可以同时指定多个输出目的地。 优先级： `ALL < DEBUG < INFO <WARN < ERROR < FATAL < OFF`

* 配置日志信息输出目的地 Appender，其语法为：
```
log4j.appender.appenderName = Log4j 提供的 appender 类
log4j.appender.appenderName.属性名 = 属性值
...
log4j.appender.appenderName.属性名 = 属性值
```
 其中， Log4j 提供的 appender 有以下几种
 
 * org.apache.log4j.ConsoleAppender（控制台），
 * org.apache.log4j.FileAppender（文件），
 * org.apache.log4j.DailyRollingFileAppender（每天产生一个日志文件），
 * org.apache.log4j.RollingFileAppender（文件大小到达指定尺寸的时候产生一个新的文件），
 * org.apache.log4j.WriterAppender（将日志信息以流格式发送到任意指定的地方）
 
 ##### 分别有以下选项
 
  * ConsoleAppender 选项
  `Threshold=WARN`:指定日志消息的输出最低层次。
  `ImmediateFlush=true`:默认值是 true,意谓着所有的消息都会被立即输出。
  `Target=System.err`：默认情况下是： System.out,指定输出控制台
  * FileAppender 选项
  `Threshold=WARN`:指定日志消息的输出最低层次。
  `ImmediateFlush=true`:默认值是 true,意谓着所有的消息都会被立即输出。
  `File=mylog.txt`:指定消息输出到 mylog.txt 文件。
  `Append=false`:默认值是 true,即将消息增加到指定文件中， false 指将消息覆盖指定的文件内容。
  * DailyRollingFileAppender 选项
  `Threshold=WARN`:指定日志消息的输出最低层次。
  `ImmediateFlush=true`:默认值是 true,意谓着所有的消息都会被立即输出。
  `File=mylog.txt`:指定消息输出到 mylog.txt 文件。
  `Append=false`:默认值是 true,即将消息增加到指定文件中， false 指将消息覆盖指定的文件内容。
  `DatePattern=''.''yyyy-ww`:每周滚动一次文件，即每周产生一个新的文件。
  当然也可以指定按月、周、天、时和分。即对应的格式如下：
  1)''.''yyyy-MM: 每月
  2)''.''yyyy-ww: 每周
  3)''.''yyyy-MM-dd: 每天
  4)''.''yyyy-MM-dd-a: 每天两次
  5)''.''yyyy-MM-dd-HH: 每小时
  6)''.''yyyy-MM-dd-HH-mm: 每分钟
  * RollingFileAppender 选项
  `Threshold=WARN`:指定日志消息的输出最低层次。
  `ImmediateFlush=true`:默认值是 true,意谓着所有的消息都会被立即输出。
  `File=mylog.txt`:指定消息输出到 mylog.txt 文件。
  `Append=false`:默认值是 true,即将消息增加到指定文件中， false 指将消息覆盖指定的文件内容。
  `MaxFileSize=100KB`: 后缀可以是 KB, MB 或者是 GB. 在日志文件到达该大小时，将会自动滚动，
  即将原来的内容移到 mylog.log.1 文件。
  `MaxBackupIndex=2`:指定可以产生的滚动文件的最大数。
  
* 配置日志信息的格式（布局），其语法为：
```
log4j.appender.appenderName.layout = Log4j 提供的 layout 类
log4j.appender.appenderName.layout.属性 = 值
...
log4j.appender.appenderName.layout.属性 = 值
```
其中， Log4j 提供的 layout 有以下几种：
  * org.apache.log4j.HTMLLayout（以 HTML 表格形式布局），
  * org.apache.log4j.PatternLayout（可以灵活地指定布局模式），
  * org.apache.log4j.SimpleLayout（包含日志信息的级别和信息字符串），
  * org.apache.log4j.TTCCLayout（包含日志产生的时间、线程、类别等等信息）
