title: HashMap遍历方式及其性能
date: 2015-8-11 19:46:49
categories: java
tags: [java,hashmap,性能]
photos:
- http://7xifb5.com1.z0.glb.clouddn.com/wustrive-hexojava知识结构.png
---

### HashMap的四种遍历方式
1. for each map.entrySet()
```
Map<String, String> map = new HashMap<String, String>();
for (Entry<String, String> entry : map.entrySet()) {
    entry.getKey();
    entry.getValue();
}

```

2. 调用map.entrySet()的集合迭代器 
```
Iterator<Map.Entry<String, String>> iterator = map.entrySet().iterator();
while (iterator.hasNext()) {
    Map.Entry<String, String> entry = iterator.next();
    entry.getKey();
    entry.getValue();
}

```

3.  for each map.keySet()，再调用get获取
```
Map<String, String> map = new HashMap<String, String>();
for (String key : map.keySet()) {
    map.get(key);
}

```

4. for each map.entrySet()，用临时变量保存map.entrySet()
```
Set<Entry<String, String>> entrySet = map.entrySet();
for (Entry<String, String> entry : entrySet) {
    entry.getKey();
    entry.getValue();
}

```

### HashMap四种遍历方式的性能测试及对比
性能测试代码：
```
package cn.trinea.java.test;

import java.text.DecimalFormat;
import java.util.Calendar;
import java.util.HashMap;
import java.util.Iterator;
import java.util.Map;
import java.util.Map.Entry;
import java.util.Set;
 
/**
 * JavaLoopTest
 * 
 * @author www.trinea.cn 2013-10-28
 */
public class JavaLoopTest {
 
    public static void main(String[] args) {
        System.out.print("compare loop performance of HashMap");
        loopMapCompare(getHashMaps(10000, 100000, 1000000, 2000000));
    }
 
    public static Map<String, String>[] getHashMaps(int... sizeArray) {
        Map<String, String>[] mapArray = new HashMap[sizeArray.length];
        for (int i = 0; i < sizeArray.length; i++) {
            int size = sizeArray[i];
            Map<String, String> map = new HashMap<String, String>();
            for (int j = 0; j < size; j++) {
                String s = Integer.toString(j);
                map.put(s, s);
            }
            mapArray[i] = map;
        }
        return mapArray;
    }
 
    public static void loopMapCompare(Map<String, String>[] mapArray) {
        printHeader(mapArray);
        long startTime, endTime;
 
        // Type 1
        for (int i = 0; i < mapArray.length; i++) {
            Map<String, String> map = mapArray[i];
            startTime = Calendar.getInstance().getTimeInMillis();
            for (Entry<String, String> entry : map.entrySet()) {
                entry.getKey();
                entry.getValue();
            }
            endTime = Calendar.getInstance().getTimeInMillis();
            printCostTime(i, mapArray.length, "for each entrySet", endTime - startTime);
        }
 
        // Type 2
        for (int i = 0; i < mapArray.length; i++) {
            Map<String, String> map = mapArray[i];
            startTime = Calendar.getInstance().getTimeInMillis();
            Iterator<Map.Entry<String, String>> iterator = map.entrySet().iterator();
            while (iterator.hasNext()) {
                Map.Entry<String, String> entry = iterator.next();
                entry.getKey();
                entry.getValue();
            }
            endTime = Calendar.getInstance().getTimeInMillis();
            printCostTime(i, mapArray.length, "for iterator entrySet", endTime - startTime);
        }
 
        // Type 3
        for (int i = 0; i < mapArray.length; i++) {
            Map<String, String> map = mapArray[i];
            startTime = Calendar.getInstance().getTimeInMillis();
            for (String key : map.keySet()) {
                map.get(key);
            }
            endTime = Calendar.getInstance().getTimeInMillis();
            printCostTime(i, mapArray.length, "for each keySet", endTime - startTime);
        }
 
        // Type 4
        for (int i = 0; i < mapArray.length; i++) {
            Map<String, String> map = mapArray[i];
            startTime = Calendar.getInstance().getTimeInMillis();
            Set<Entry<String, String>> entrySet = map.entrySet();
            for (Entry<String, String> entry : entrySet) {
                entry.getKey();
                entry.getValue();
            }
            endTime = Calendar.getInstance().getTimeInMillis();
            printCostTime(i, mapArray.length, "for entrySet=entrySet()", endTime - startTime);
        }
    }
 
    static int                 FIRST_COLUMN_LENGTH = 23, OTHER_COLUMN_LENGTH = 12, TOTAL_COLUMN_LENGTH = 71;
    static final DecimalFormat COMMA_FORMAT        = new DecimalFormat("#,###");
 
    public static void printHeader(Map... mapArray) {
        printRowDivider();
        for (int i = 0; i < mapArray.length; i++) {
            if (i == 0) {
                StringBuilder sb = new StringBuilder().append("map size");
                while (sb.length() < FIRST_COLUMN_LENGTH) {
                    sb.append(" ");
                }
                System.out.print(sb);
            }
 
            StringBuilder sb = new StringBuilder().append("| ").append(COMMA_FORMAT.format(mapArray[i].size()));
            while (sb.length() < OTHER_COLUMN_LENGTH) {
                sb.append(" ");
            }
            System.out.print(sb);
        }
        TOTAL_COLUMN_LENGTH = FIRST_COLUMN_LENGTH + OTHER_COLUMN_LENGTH * mapArray.length;
        printRowDivider();
    }
 
    public static void printRowDivider() {
        System.out.println();
        StringBuilder sb = new StringBuilder();
        while (sb.length() < TOTAL_COLUMN_LENGTH) {
            sb.append("-");
        }
        System.out.println(sb);
    }
 
    public static void printCostTime(int i, int size, String caseName, long costTime) {
        if (i == 0) {
            StringBuilder sb = new StringBuilder().append(caseName);
            while (sb.length() < FIRST_COLUMN_LENGTH) {
                sb.append(" ");
            }
            System.out.print(sb);
        }
 
        StringBuilder sb = new StringBuilder().append("| ").append(costTime).append(" ms");
        while (sb.length() < OTHER_COLUMN_LENGTH) {
            sb.append(" ");
        }
        System.out.print(sb);
 
        if (i == size - 1) {
            printRowDivider();
        }
    }
}


```
PS：如果运行报异常in thread “main” java.lang.OutOfMemoryError: Java heap space，请将main函数里面map size的大小减小。
其中getHashMaps函数会返回不同size的HashMap。
loopMapCompare函数会分别用上面的遍历方式1-4去遍历每一个map数组(包含不同大小HashMap)中的HashMap。
print开头函数为输出辅助函数，可忽略。

测试环境为Windows7 32位系统 3.2G双核CPU 4G内存，Java 7，Eclipse -Xms512m -Xmx512m
最终测试结果如下：

```
compare loop performance of HashMap
-----------------------------------------------------------------------
map size               | 10,000    | 100,000   | 1,000,000 | 2,000,000 
-----------------------------------------------------------------------
for each entrySet      | 2 ms      | 6 ms      | 36 ms     | 91 ms     
-----------------------------------------------------------------------
for iterator entrySet  | 0 ms      | 4 ms      | 35 ms     | 89 ms     
-----------------------------------------------------------------------
for each keySet        | 1 ms      | 6 ms      | 48 ms     | 126 ms    
-----------------------------------------------------------------------
for entrySet=entrySet()| 1 ms      | 4 ms      | 35 ms     | 92 ms     
-----------------------------------------------------------------------
```
表横向为同一遍历方式不同大小HashMap遍历的时间消耗，纵向为同一HashMap不同遍历方式遍历的时间消耗。
PS：由于首次遍历HashMap会稍微多耗时一点，for each的结果稍微有点偏差，将测试代码中的几个Type顺序调换会发现，for each entrySet耗时和for iterator entrySet接近。

### HashMap遍历方式结果分析
从上面知道for each与显示调用Iterator等价，上表的结果中可以看出除了第三种方式(for each map.keySet())，再调用get获取方式外，其他三种方式性能相当。本例还是hash值散列较好的情况，若散列算法较差，第三种方式会更加耗时。
我们看看HashMap entrySet和keySet的源码
```
private final class KeyIterator extends HashIterator<K> {
    public K next() {
        return nextEntry().getKey();
    }
}
 
private final class EntryIterator extends HashIterator<Map.Entry<K,V>> {
    public Map.Entry<K,V> next() {
        return nextEntry();
    }
}
```
分别是keySet()和entrySet()返回的set的迭代器，从中我们可以看到只是返回值不同而已，父类相同，所以性能相差不多。只是第三种方式多了一步根据key get得到value的操作而已。get的时间复杂度根据hash算法而异，源码如下：

```
public V get(Object key) {
    if (key == null)
        return getForNullKey();
    Entry<K,V> entry = getEntry(key);
 
    return null == entry ? null : entry.getValue();
}
 
/**
 * Returns the entry associated with the specified key in the
 * HashMap.  Returns null if the HashMap contains no mapping
 * for the key.
 */
final Entry<K,V> getEntry(Object key) {
    int hash = (key == null) ? 0 : hash(key);
    for (Entry<K,V> e = table[indexFor(hash, table.length)];
         e != null;
         e = e.next) {
        Object k;
        if (e.hash == hash &&
            ((k = e.key) == key || (key != null && key.equals(k))))
            return e;
    }
    return null;
}
```
get的时间复杂度取决于for循环循环次数，即hash算法。

### 结论总结
从上面的分析来看：
a. HashMap的循环，如果既需要key也需要value，直接用
```
Map<String, String> map = new HashMap<String, String>();
for (Entry<String, String> entry : map.entrySet()) {
    entry.getKey();
    entry.getValue();
}
```
即可，foreach简洁易懂。

b. 如果只是遍历key而无需value的话，可以直接用
```
Map<String, String> map = new HashMap<String, String>();
for (String key : map.keySet()) {
    // key process
}
```

参考：[HashMap循环遍历方式及其性能对比](http://www.trinea.cn/android/hashmap-loop-performance/)