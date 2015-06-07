title: log4j配置参考
date: 2015-03-21 23:00:16
categories: java
tags: [log4j,java]
---

**说明**
>- 此规范使用Apache的log4j
>- 此规范只提供参考


#### 日志配置规范(推荐)

1, 不同级别的日志分成不同的日志文件输出
>尽量不要将info日志和error日志输出到一起,这样不便于日志分析

2, 每天不同级别分别生成一个日志文件
>这样时间久了，日志文件的数量可能较多，可以定期自动删除一部分较早的文件

3, 日志文件的名称为"项目名称+输出级别+日志输出日期"
>比如：lbt-data-layer-error.log.2014-12-15.log

#### log4j.properties配置参考

    ### set log levels ###
    log4j.rootLogger =INFO,stdout, I, E

    ######Console#######
    log4j.appender.stdout = org.apache.log4j.ConsoleAppender
    log4j.appender.stdout.layout = org.apache.log4j.PatternLayout
    log4j.appender.stdout.layout.ConversionPattern = %d{ABSOLUTE} %5p %c:%L - %m%n

    ### INFO ###
    log4j.appender.I = org.apache.log4j.DailyRollingFileAppender
    log4j.appender.I.File = ${catalina.home}/logs/lbt-data-layer-info.log
    log4j.appender.I.DatePattern='.'yyyy-MM-dd'.log'
    log4j.appender.I.Threshold = INFO
    log4j.appender.I.layout = org.apache.log4j.PatternLayout
    log4j.appender.I.layout.ConversionPattern = %-d{yyyy-MM-dd HH:mm:ss} [%t:%r] - [%p] %m%n

    ### ERROR ###
    log4j.appender.E=org.apache.log4j.DailyRollingFileAppender
    log4j.appender.E.File = ${catalina.home}/logs/lbt-data-layer-error.log
    log4j.appender.E.DatePattern='.'yyyy-MM-dd'.log'
    log4j.appender.E.Threshold = ERROR 
    log4j.appender.E.layout = org.apache.log4j.PatternLayout
    log4j.appender.E.layout.ConversionPattern = %-d{yyyy-MM-dd HH:mm:ss} [%t:%r] - [%p] %m%n




#### 日志输出规范

1, 定义日志变量
>日志变量往往不变，最好定义成final static，变量名用大写。

2, 日志分级
Java的日志框架一般会提供以下日志级别，缺省打开info级别，也就是debug，trace级别的日志在生产环境不会输出，在开发和测试环境可以通过不同的日志配置文件打开debug级别。

>- fatal - 严重的，造成服务中断的错误；
>- error - 其他错误运行期错误；
>- warn - 警告信息，如程序调用了一个即将作废的接口，接口的不当使用，运行状态不是期望的但仍可继续处理等；
>- info - 有意义的事件信息，如程序启动，关闭事件，收到请求事件等；
>- debug - 调试信息，可记录详细的业务处理到哪一步了，以及当前的变量状态；
>- trace - 更详细的跟踪信息；

3, 基本的Logger编码规范(参考)
>- 在一个对象中通常只使用一个Logger对象，Logger应该是static final的，只有在少数需要在构造函数中传递logger的情况下才使用private final
>- 输出Exceptions的全部Throwable信息，因为logger.error(msg)和logger.error(msg,e.getMessage())这样的日志输出方法会丢失掉最重要的StackTrace信息。
>- 不允许记录日志后又抛出异常，因为这样会多次记录日志，只允许记录一次日志。
>- 不允许出现System print(包括System.out.println和System.error.println)语句。
>- 不允许出现printStackTrace。
>- 日志性能的考虑，如果代码为核心代码，执行频率非常高，则输出日志建议增加判断，尽量少输出没有作用的日志，尤其是低级别的输出debug、info、warn。
>- 通常情况下在程序日志里记录一些比较有意义的状态数据：程序启动，退出的时间点；程序运行消耗时间；耗时程序的执行进度；重要变量的状态变化。