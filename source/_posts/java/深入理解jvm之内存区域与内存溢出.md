title: 深入理解jvm之内存区域与内存溢出
date: 2015-12-24 21:21:29
categories: java
tags: [java,jvm,虚拟机,性能,HotSpot]
photos:
- http://7xifb5.com1.z0.glb.clouddn.com/wustrive-hexojava知识结构.png
---

## Java内存区域与内存溢出异常 

### 运行时数据区域
![java虚拟机内存分区](http://7xifb5.com1.z0.glb.clouddn.com/wustrive-blog%2Fjava%E8%99%9A%E6%8B%9F%E6%9C%BA%E5%86%85%E5%AD%98%E5%88%86%E5%8C%BA.jpg)

#### 程序计数器

- 当前线程所执行的字节码的行号指示器
- 当前线程私有
- 不会出现OutOfMemoryError情况

#### java虚拟机栈

- 线程私有，生命周期与线程相同
- java方法执行的内存模型，每个方法执行的同时都会创建一个栈帧，存储局部变量表(基本类型、对象引用)、操作数栈、动态链接、方法出口等信息
- StackOverflowError异常：当线程请求的栈深度大于虚拟机所允许的深度
- OutOfMemoryError异常：如果栈的扩展时无法申请到足够的内存

#### 本地方法栈
与虚拟机栈相似，主要为虚拟机使用到的Native方法服务，在HotSpot虚拟机中直接把本地方法栈与虚拟机栈二合一

#### Java堆(Java Heap)
java堆是被所有线程共享的一块内存区域，在虚拟机启动时创建。此区域的唯一目的就是存储对象实例。java堆是垃圾收集器管理的主要区域。java堆还可以细分为：新生代与老年代。在细一点有Eden空间、Form Survivor空间、To Survivor空间等。

- 可以通过-Xmx和-Xms控制堆的大小
- OutOfMemoryError异常：当在堆中没有内存完成实例分配，且堆也无法再扩展时。

#### 方法区

- 线程间共享
- 用于存储已被虚拟机加载的类信息、常量、静态变量、即时编译器编译后的代码等数据
- OutOfMemoryError异常：当方法区无法满足内存的分配需求时

#### 运行时常量池

- 方法区的一部分
- 用于存放编译期生成的各种字面量与符号引用
- OutOfMemoryError异常：当常量池无法再申请到内存时

#### 直接内存

- NIO可以使用Native函数库直接分配堆外内存，堆中的DirectByteBuffer对象作为这块内存的引用进行操作
- 大小不受Java堆大小的限制，受本机(服务器)内存限制
- OutOfMemoryError异常：系统内存不足时

### HotSpot虚拟机

#### 对象的创建
虚拟机遇到一条new指令时，首先将去检查这个对象的参数是否在常量池中定位到一个类的符号引用，并且检查这个符号引用代表的类是否已被加载、解析和初始化过。如果没有，必须先执行类的加载过程。
在类加载检查通过后，虚拟机将为新生对象分配内存。对象所需内存大小再类加载完成后便可确定。内存分配可以采用“指针碰撞”与“空闲列表”的方式。

#### 对象的访问定位
java程序需要通过栈上的reference数据来操作堆上的具体对象。访问方式有使用句柄和直接指针两种。

- 句柄访问 java堆中将会划分出一块内存来作为句柄池，reference中存储的就是对象的句柄地址，而句柄中包含了对象实例数据与类型数据各自的具体地址信息
- 直接指针访问 java堆对象的布局中必须考虑如何放置访问类型数据的相关信息，reference中存储的就是对象地址


### OOM异常的解决思路
生成Dump快照文件：

- 通过jvm参数--XX:-HeapDumpOnOutOfMemoryError可以让JVM在出现内存溢出是Dump出当前的内存转储快照
- 用jmap生产dump文件，win通过任务管理器查看tomcat的进程pid，linux用ps命令查看进程pid,然后用jmap命令

先通过内存映像分析工具(如Eclipse的Memory Analyzer)进行分析，常见的情况有：

- 内存泄露，对象已经死了，无法通过垃圾收集器进行自动回收，通过找出泄露的代码位置和原因，才好确定解决方案；
- 内存溢出，内存中的对象都还必须存活着，这说明Java堆分配空间不足，检查堆设置大小（-Xmx与-Xms），检查代码是否存在对象生命周期太长、持有状态时间过长的情况。

OOM异常示例：
```
package oom;

import java.util.ArrayList;
import java.util.List;

/**
 * VM Args: -Xms20m -Xmx20m -XX:+HeapDumpOnOutOfMemoryError
* @ClassName: HeapOOM 
*
 */
public class HeapOOM {
    static class OOMObject{
        
    }
    
    public static void main(String[] args) {
        List<OOMObject> list = new ArrayList<OOMObject>();
        while(true){
            list.add(new OOMObject());
        }
    }
}

```

### 参考
《深入理解java虚拟机 JVM高级特性与最佳实践》