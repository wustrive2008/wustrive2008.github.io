title: List与Map的遍历过程中删除元素
date: 2015-9-18 21:51:54
categories: java
tags: [java,list,map,Arraylist,删除,HashMap]
photos:
- http://7xifb5.com1.z0.glb.clouddn.com/wustrive-hexojava知识结构.png
---

在日常的开发过程中，经常需要对List或Map里面的符合某种业务的数据进行删除，但是如果不了解里面的机制就容易掉入“陷阱”导致遗漏或者程序异常。
<!--more-->

### List遍历过程中删除元素

#### 使用索引下标遍历的方式
示例：删除列表中的2
``` java
public static void main(String[] args) {
        List<Integer> list = new ArrayList<Integer>();
        list.add(1);
        list.add(2);
        list.add(2);
        list.add(3);
        list.add(4);
        
        for (int i = 0; i < list.size(); i++) {
            if(2 == list.get(i)){
                list.remove(i);
            }
            System.out.println(list.get(i));
        }
        
        System.out.println("list=" + list.toString());
        
    }

```

输出结果：
```
1
2
3
4
list=[1, 2, 3, 4]
```

问题：
结果显示只删除了一个2，另一个2被遗漏了，原因是：删除了第一个2后，集合里的元素个数减1，后面的元素往前移了1位，导致了第二个2被遗漏了。

#### 使用For循环遍历的方式
示例：
``` java
public static void listIterator2(){
        List<Integer> list = new ArrayList<Integer>();
        list.add(1);
        list.add(2);
        list.add(2);
        list.add(3);
        list.add(4);
        
        for (int value : list) {
            if(2 == value){
                list.remove(value);
            }
            System.out.println(value);
        }
        
        System.out.println("list=" + list.toString());
        
    }
```

结果：
``` java
Exception in thread "main" 1
2
java.util.ConcurrentModificationException
    at java.util.ArrayList$Itr.checkForComodification(Unknown Source)
    at java.util.ArrayList$Itr.next(Unknown Source)
    at test.ListIterator.listIterator2(ListIterator.java:39)
    at test.ListIterator.main(ListIterator.java:10)

```

说明：
jdk中对ConcurrentModificationException的描述:
public class ConcurrentModificationException extends 
RuntimeException当方法检测到对象的并发修改，但不允许这种修改时，抛出此异常。
例如，某个线程在 Collection 上进行迭代时，通常不允许另一个线性修改该 Collection。通常在这些情况下，迭代的结果是不确定的。如果检测到这种行为，一些迭代器实现（包括 JRE 提供的所有通用 collection 实现）可能选择抛出此异常。执行该操作的迭代器称为快速失败 迭代器，因为迭代器很快就完全失败，而不会冒着在将来某个时间任意发生不确定行为的风险。
注意，此异常不会始终指出对象已经由不同 线程并发修改。如果单线程发出违反对象协定的方法调用序列，则该对象可能抛出此异常。例如，如果线程使用快速失败迭代器在 collection 上迭代时直接修改该 collection，则迭代器将抛出此异常。
注意，迭代器的快速失败行为无法得到保证，因为一般来说，不可能对是否出现不同步并发修改做出任何硬性保证。快速失败操作会尽最大努力抛出 ConcurrentModificationException。因此，为提高此类操作的正确性而编写一个依赖于此异常的程序是错误的做法，正确做法是：ConcurrentModificationException 应该仅用于检测 bug。

Java中的For each实际上使用的是iterator进行处理的。而iterator是不允许集合在iterator使用期间删除的。所以导致了iterator抛出了ConcurrentModificationException 。

#### **正确的方式**
示例：
```java
public static void listIterator3(){
        List<Integer> list = new ArrayList<Integer>();
        list.add(1);
        list.add(2);
        list.add(2);
        list.add(3);
        list.add(4);
        
        Iterator<Integer> it = list.iterator();
        while (it.hasNext()){
            Integer value = it.next();
            if (2 == value) {
                it.remove();
            }
            
            System.out.println(value);
        }
        
        System.out.println("list=" + list.toString());
    }
```

结果：
```
1
2
2
3
4
list=[1, 3, 4]

```

### Map遍历过程中删除元素
正确做法的示例：
```java
    public static void main(String[] args) {
        HashMap<String, String> map = new HashMap<String, String>();
        map.put("1", "test1");
        map.put("2", "test2");
        map.put("3", "test3");
        map.put("4", "test4");
        
        //完整遍历Map
        for (Entry<String, String> entry : map.entrySet()) {
            System.out.printf("key: %s value:%s\r\n", entry.getKey(), entry.getValue());
        }
        
        //删除元素
        Iterator<Map.Entry<String, String>> it = map.entrySet().iterator(); 
        while(it.hasNext())
        { 
            Map.Entry<String, String> entry= it.next(); 
            String key= entry.getKey(); 
            int k = Integer.parseInt(key);
            if(k%2==1)
            { 
                System.out.printf("delete key:%s value:%s\r\n", key, entry.getValue());
                it.remove(); 
            } 
        } 
        
        //完整遍历Map
        for (Entry<String, String> entry : map.entrySet()) {
            System.out.printf("key: %s value:%s\r\n", entry.getKey(), entry.getValue());
        }
    }

```

结果：
``` java
key: 1 value:test1
key: 2 value:test2
key: 3 value:test3
key: 4 value:test4
delete key:1 value:test1
delete key:3 value:test3
key: 2 value:test2
key: 4 value:test4
```

### 注意
但对于iterator的remove()方法，也有需要我们注意的地方：
1. 每调用一次iterator.next()方法，只能调用一次remove()方法。
2. 调用remove()方法前，必须调用过一次next()方法。

JDK-API中对于remove()方法的描述：
void remove()从迭代器指向的集合中移除迭代器返回的最后一个元素（可选操作）。每次调用 next 只能调用一次此方法。如果进行迭代时用调用此方法之外的其他方式修改了该迭代器所指向的集合，则迭代器的行为是不明确的。
抛出：UnsupportedOperationException - 如果迭代器不支持 remove 操作。IllegalStateException - 如果尚未调用 next 方法，或者在上一次调用 next 方法之后已经调用了remove 方法。

### 参考
[遍历的时候删除List](http://blog.csdn.net/longyulu/article/details/8315068)
