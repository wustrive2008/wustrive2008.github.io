title: logback详解
date: 2016-12-30 21:50:25
categories: java
tags: [java,logback,slf4j,日志]
photos:
- http://7xifb5.com1.z0.glb.clouddn.com/wustrive-hexojava知识结构.png
---

### logback详解

>>说明：下面内容从网上搜集而来

#### 与log4j对比

- 更快的执行速度： 基于我们先前在log4j上的工作，logback 重写了内部的实现，在某些特定的场景上面，甚至可以比之前的速度快上10倍。在保证logback的组件更加快速的同时，同时所需的内存更加少。
- logback-classic 非常自然的实现了SLF4J：ogback-classic中的longging类自然的实现了SLF4J。当你使用 logback-classic作为底层实现时，涉及到LF4J日记系统的问题你完全不需要考虑。更进一步来说，由于 logback-classic强烈建议使用SLF4J作为客户端日记系统实现，如果需要切换到log4j或者其他，你只需要替换一个jar包即可，不需要去改变那些通过SLF4J API 实现的代码。这可以大大减少更换日记系统的工作量。
- 自动重新载入配置文件：Logback-classic可以在配置文件被修改后，自动重新载入。这个扫描过程很快，无资源争用，并且可以动态扩展支持在上百个线程之间每秒上百万个调用。它和应用服务器结合良好，并且在JEE环境通用，因为它不会调用创建一个单独的线程来做扫描。
- 优雅地从I/O错误中恢复：FileAppender和它的子类，包括RollingFileAppender，可以优雅的从I/O错误中恢复。所以，如果一个文件服务器临时宕机，你再也不需要重启你的应用，而日志功能就能正常工作。当文件服务器恢复工作，logback相关的appender就会透明地和快速的从上一个错误中恢复。
- 自动清除旧的日志归档文件：通过设置TimeBasedRollingPolicy 或者 SizeAndTimeBasedFNATP的 maxHistory 属性，你就可以控制日志归档文件的最大数量。如果你的回滚策略是每月回滚的，并且你希望保存一年的日志，那么只需简单的设置maxHistory属性为12。对于12个月之前的归档日志文件将被自动清除。
- 自动压缩归档日志文件：RollingFileAppender可以在回滚操作中，自动压缩归档日志文件。压缩通常是异步执行的，所以即使是很大的日志文件，你的应用都不会因此而被阻塞。
- 配置文件中的条件处理：开发者通常需要在不同的目标环境中变换logback的配置文件，例如开发环境，测试环境和生产环境。这些配置文件大体是一样的，除了某部分会有不同。为了避免重复，logback支持配置文件中的条件处理，只需使用`<if>`,`<then>`和`<else>`，那么同一个配置文件就可以在不同的环境中使用了。
- 过滤： Logback拥有远比log4j更丰富的过滤能力。例如，让我们假设，有一个相当重要的商业应用部署在生产环境。考虑到大量的交易数据需要处理，记录级别被设置为WARN，那么只有警告和错误信息才会被记录。现在，想象一下，你在开发环境遇到了一个臭虫，但是在测试平台中却很难发现，因为一些环境之间(生产环境/测试环境)的未知差异。使用log4j，你只能选择在生产系统中降低记录的级别到DEBUG，来尝试发现问题。但是很不幸，这会生成大量的日志记录，让分析变得困难。更重要的是，多余的日志记录会影响到生产环境的性能。使用logback，你可以选择保留只所有用户的WARN级别的日志，而除了某个用户，例如Alice，而她就是问题的相关用户。当Alice登录系统，她就会以DEBUG级别被记录，而其他用户仍然是以WARN级别来记录日志。这个功能，可以通过在配置文件的XML中添加4行。请在相关章节中查找MDCFilter


#### logback 基础配置

##### 根节点<configuration>包含的属性：

- scan: 当此属性设置为true时，配置文件如果发生改变，将会被重新加载，默认值为true。
- scanPeriod: 设置监测配置文件是否有修改的时间间隔，如果没有给出时间单位，默认单位是毫秒。当scan为true时，此属性生效。默认的时间间隔为1分钟。
- debug: 当此属性设置为true时，将打印出logback内部日志信息，实时查看logback运行状态。默认值为false。

```xml
<configuration scan="true" scanPeriod="60 seconds" debug="false">  
      <!-- 其他配置省略-->  
</configuration>  
```

##### 根节点`<configuration>`的子节点

设置上下文名称：`<contextName>`
每个logger都关联到logger上下文，默认上下文名称为“default”。但可以使用`<contextName>`设置成其他名字，用于区分不同应用程序的记录。一旦设置，不能修改。

```xml
<configuration scan="true" scanPeriod="60 seconds" debug="false">  
      <contextName>myAppName</contextName>  
      <!-- 其他配置省略-->  
</configuration>  
```

设置变量： `<property>`
有两个属性，name和value；其中name的值是变量的名称，value的值时变量定义的值。通过`<property>`定义的值会被插入到logger上下文中。定义变量后，可以使“${}”来使用变量。
例如使用`<property>`定义上下文名称，然后在`<contentName>`设置logger上下文时使用。

```xml
<configuration scan="true" scanPeriod="60 seconds" debug="false">  
      <property name="APP_Name" value="myAppName" />   
      <contextName>${APP_Name}</contextName>  
      <!-- 其他配置省略-->  
</configuration>   
```

获取时间戳字符串：`<timestamp>`

两个属性 key:标识此`<timestamp>` 的名字；datePattern：设置将当前时间（解析配置文件的时间）转换为字符串的模式，遵循Java.txt.SimpleDateFormat的格式。
例如将解析配置文件的时间作为上下文名称：
```xml
<configuration scan="true" scanPeriod="60 seconds" debug="false">  
      <timestamp key="bySecond" datePattern="yyyyMMdd'T'HHmmss"/>   
      <contextName>${bySecond}</contextName>  
      <!-- 其他配置省略-->  
</configuration>   
```

##### 设置loger：

`<loger>`
用来设置某一个包或者具体的某一个类的日志打印级别、以及指定`<appender>`。`<loger>`仅有一个name属性，一个可选的level和一个可选的addtivity属性。

- name: 用来指定受此loger约束的某一个包或者具体的某一个类。
- level: 用来设置打印级别，大小写无关：TRACE, DEBUG, INFO, WARN, ERROR, ALL 和OFF，还有一个特俗值INHERITED或者同义词NULL，代表强制执行上级的级别。如果未设置此属性，那么当前loger将会继承上级的级别。
- addtivity: 是否向上级loger传递打印信息。默认是true。
`<loger>`可以包含零个或多个`<appender-ref>`元素，标识这个appender将会添加到这个loger。

`<root>`
也是`<loger>`元素，但是它是根loger。只有一个level属性，应为已经被命名为"root".
- level: 用来设置打印级别，大小写无关：TRACE, DEBUG, INFO, WARN, ERROR, ALL 和 OFF，不能设置为INHERITED或者同义词NULL。
默认是DEBUG。
`<root>`可以包含零个或多个`<appender-ref>`元素，标识这个appender将会添加到这个loger。

#### 示例
LogbackDemo.java
```java
package logback;  
  
import org.slf4j.Logger;  
import org.slf4j.LoggerFactory;  
  
public class LogbackDemo {  
    private static Logger log = LoggerFactory.getLogger(LogbackDemo.class);  
    public static void main(String[] args) {  
        log.trace("======trace");  
        log.debug("======debug");  
        log.info("======info");  
        log.warn("======warn");  
        log.error("======error");  
    }  
}  
```

logback.xml配置文件
**第1种：只配置root**
```xml
<configuration>   
   
  <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">   
    <!-- encoder 默认配置为PatternLayoutEncoder -->   
    <encoder>   
      <pattern>%d{HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n</pattern>   
    </encoder>   
  </appender>   
   
  <root level="INFO">             
    <appender-ref ref="STDOUT" />   
  </root>     
     
 </configuration> 
```

其中appender的配置表示打印到控制台(稍后详细讲解appender )；
`<root level="INFO">`将root的打印级别设置为“INFO”，指定了名字为“STDOUT”的appender。
 
当执行logback.LogbackDemo类的main方法时，root将级别为“INFO”及大于“INFO”的日志信息交给已经配置好的名为“STDOUT”的appender处理，“STDOUT”appender将信息打印到控制台；

打印结果如下：

```
13:30:38.484 [main] INFO  logback.LogbackDemo - ======info  
13:30:38.500 [main] WARN  logback.LogbackDemo - ======warn  
13:30:38.500 [main] ERROR logback.LogbackDemo - ======error 
```

**第2种：带有loger的配置，不指定级别，不指定appender**

```xml
<configuration>   
   
  <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">   
    <!-- encoder 默认配置为PatternLayoutEncoder -->   
    <encoder>   
      <pattern>%d{HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n</pattern>   
    </encoder>   
  </appender>   
   
  <!-- logback为java中的包 -->   
  <logger name="logback"/>   
   
  <root level="DEBUG">             
    <appender-ref ref="STDOUT" />   
  </root>     
     
 </configuration>  
```

其中appender的配置表示打印到控制台(稍后详细讲解appender )；
`<logger name="logback" />`将控制logback包下的所有类的日志的打印，但是并没用设置打印级别，所以继承他的上级`<root>`的日志级别“DEBUG”；
没有设置addtivity，默认为true，将此loger的打印信息向上级传递；
没有设置appender，此loger本身不打印任何信息。
`<root level="DEBUG">`将root的打印级别设置为“DEBUG”，指定了名字为“STDOUT”的appender。
 
当执行logback.LogbackDemo类的main方法时，因为LogbackDemo 在包logback中，所以首先执行`<logger name="logback" />`，将级别为“DEBUG”及大于“DEBUG”的日志信息传递给root，本身并不打印；
root接到下级传递的信息，交给已经配置好的名为“STDOUT”的appender处理，“STDOUT”appender将信息打印到控制台；

打印结果如下：

```
13:19:15.406 [main] DEBUG logback.LogbackDemo - ======debug  
13:19:15.406 [main] INFO  logback.LogbackDemo - ======info  
13:19:15.406 [main] WARN  logback.LogbackDemo - ======warn  
13:19:15.406 [main] ERROR logback.LogbackDemo - ======error  
```

**第3种：带有多个loger的配置，指定级别，指定appender** 

```xml
<configuration>   
   <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">   
    <!-- encoder 默认配置为PatternLayoutEncoder -->   
    <encoder>   
      <pattern>%d{HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n</pattern>   
    </encoder>   
  </appender>   
   
  <!-- logback为java中的包 -->   
  <logger name="logback"/>   
  <!--logback.LogbackDemo：类的全路径 -->   
  <logger name="logback.LogbackDemo" level="INFO" additivity="false">  
    <appender-ref ref="STDOUT"/>  
  </logger>   
    
  <root level="ERROR">             
    <appender-ref ref="STDOUT" />   
  </root>     
</configuration>  
```

其中appender的配置表示打印到控制台(稍后详细讲解appender )；
 
`<logger name="logback" />`将控制logback包下的所有类的日志的打印，但是并没用设置打印级别，所以继承他的上级`<root>`的日志级别“DEBUG”；
没有设置addtivity，默认为true，将此loger的打印信息向上级传递；
没有设置appender，此loger本身不打印任何信息。

`<logger name="logback.LogbackDemo" level="INFO" additivity="false">`控制logback.LogbackDemo类的日志打印，打印级别为“INFO”；
additivity属性为false，表示此loger的打印信息不再向上级传递，
指定了名字为“STDOUT”的appender。
 
`<root level="DEBUG">`将root的打印级别设置为“ERROR”，指定了名字为“STDOUT”的appender。
当执行logback.LogbackDemo类的main方法时，先执行`<logger name="logback.LogbackDemo" level="INFO" additivity="false">`，将级别为“INFO”及大于“INFO”的日志信息交给此loger指定的名为“STDOUT”的appender处理，在控制台中打出日志，不再向次loger的上级 `<logger name="logback"/>` 传递打印信息；
`<logger name="logback"/>`未接到任何打印信息，当然也不会给它的上级root传递任何打印信息；

打印结果如下：  

```
14:05:35.937 [main] INFO  logback.LogbackDemo - ======info  
14:05:35.937 [main] WARN  logback.LogbackDemo - ======warn  
14:05:35.937 [main] ERROR logback.LogbackDemo - ======error  
```

如果将`<logger name="logback.LogbackDemo" level="INFO" additivity="false">`修改为 `<logger name="logback.LogbackDemo" level="INFO" additivity="true">`那打印结果将是什么呢？
没错，日志打印了两次，想必大家都知道原因了，因为打印信息向上级传递，logger本身打印一次，root接到后又打印一次
打印结果如下：  

```
14:09:01.531 [main] INFO  logback.LogbackDemo - ======info  
14:09:01.531 [main] INFO  logback.LogbackDemo - ======info  
14:09:01.531 [main] WARN  logback.LogbackDemo - ======warn  
14:09:01.531 [main] WARN  logback.LogbackDemo - ======warn  
14:09:01.531 [main] ERROR logback.LogbackDemo - ======error  
14:09:01.531 [main] ERROR logback.LogbackDemo - ======error  
```


#### `<appender>`配置

`<appender>`是`<configuration>`的子节点，是负责写日志的组件。
`<appender>`有两个必要属性name和class。name指定appender名称，class指定appender的全限定名。


##### ConsoleAppender
把日志添加到控制台，有以下子节点：
`<encoder>`：对日志进行格式化。（具体参数稍后讲解 ）
`<target>`：字符串 System.out 或者 System.err ，默认 System.out ；
例如：

```xml
<configuration>  
  
  <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">  
    <encoder>  
      <pattern>%-4relative [%thread] %-5level %logger{35} - %msg %n</pattern>  
    </encoder>  
  </appender>  
  
  <root level="DEBUG">  
    <appender-ref ref="STDOUT" />  
  </root>  
</configuration>  
```

##### FileAppender
把日志添加到文件，有以下子节点：
`<file>`：被写入的文件名，可以是相对目录，也可以是绝对目录，如果上级目录不存在会自动创建，没有默认值。
`<append>`：如果是 true，日志被追加到文件结尾，如果是 false，清空现存文件，默认是true。
`<encoder>`：对记录事件进行格式化。（具体参数稍后讲解 ）
`<prudent>`：如果是 true，日志会被安全的写入文件，即使其他的FileAppender也在向此文件做写入操作，效率低，默认是 false。
例如：

```xml
<configuration>  
  
  <appender name="FILE" class="ch.qos.logback.core.FileAppender">  
    <file>testFile.log</file>  
    <append>true</append>  
    <encoder>  
      <pattern>%-4relative [%thread] %-5level %logger{35} - %msg%n</pattern>  
    </encoder>  
  </appender>  
          
  <root level="DEBUG">  
    <appender-ref ref="FILE" />  
  </root>  
</configuration>  
```

##### RollingFileAppender
滚动记录文件，先将日志记录到指定文件，当符合某个条件时，将日志记录到其他文件。有以下子节点：
`<file>`：被写入的文件名，可以是相对目录，也可以是绝对目录，如果上级目录不存在会自动创建，没有默认值。
`<append>`：如果是 true，日志被追加到文件结尾，如果是 false，清空现存文件，默认是true。
`<encoder>`：对记录事件进行格式化。（具体参数稍后讲解 ）
`<rollingPolicy>`:当发生滚动时，决定 RollingFileAppender 的行为，涉及文件移动和重命名。
`<triggeringPolicy >`: 告知 RollingFileAppender 合适激活滚动。
`<prudent>`：当为true时，不支持FixedWindowRollingPolicy。支持TimeBasedRollingPolicy，但是有两个限制，1不支持也不允许文件压缩，2不能设置file属性，必须留空。

rollingPolicy:

**TimeBasedRollingPolicy：** 最常用的滚动策略，它根据时间来制定滚动策略，既负责滚动也负责出发滚动。有以下子节点：
`<fileNamePattern>`:
必要节点，包含文件名及“%d”转换符， “%d”可以包含一个Java.text.SimpleDateFormat指定的时间格式，如：%d{yyyy-MM}。如果直接使用 %d，默认格式是 yyyy-MM-dd。RollingFileAppender 的file字节点可有可无，通过设置file，可以为活动文件和归档文件指定不同位置，当前日志总是记录到file指定的文件（活动文件），活动文件的名字不会改变；如果没设置file，活动文件的名字会根据fileNamePattern 的值，每隔一段时间改变一次。“/”或者“\”会被当做目录分隔符。
 
`<maxHistory>`:
可选节点，控制保留的归档文件的最大数量，超出数量就删除旧文件。假设设置每个月滚动，且`<maxHistory>`是6，则只保存最近6个月的文件，删除之前的旧文件。注意，删除旧文件是，那些为了归档而创建的目录也会被删除。

**FixedWindowRollingPolicy：** 
根据固定窗口算法重命名文件的滚动策略。有以下子节点：
`<minIndex>`:窗口索引最小值
`<maxIndex>`:窗口索引最大值，当用户指定的窗口过大时，会自动将窗口设置为12。
`<fileNamePattern >`:
必须包含“%i”例如，假设最小值和最大值分别为1和2，命名模式为 mylog%i.log,会产生归档文件mylog1.log和mylog2.log。还可以指定文件压缩选项，例如，mylog%i.log.gz 或者 没有log%i.log.zip

triggeringPolicy:
**SizeBasedTriggeringPolicy：**查看当前活动文件的大小，如果超过指定大小会告知RollingFileAppender 触发当前活动文件滚动。只有一个节点:
`<maxFileSize>`:这是活动文件的大小，默认值是10MB。

例如：每天生成一个日志文件，保存30天的日志文件。

```xml
<configuration>   
  <appender name="FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">   
      
    <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">   
      <fileNamePattern>logFile.%d{yyyy-MM-dd}.log</fileNamePattern>   
      <maxHistory>30</maxHistory>    
    </rollingPolicy>   
   
    <encoder>   
      <pattern>%-4relative [%thread] %-5level %logger{35} - %msg%n</pattern>   
    </encoder>   
  </appender>    
   
  <root level="DEBUG">   
    <appender-ref ref="FILE" />   
  </root>   
</configuration>  
```


例如：按照固定窗口模式生成日志文件，当文件大于20MB时，生成新的日志文件。窗口大小是1到3，当保存了3个归档文件后，将覆盖最早的日志。

```xml
<configuration>   
  <appender name="FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">   
    <file>test.log</file>   
   
    <rollingPolicy class="ch.qos.logback.core.rolling.FixedWindowRollingPolicy">   
      <fileNamePattern>tests.%i.log.zip</fileNamePattern>   
      <minIndex>1</minIndex>   
      <maxIndex>3</maxIndex>   
    </rollingPolicy>   
   
    <triggeringPolicy class="ch.qos.logback.core.rolling.SizeBasedTriggeringPolicy">   
      <maxFileSize>5MB</maxFileSize>   
    </triggeringPolicy>   
    <encoder>   
      <pattern>%-4relative [%thread] %-5level %logger{35} - %msg%n</pattern>   
    </encoder>   
  </appender>   
           
  <root level="DEBUG">   
    <appender-ref ref="FILE" />   
  </root>   
</configuration>  
```

另外还有SocketAppender、SMTPAppender、DBAppender、SyslogAppender、SiftingAppender，并不常用，这些就不在这里讲解了，大家可以参考官方文档。当然大家可以编写自己的Appender。

`<encoder>：`
负责两件事，一是把日志信息转换成字节数组，二是把字节数组写入到输出流。
目前PatternLayoutEncoder 是唯一有用的且默认的encoder ，有一个`<pattern>`节点，用来设置日志的输入格式。使用“%”加“转换符”方式，如果要输出“%”，则必须用“\”对“\%”进行转义。

例如：
```xml
<encoder>   
   <pattern>%-4relative [%thread] %-5level %logger{35} - %msg%n</pattern>   
</encoder>
```

格式修饰符，与转换符共同使用：
可选的格式修饰符位于“%”和转换符之间。
第一个可选修饰符是左对齐 标志，符号是减号“-”；接着是可选的最小宽度 修饰符，用十进制数表示。如果字符小于最小宽度，则左填充或右填充，默认是左填充（即右对齐），填充符为空格。如果字符大于最小宽度，字符永远不会被截断。最大宽度 修饰符，符号是点号"."后面加十进制数。如果字符大于最大宽度，则从前面截断。点符号“.”后面加减号“-”在加数字，表示从尾部截断。
例如：%-4relative 表示，将输出从程序启动到创建日志记录的时间 进行左对齐 且最小宽度为4。


#### <filter>

过滤器，执行一个过滤器会有返回个枚举值，即DENY，NEUTRAL，ACCEPT其中之一。返回DENY，日志将立即被抛弃不再经过其他过滤器；返回NEUTRAL，有序列表里的下个过滤器过接着处理日志；返回ACCEPT，日志会被立即处理，不再经过剩余过滤器。
过滤器被添加到`<Appender>` 中，为`<Appender>` 添加一个或多个过滤器后，可以用任意条件对日志进行过滤。`<Appender>` 有多个过滤器时，按照配置顺序执行。

下面是几个常用的过滤器：

**LevelFilter：** 级别过滤器，根据日志级别进行过滤。如果日志级别等于配置级别，过滤器会根据onMath 和 onMismatch接收或拒绝日志。有以下子节点：
`<level>`:设置过滤级别
`<onMatch>`:用于配置符合过滤条件的操作
`<onMismatch>`:用于配置不符合过滤条件的操作
 
例如：将过滤器的日志级别配置为INFO，所有INFO级别的日志交给appender处理，非INFO级别的日志，被过滤掉。

```xml
<configuration>   
  <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">   
    <filter class="ch.qos.logback.classic.filter.LevelFilter">   
      <level>INFO</level>   
      <onMatch>ACCEPT</onMatch>   
      <onMismatch>DENY</onMismatch>   
    </filter>   
    <encoder>   
      <pattern>   
        %-4relative [%thread] %-5level %logger{30} - %msg%n   
      </pattern>   
    </encoder>   
  </appender>   
  <root level="DEBUG">   
    <appender-ref ref="CONSOLE" />   
  </root>   
</configuration>  
```

**ThresholdFilter：**
临界值过滤器，过滤掉低于指定临界值的日志。当日志级别等于或高于临界值时，过滤器返回NEUTRAL；当日志级别低于临界值时，日志会被拒绝。
例如：过滤掉所有低于INFO级别的日志。
```xml
<configuration>   
  <appender name="CONSOLE"   
    class="ch.qos.logback.core.ConsoleAppender">   
    <!-- 过滤掉 TRACE 和 DEBUG 级别的日志-->   
    <filter class="ch.qos.logback.classic.filter.ThresholdFilter">   
      <level>INFO</level>   
    </filter>   
    <encoder>   
      <pattern>   
        %-4relative [%thread] %-5level %logger{30} - %msg%n   
      </pattern>   
    </encoder>   
  </appender>   
  <root level="DEBUG">   
    <appender-ref ref="CONSOLE" />   
  </root>   
</configuration>  
```

**EvaluatorFilter：**
求值过滤器，评估、鉴别日志是否符合指定条件。有一下子节点：
`<evaluator>`:
鉴别器，常用的鉴别器是JaninoEventEvaluato，也是默认的鉴别器，它以任意的Java布尔值表达式作为求值条件，求值条件在配置文件解释过成功被动态编译，布尔值表达式返回true就表示符合过滤条件。evaluator有个子标签`<expression>`，用于配置求值条件。
`<onMatch>`:用于配置符合过滤条件的操作
`<onMismatch>`:用于配置不符合过滤条件的操作

例如：过滤掉所有日志消息中不包含“billing”字符串的日志。

```xml
<configuration>   
   
  <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">   
    <filter class="ch.qos.logback.core.filter.EvaluatorFilter">         
      <evaluator> <!-- 默认为 ch.qos.logback.classic.boolex.JaninoEventEvaluator -->   
        <expression>return message.contains("billing");</expression>   
      </evaluator>   
      <OnMatch>ACCEPT </OnMatch>  
      <OnMismatch>DENY</OnMismatch>  
    </filter>   
    <encoder>   
      <pattern>   
        %-4relative [%thread] %-5level %logger - %msg%n   
      </pattern>   
    </encoder>   
  </appender>   
   
  <root level="INFO">   
    <appender-ref ref="STDOUT" />   
  </root>   
</configuration>  
```

`<matcher>` ：
匹配器，尽管可以使用String类的matches()方法进行模式匹配，但会导致每次调用过滤器时都会创建一个新的Pattern对象，为了消除这种开销，可以预定义一个或多个matcher对象，定以后就可以在求值表达式中重复引用。`<matcher>`是`<evaluator>`的子标签。
`<matcher>`中包含两个子标签，一个是`<name>`，用于定义matcher的名字，求值表达式中使用这个名字来引用matcher；另一个是`<regex>`，用于配置匹配条件。

```xml
<configuration debug="true">   
   
  <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">   
    <filter class="ch.qos.logback.core.filter.EvaluatorFilter">   
      <evaluator>           
        <matcher>   
          <Name>odd</Name>   
          <!-- filter out odd numbered statements -->   
          <regex>statement [13579]</regex>   
        </matcher>   
           
        <expression>odd.matches(formattedMessage)</expression>   
      </evaluator>   
      <OnMismatch>NEUTRAL</OnMismatch>   
      <OnMatch>DENY</OnMatch>   
    </filter>   
    <encoder>   
      <pattern>%-4relative [%thread] %-5level %logger - %msg%n</pattern>   
    </encoder>   
  </appender>   
   
  <root level="DEBUG">   
    <appender-ref ref="STDOUT" />   
  </root>   
</configuration>  
```


#### 参考

[从Log4j迁移到LogBack的理由](https://www.oschina.net/translate/reasons-to-prefer-logbak-over-log4j)
[logback logback.xml常用配置详解](http://blog.csdn.net/haidage/article/details/6794540)



