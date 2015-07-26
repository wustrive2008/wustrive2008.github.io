title: "java集合总结"
date: 2015-06-07 21:46:12
categories: java
tags: [java,集合]
---

## List
### ArrayList

1. 以数组实现，有容量限制，超出限制时会自动扩充容量，为避免不必要的复制操作，使用时尽量给出数组的预估大小。
2. 按数组小标随机访问(get(i)/set(i,e))的性能很高，删除元素性能低，适合数据比较固定，随机访问操作远多于删除操作的列表

### LinkedList
1. 以双向链表实现，无容量限制，但双向链表本身使用了更多空间
2. 按下标访问元素，需要遍历链表移动指针
3. 插入删除元素的效率高，因为只需要修改操作节点的前后节点指针

### CopyOnWriteArrayList
1. 实现并发的ArrayList，使用CopyOnWrite策略，在修改时先复制一个快照来修改，改完后再让内部指针指向新数组，因为对快照的操作对读操作不可见，所以只有写锁没有读锁，比较适合读多写少的场景。如果更新频率较高，或数组较大时，还是Collections.synchronizedList(list)，对所有操作用同一把锁来保证线程安全更好。
2. 增加了addIfAbsent(e)方法，会遍历数组来检查元素是否已存在，性能不太好

## Map
### HashMap
1. 以Entry[]数组实现的哈希桶数组，用key的哈希值取模桶数组的大小可得到数组下标。
2. 插入元素时，如果两个key落在同一个桶，Entry用一个next属性实现多个Entry以单向链表方式存放，后入桶的Entry将next指向桶当前的Entry。查找哈希值为17的key时，先定位到第一个哈希桶，然后以链表遍历所有元素，逐个比较其key值
3. 当Entry数量达到桶数量的75%时，会成倍扩容桶数组，并重新分配所有原来Entry。
4. 在JDK8里，新增默认为8的閥值，当一个桶里的Entry超过8个，就不以单向链表而以红黑树来存放以加快查找速度。

### LinkedHashMap
1. 扩展HashMap增加双向链表实现，支持iterator()时按Entry的插入顺序来排序(只算插入不算更新， 如果设置accessOrder属性为true，则所有读写访问都算)
2. 实现上是在Entry上再增加属性before/after指针，插入时把自己加到Header Entry的前面去。如果所有读写访问都要排序，还要把前后Entry的before/after拼接起来以在链表中删除掉自己。

### TreeMap
1. 以红黑树实现,支持iterator()时按Key值排序，可按实现了Comparable接口的Key的自然顺序升序排序，或由传入的Comparator控制。可想象的，在树上插入/删除元素的代价一定比HashMap的大。
2. 支持SortedMap接口，如firstKey()，lastKey()取得最大最小的key，或sub(fromKey, toKey), tailMap(fromKey)剪取Map的某一段。

### ConcurrentHashMap
1. 并发优化的HashMap，默认16把写锁(可以设置更多),有效分散了阻塞的概率，而且没有读锁
2. 数据结构为Segment[]，Segment里面才是哈希桶数组，每个Segment一把锁。Key先算出它在哪个Segment里，再算出它在哪个哈希桶里。
3. 支持ConcurrentMap接口，如putIfAbsent(key，value)与相反的replace(key，value)与以及实现CAS的replace(key, oldValue, newValue)。
4. 没有读锁是因为put/remove动作是个原子动作(比如put是一个对数组元素/Entry 指针的赋值操作)，因此read不会看到一个更新动作的中间状态。

### ConcurrentSkipListMap
JDK6新增的并发优化的SortedMap，以SkipList实现。SkipList是红黑树的一种简化替代方案，是个很流行的有序集合算法，见入门教程。Concurrent包选用它是因为它支持基于CAS的无锁算法，而红黑树则没有好的无锁算法。
很特殊的，它的size()不能随便调，会遍历来统计。

## Set 
> (Set几乎都是内部用一个Map来实现, 因为Map里的KeySet就是一个Set，而value全部使用同一个Object。Set的特征也继承了那些内部Map实现的特征。)

1. HashSet：内部是HashMap。
2. LinkedHashSet：内部是LinkedHashMap。
3. TreeSet：内部是TreeMap的SortedSet。
4. ConcurrentSkipListSet：内部是ConcurrentSkipListMap的并发优化的SortedSet。
5. CopyOnWriteArraySet：内部是CopyOnWriteArrayList的并发优化的Set，利用其addIfAbsent()方法实现元素去重，如前所述该方法的性能很一般。

## Queue
Queue是在两端出入的List，所以也可以用数组或链表来实现。
### LinkedList
是的，以双向链表实现的LinkedList既是List，也是Queue。它是唯一一个允许放入null的Queue。

### ArrayDeque
1. 以循环数组实现的双向Queue。大小是2的倍数，默认是16。
2. 普通数组只能快速在末尾添加元素，为了支持FIFO，从数组头快速取出元素，就需要使用循环数组：有队头队尾两个下标：弹出元素时，队头下标递增；加入元素时，如果已到数组空间的末尾，则将元素循环赋值到数组[0],同时队尾下标指向0，再插入下一个元素则赋值到数组[1]，队尾下标指向1。如果队尾的下标追上队头，说明数组所有空间已用完，进行双倍的数组扩容。

### 线程安全的队列
1. ConcurrentLinkedQueue/ConcurrentLinkedDeque
无界的并发优化的Queue，基于链表，实现了依赖于CAS的无锁算法
2. PriorityBlockingQueue
无界的并发优化的PriorityQueue，也是基于二分堆。使用一把公共的读写锁。虽然实现了BlockingQueue接口，其实没有任何阻塞队列的特征，空间不够时会自动扩容。
3. DelayQueue
内部包含一个PriorityQueue，同样是无界的。元素需实现Delayed接口，每次调用时需返回当前离触发时间还有多久，小于0表示该触发了。
pull()时会用peek()查看队头的元素，检查是否到达触发时间。ScheduledThreadPoolExecutor用了类似的结构。

### 线程安全的阻塞队列
1. BlockingQueue的队列长度受限，用以保证生产者与消费者的速度不会相差太远，避免内存耗尽。队列长度设定后不可改变。当入队时队列已满，或出队时队列已空，不同函数的效果见下表：<table><tr><td></td><td>可能报异常</td><td>返回布尔值</td><td>可能阻塞等待</td>
<td>可设定等待时间</td></tr><tr><td>入队</td><td>add(e)</td><td>offer(e)</td><td>put(e)</td><td>offer(e, timeout, unit)</td></tr><tr><td>出队</td><td>remove()</td><td>poll()</td><td>take()</td><td>poll(timeout, unit)</td>
</tr><tr><td>查看</td><td>element()</td><td>peek()</td><td>无</td><td>无</td>
</tr></table>
2. ArrayBlockingQueue
定长的并发优化的BlockingQueue，基于循环数组实现。也由一把公共读写锁与notFull、notEmpty两个Condition管理阻塞状态。
3. LinkedBlockingQueue
可选定长的并发优化的BlockingQueue，基于链表实现，所以可以把长度设为Integer.MAX_VALUE。有takeLock、putLock两把锁及notFull、notEmpty两个Condition精细复杂的管理阻塞状态。

### 参考
[关于Java集合的小抄](http://calvin1978.blogcn.com/articles/collection.html)