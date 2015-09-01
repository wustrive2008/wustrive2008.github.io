title: java常见内存溢出(OOM)解决方案
date: 2015-9-1 20:55:06
categories: java
tags: [java,内存,OOM,内存溢出]
photos:
- http://7xifb5.com1.z0.glb.clouddn.com/wustrive-hexojava知识结构.png
---

### jvm内存区域
1. 程序计数器
    一块很小的内存空间，作用是当前线程所执行的字节码的行号指示器。
2.  java栈
    与程序计数器一样，java栈（虚拟机栈）也是线程私有的，其生命周期与线程相同。通常存放基本数据类型，对象引用（一个指向对象起始地址的引用指针或一个代表对象的句柄），reeturnAddress类型（指向一条字节码指令的地址）

    栈区域有两种异常类型：如果线程请求的栈深度大于虚拟机所允许的深度，将抛StrackOverflowError异常；如果虚拟机栈可以动态扩展（大部分虚拟机都可动态扩展），当扩展时无法申请到足够的内存时会抛出OutOfMemoryError异常。
3.  本地方法栈
    与虚拟机栈作用很相似，区别是虚拟机栈为虚拟机执行java方法服务，而本地方法栈则是为虚拟机用到的Native方法服务。和虚拟机栈一样可能抛出StackOverflowError和OutOfMemoryError异常。
4.  java堆
    java Heap是jvm所管理的内存中最大的区域。JavaHeap是被所有线程共享的一块内存区域，在虚拟机启动时创建。主要存放对象实例。JavaHeap是垃圾收集器管理的主要区域，其可细分为新生代和老年代。如果在堆中没有内存完成实例分配，并且也无法再扩展时，会抛出OutOfMemoryError异常。
5.  方法区
    与javaHeap一样是各个线程共享的内存区域，用于存放已被虚拟机加载的类信息、常量、静态变量、及时编译器编译后的代码等数据。当方法区无法满足内存分配的需求时，将抛出OutOfMemoryError异常。方法同时包含常听说的运行时常量池，用于存放编译期生成的各种字面量和符号引用。
6.  直接内存
    直接内存并不是虚拟机运行时数据区的一部分，也不是java虚拟机规范中定义的内存区域，是jvm外部的内存区域，这部分区域也可能导致OutOfMemoryError异常。

### jvm参数

-Xss（StackSpace）栈空间

-Xms ，-Xmx（heap memory space）堆空间：Heap是大家最为熟悉的区域，他是jvm用来存储对象实例的区域，Heap在32位的系统中最大为2G，其大小通过-Xms和-Xmx来控制，-Xms为jvm启动时申请的最小Heap内存，默认为物理内存的1/64，但小于1G，-Xmx为jvm可申请的最大的Heap内存，默认为物理内存的1/4,一般也小于1G，默认当空余堆内存小于40%时，jvm会最大Heap的大小到-Xmx指定大小，可通过-XX:MinHeapFreeRatio来指定这个比例，当空余堆内存大于70%时，JVM会将Heap的大小往-Xms指定的大小调整，可通过-XX:MaxHeapFreeRatio来指定这个比例，但通常为了避免频繁调整HeapSize的大小，将-Xms和-Xmx的值设为相同。

-XX:PermSize  -XX:MaxPermSize：方法区持久代大小：方法区域也是全局共享的，在一定的条件下它也会被 GC，当方法区域需要使用的内存超过其允许的大小时，会抛出 OutOfMemory的错误信息。

### 常见内存溢出错误解决办法

1. OutOfMemoryError异常
    除了程序计数器外，虚拟机内存的其他几个运行时区域都有发生OutOfMemoryError(OOM)异常的可能，

    Java Heap 溢出

    一般的异常信息：java.lang.OutOfMemoryError:Java heap spacess

    java堆用于存储对象实例，我们只要不断的创建对象，并且保证GC Roots到对象之间有可达路径来避免垃圾回收机制清除这些对象，就会在对象数量达到最大堆容量限制后产生内存溢出异常。

    出现这种异常，一般手段是先通过内存映像分析工具(如Eclipse Memory Analyzer)对dump出来的堆转存快照进行分析，重点是确认内存中的对象是否是必要的，先分清是因为内存泄漏(Memory Leak)还是内存溢出(Memory Overflow)。

    如果是内存泄漏，可进一步通过工具查看泄漏对象到GC Roots的引用链。于是就能找到泄漏对象时通过怎样的路径与GC Roots相关联并导致垃圾收集器无法自动回收。

    如果不存在泄漏，那就应该检查虚拟机的参数(-Xmx与-Xms)的设置是否适当。
2. 虚拟机栈和本地方法栈溢出
    如果线程请求的栈深度大于虚拟机所允许的最大深度，将抛出StackOverflowError异常。

    如果虚拟机在扩展栈时无法申请到足够的内存空间，则抛出OutOfMemoryError异常

    这里需要注意当栈的大小越大可分配的线程数就越少。
3. 运行时常量池溢出
    异常信息：java.lang.OutOfMemoryError:PermGen space

    如果要向运行时常量池中添加内容，最简单的做法就是使用String.intern()这个Native方法。该方法的作用是：如果池中已经包含一个等于此String的字符串，则返回代表池中这个字符串的String对象；否则，将此String对象包含的字符串添加到常量池中，并且返回此String对象的引用。由于常量池分配在方法区内，我们可以通过-XX:PermSize和-XX:MaxPermSize限制方法区的大小，从而间接限制其中常量池的容量。
4. 方法区溢出
   方法区用于存放Class的相关信息，如类名、访问修饰符、常量池、字段描述、方法描述等。

    异常信息：java.lang.OutOfMemoryError:PermGen space

    方法区溢出也是一种常见的内存溢出异常，一个类如果要被垃圾收集器回收，判定条件是很苛刻的。在经常动态生成大量Class的应用中，要特别注意这点。

### 参考
《深入理解java虚拟机》