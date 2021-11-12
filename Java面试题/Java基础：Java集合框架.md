# Java基础（一）：Java集合框架

## Java 集合框架

早在 Java 2 中之前，Java 就提供了特设类。比如：Dictionary，Vector，Stack，和 Properties 这些类用来存储和操作对象组。

虽然这些类都非常有用，但是它们缺少一个核心的，统一的主题。由于这个原因，使用 Vector 类的方式和使用 Properties 类的方式有着很大不同。

集合框架被设计成要满足以下几个目标。

* 该框架必须是高性能的。基本集合（动态数组、链表、树、哈希表）的实现也必须是高效的。
* 该集合允许不同类型的集合，用类似的方式工作，具有高度的互操作性。
* 对一个集合的扩展和适应必须是简单的。

为此，整个集合框架就围绕一组标准接口而设计。你可以直接使用这些接口的标准实现，诸如：**LinkedList，HashSet，和 TreeSet**等，除此之外你也可以通过这些接口实现直接的集合。

![img](https://imgconvert.csdnimg.cn/aHR0cHM6Ly93d3cucnVub29iLmNvbS93cC1jb250ZW50L3VwbG9hZHMvMjAxNC8wMS8yMjQzNjkwLTljZDljODk2ZTBkNTEyZWQuZ2lm)

从上面的集合框架可以看到，Java 集合框架主要包括两种类型的容器，一种是集合（Collection），存储一个元素集合，另一种是图（Map），存储键/值对映射。Collection 接口又有3种子类型，List、Set 和 Queue，再下面是一些抽象类，最后是具体实现类，常用的有 ArrayList、LinkedList、HashSet、LinkedHashSet、HashMap、LinkedHashMap 等等。

集合框架是一个用来代表和操纵集合的统一架构。所有的集合框架都包含以下内容：

* **接口：**是代表集合的抽象数据类型。例如 Collection、List、Set、Map 等。之所以定义多个接口，是为了以不同的方式操作集合对象
* **实现（类）：**是集合接口的具体实现。从本质上讲，它们是可重复使用的数据结构，例如：ArrayList、LinkedList、HashSet、HashMap。
* **算法：**是实现集合接口的对象里的方法执行的一些有用的计算，例如：搜索和排序。这些算法被称为多态，那是因为相同的方法可以在相似的接口上有着不同的实现。

除了集合，该框架也定义了几个 Map 接口和类。Map 里存储的是键/值对。尽管 Map 不是集合，但是它们完全整合在集合中。

### 集合框架体系如图所示

![img](https://imgconvert.csdnimg.cn/aHR0cHM6Ly93d3cucnVub29iLmNvbS93cC1jb250ZW50L3VwbG9hZHMvMjAxNC8wMS9qYXZhLWNvbGwucG5n?x-oss-process=image/format,png)

Java 集合框架提供了一套性能优良、使用方便的接口和类，Java 集合框架位于 java.util 包中，所以当使用集合框架的时候需要进行导包。

## 集合接口

集合框架定义了一些接口。本节提供了每个接口的概述：

| 接口            | 接口描述                                                     |
| --------------- | ------------------------------------------------------------ |
| Collection 接口 | Collection 是最基本的集合接口，一个Collection 代表一组 Object，即 Collection 的元素，Java 不提供直接继承 Collection 的类，只提供继承于的子接口（如 List 和 Set）。Collection 接口存储一组不唯一，无序的对象。 |
| List 接口       | List 接口是一个有序的 Collection，使用此接口能够精准的控制每个元素插入的位置，能够通过索引（元素在 List 中位置，类似于数组的下标）来访问 List 中的元素，第一个元素的索引为 0，而且允许有相同的元素。List 接口存储一组不唯一，有序（插入顺序）的对象。 |
| Set             | Set 具有与 Collection 完全一样的接口，只是行为上不同，Set 不保存重复的元素。Set 接口存储一组唯一，无序的对象。 |
| SortedSet       | SortedSet 继承于 Set 保存有序的集合。                        |
| Map             | Map 接口存储一组键值对象，提供 key（键） 到 value（值）的映射。 |
| Map.Entry       | Map.Entry 描述在一个 Map 中的一个元素（键/值对）。是一个 Map 的内部类。 |
| SortedMap       | SortedMap 继承于 Map，使 Key 保持在升序排列。                |
| Enumeration     | 这是一个传统的接口和定义的方法，通过它可以枚举（一次获得一次）对象集合中的元素。这个传统接口已被迭代器取代。 |

### Set和List的区别

1. Set 接口实例存储的是无序的，不重复的数据。List 接口实例存储的是有序的，可以重复的元素。
2. Set 检索效率低下，删除和插入效率高，插入和删除不会引起元素位置改变**<实现类有HashSet，TreeSet>**。
3. List 和数组类似，可以动态增长，根据实际存储的数据的长度自动增长 List 的长度。查找元素效率高，插入删除效率低，因为会引起其他元素位置改变**<实现类有ArrayList，LinkedList，Vector>**。

## 集合实现类（集合类）

Java 提供了一套实现了 Collection 接口的标准集合类。其中一些是具体类，这些类可以直接拿到使用，而另外一些是抽象类，提供了接口的部分实现。

标准集合类汇总于下表：

| 类                         | 类描述                                                       |
| -------------------------- | ------------------------------------------------------------ |
| **AbstractCollection**     | 实现了大部分的集合接口。                                     |
| **AbstractList**           | 继承于 AbstractCollection 并且实现了大部分 List 接口。       |
| **AbstractSequentialList** | 继承于 AbstractList，提供了对数据元素的链式访问而不是随机访问。 |
| LinkedList                 | 该类实现了 List 接口，允许有null（空）元素。主要用于创建链表数据结构，该类没有同步方法，如果多个线程同时访问一个 List ，则必须自己实现访问同步，解决方法就是在创建 List 时候构造一个同步的 List。例如：`List list = Collections.synchronizedList(newLinkedList(...));` LinkedList 查找效率低。 |
| ArrayList                  | 该类也是实现了 List 的接口，实现了可变大小的数组，随机访问和遍历元素时，提供更好的性能。该类也是非同步的，在多线程的情况下不要使用。ArrayList 增长当前长度的50%，插入删除效率低。 |
| **AbstractSet**            | 继承于 AbstractCollection 并且实现了大部分 Set 接口。        |
| HashSet                    | 该类实现了 Set 接口，不允许出现重复元素，不保证集合中元素的顺序，允许包含值为null的元素，但最多只能一个。 |
| LinkedHashSet              | 具有可预知迭代顺序的`Set`接口的哈希表和链接列表实现。        |
| TreeSet                    | 该类实现了Set 接口，可以实现排序等功能。                     |
| **AbstractMap**            | 实现了大部分的Map接口                                        |
| HashMap                    | HashMap 是一个散列表，它存储的内容是键值对（key-value）映射。该类实现了 Map 接口，根据键的HashCode值存储数据，具有很快的访问速度，最多允许一条记录的键为null，不支持线程同步 |
| TreeMap                    | 继承了AbstractMap，并且使用了一棵树。                        |
| WeakHashMap                | 继承AbstractMap，使用弱密钥的哈希表。                        |
| LinkedHashMap              | 继承于HashMap，使用元素的自然顺序对元素进行排序              |
| IdentityHashMap            | 继承AbstractMap类，比较文档时使用引用相等。                  |

在前面的教程中已经讨论通过了java.util包中定义的类，如下所示：

| 类         | 类描述                                                       |
| ---------- | ------------------------------------------------------------ |
| Vector     | 该类与ArrayList非常相似，但是该类是同步的，可以用在多线程的情况，该类允许设置默认的增长长度，默认扩展方式为原来的两倍。 |
| Stack      | 栈是Vector的一个子类，它实现了一个标准的后进先出的栈。       |
| Dictionary | Dictionary类是一个抽象类，用来存储键/值对，作用和Map类相似。 |
| Hashtable  | Hashtable是Dictionary（字典）类的子类，位于java.util包中。   |
| Properties | Properties继承于Hashtable，表示一个持久的属性集，属性列表中每个键及其对应值都是一个字符串。 |
| BitSet     | 一个BitSet类创建一种特殊类型的数组来保存位值。BitSet中数组大小会随需要增加。 |

## 集合算法

集合框架定义了几种算法，可用于集合和映射。这些算法被定义为集合类的静态方法。

在尝试比较不兼容的类型时，一些方法能够抛出 ClassCastException异常。当试图修改一个不可修改的集合时，抛出UnsupportedOperationException异常。

集合定义三个静态的变量：EMPTY_SET，EMPTY_LIST，EMPTY_MAP的。这些变量都不可改变。

| 算法                  | 算法描述                       |
| --------------------- | ------------------------------ |
| Collection Algorithms | 这里是一个列表的所有算法实现。 |

## 如何使用迭代器

通常情况下，你会希望遍历一个集合中的元素。例如，显示集合的每个元素。

一般遍历数组都是采用for循环或者增强for，这两个方法也可以用在集合框架，但是还有一种方法是采用迭代器遍历集合框架，它是一个对象，实现了iterator接口或ListIterator接口。

迭代器，使你能够通过循环来得到或删除集合的元素。ListIterator继承了Iterator，以允许双向遍历列表和修改元素。

| 迭代器        | 迭代器方法描述                                               |
| ------------- | ------------------------------------------------------------ |
| Java Iterator | 使用Java Iterator 这里通过实例列出Iterator和ListIterator接口提供的所有方法。 |

### 遍历 ArrayList

```java
import java.util.*;

public class Test{
    public static void main(String[] args) {
        List<String> list = new ArrayList<String>();
        list.add("Hello");
        list.add("World");
        list.add("HAHAHA");
        //第一种遍历方法使用For-Each 遍历 List
        for(String str : list) {
            System.out.println(str);
        }
        
        //第二种遍历，把链表变成数组相关的内容进行遍历
        String[] strArray = new String[list.size()];
        list.toArray(strArray);
        for(int i = 0;i < strArray.length;i++) {
            System.out.println(strArray[i]);
        }
        
        //第三种遍历 使用迭代器进行相关遍历
        Iterator<String> ite = list.iterator();
        while(ite.hasNext()) {
            System.out.println(ite.next());
        }
    }
}
```

**解析：**

三种方法都是用来遍历ArrayList集合，第三种方法是采用迭代器的方法，该方法可以不用担心在遍历的过程中会超出集合的长度。

### 遍历 Map

```java
import java.util.*;

public class Test {
    public static void main(String[] args) {
        Map<String, String> map = new HashMap<String,String>();
        map.put("1","value1");
        map.put("2","value2");
        map.put("3","value3");
        
        //第一种：普通使用，二次取值
        System.out.println("通过Map.KeySet遍历key和value：");
        for(String key : map.keySet()) {
            System.out.println("key= " + key + " and value= " + map.get(key));
        }
        
        //第二种
        System.out.println("通过Map.entrySet使用iterator遍历key和value：");
        Iterator<Map.Entry<String, String>> it = map.entrySet().iterator();
        while(it.hasNext()) {
            Map.Entry<String, String> entry = it.next();
            System.out.println("key= " + entry.getKey() + " and value= " + entry.getValue());
        }
        
        //第三种：推荐，尤其是容量大时
        System.out.println("通过Map.entrySet遍历Key和value");
        for(Map.Entry<String, String> entry : map.entrySet()) {
            System.out.println("key= " + entry.getKey() + " and value= " + entry.getValue());
        }
        
        //第四种
        System.out.println("通过Map.values()遍历所有的value，但不能遍历key");
        for(String v : map.values()) {
            System.out.println("value= " + v);
        }
    }
}
```

## 如何使用比较器

TreeSet和TreeMap的按照排序顺序来存储元素。然而，这是通过比较器来精确定义按照什么样的排序顺序。

这个接口可以让我们以不同的方式来排序一个集合。

| 序号 | 比较器方法描述                                               |
| ---- | ------------------------------------------------------------ |
| 1    | 使用Java Comparator 这里通过实例列出Comparator接口提供的所有方法 |

## 总结

Java 集合框架为程序员提供了预先包装的数据结构和算法来操纵他们。

集合时一个对象，可容纳其他对象的引用。集合接口声明对每一种类型的集合可以执行的操作。

集合框架的类和接口均在java.util包中。

任何对象加入集合类后，自动转变为Object类型，所以在取出的时候，需要进行强制类型转换。



# Java基础（二）：迭代器（Iterator）

### Java Iterator（迭代器）

Java Iterator（迭代器）不是一个集合，它是一种用于访问集合的方法，可用于迭代ArrayList和HashSet等集合。

Iterator是Java迭代器最简单的实现，ListIterator是Collection API中的接口，它扩展了Iterator接口。

![img](https://imgconvert.csdnimg.cn/aHR0cHM6Ly93d3cucnVub29iLmNvbS93cC1jb250ZW50L3VwbG9hZHMvMjAyMC8wNy9MaXN0SXRlcmF0b3ItQ2xhc3MtRGlhZ3JhbS5qcGc?x-oss-process=image/format,png)

迭代器it的两个基本操作是next、hasNext和remove。

调用it.next()会返回迭代器的下一个元素，并且更新迭代器的状态。

调用it.hasNext()用于检测集合中是否还有元素。

调用it.remove()将迭代器返回的元素删除。

Iterator类位于java.util包中，使用前需要引入它，语法格式如下：

```java
import java.util.Iterator;
```

### 获取一个迭代器

集合想获取一个迭代器可以使用iterator()方法：

```java
import java.util.ArrayList;
import java.util.Iterator;

public class RunoobTest {
    public static void main(String[] args) {
        //创建集合
        ArrayList<String> sites = new ArrayList<String>();
        sites.add("Google");
        sites.add("Runoob");
        sites.add("Taobao");
        sites.add("Zhihu");
        
        //获取迭代器
        Iterator<String> it = sites.iterator();
        
        //输出集合中的第一个元素
        System.out.println(it.next());
    }
}
```

执行以上代码，输出结果如下：

```
Google
```

### 循环集合元素

让迭代器it逐个返回集合中所有元素最简单的方法是使用while循环：

```java
while(it.hasNext()) {
    System.out.println(it.next());
}
```

以下输出集合sites中的所有元素：

```java
import java.util.ArrayList;
import java.util.Iterator;

public class RunoobTest {
    public static void main(String[] args) {
        //创建集合 
        ArrayList<String> sites = new ArrayList<String>();
        sites.add("Google");
        sites.add("Runoob");
        sites.add("Taobao");
        sites.add("Zhihu");
        
        //获取迭代器
        Iterator<String> it = sites.iterator();
        
        //输出集合中的所有元素
        while(it.hasNext()) {
            System.out.println(it.next());
        }
    }
}
```

执行以上代码，输出结果如下：

```
Google
Runoob
Taobao
Zhihu
```

删除元素

要删除集合中的元素可以使用remove()方法。

以下实例我们删除集合中小于10的元素：

```java
import java.util.ArrayList;
import java.util.Iterator;

public class RunoobTest {
    public static void main(String[] args) {
        ArrayList<Integer> numbers = new ArrayList<Integer>();
        numbers.add(12);
        numbers.add(8);
        numbers.add(2);
        numbers.add(23);
        Iterator<Integer> it = numbers.iterator();
        while(it.hasNext()) {
            Integer i = it.next();
            if(i < 10) {
                it.remove();
            }
        }
        System.out.println(numbers);
    }
}
```

执行以上代码，输出结果如下：

```
[12,23]
```

# Java基础（三）：LinkedList

## Java LinkedList

链表（Linked list）是一种常见的基础数据结构，是一种线性表，但是并不会按线性的顺序存储数据，而是在每一个节点里存到下一个节点的地址。

链表可分为单向链表和双向链表。

一个单向链表包含两个值：当前节点的值和一个指向下一个节点的链接。

![img](https://imgconvert.csdnimg.cn/aHR0cHM6Ly93d3cucnVub29iLmNvbS93cC1jb250ZW50L3VwbG9hZHMvMjAyMC8wNi80MDhweC1TaW5nbHktbGlua2VkLWxpc3Quc3ZnXy5wbmc?x-oss-process=image/format,png)

一个双向链表有三个整数值：数值、向后的节点链接、向前的节点链接。

![img](https://imgconvert.csdnimg.cn/aHR0cHM6Ly93d3cucnVub29iLmNvbS93cC1jb250ZW50L3VwbG9hZHMvMjAyMC8wNi82MTBweC1Eb3VibHktbGlua2VkLWxpc3Quc3ZnXy5wbmc?x-oss-process=image/format,png)

Java LinkedList（链表）类似于ArrayList，是一种常见的数据容器。

与ArrayList相比，LinkedList的增加和删除对操作效率更高，而查找和修改的操作效率较低。

**以下情况使用ArrayList：**

* 频繁访问列表中的某一个元素。
* 只需要在列表末尾进行添加和删除元素操作。

**以下情况使用LinkedList：**

* 你需要通过循环迭代来访问列表中的某些元素。
* 需要频繁的在列表开头、中间、末尾等位置进行添加和删除元素操作。

LinkedList继承了AbstractSequentialList类。

LinkedList实现了Queue接口，可作为队列使用。

LinkedList实现了List接口，可进行列表的相关操作。

LinkedList实现了Deque接口，可作为队列使用。

LinkedList实现了Cloneable接口，可实现克隆。

LinkedList实现了java.io.Serializable接口，即可支持序列化，能通过序列化去传输。

![img](https://imgconvert.csdnimg.cn/aHR0cHM6Ly93d3cucnVub29iLmNvbS93cC1jb250ZW50L3VwbG9hZHMvMjAyMC8wNi8yMDE5MDMyODE2NDczNy5wbmc?x-oss-process=image/format,png)

LinkedList类位于java.util包中，使用前需要引入它，语法格式如下：

```java
import java.util.LinkedList;

//普通创建方法
LinkedList<E> list = new LinkedList<E>();

//使用集合创建链表
LinkedList<E> list = new LinkedList(Collection<? extends E> c);
```

### 创建一个简单的链表实例：

```java
import java.util.LinkedList;

public class RunoobTest {
    public static void main(String[] args) {
        LinkedList<String> sites = new LinkedList<String>();
        sites.add("Google");
        sites.add("Runoob");
        sites.add("Taobao");
        sites.add("Weibo");
        System.out.println(sites);
    }
}
```

以上实例，执行输出结果为：

```
[Google,Runoob,Taobao,Weibo]
```

更多的情况下我们使用ArrayList访问列表中的随机元素更加高效，但以下几种情况LinkedList提供了更高效的方法。

在列表开头添加元素：

```java
import java.util.LinkedList;

public class RunoobTest {
    public static void main(String[] args) {
        LinkedList<String> sties = new LinkedList<String>();
        sites.add("Google");
        sites.add("Runoob");
        sites.add("Taobao");
        
        sites.addFirst("Wiki");
        System.out.println(sites);
    }
}
```

以上实例，执行输出结果为：

```
[Wiki,Google,Runoob,Taobao]
```

在列表结尾添加元素：

```java
import java.util.LinkedList;

public class RunoobTest {
    public static void main(String[] args) {
        LinkedList<String> sites = new LinkedList<String>();
        sites.add("Google");
        sites.add("Runoob");
        sites.add("Taobao");
        
        sites.addLast("Wiki");
        System.out.println(sites);
    }
}
```

以上实例，执行输出结果为：

```
[Google,Runoob,Taobao,Wiki]
```

在列表开头移除元素：

```java
import java.util,LinkedList;

public class RunoobTest {
    public static void main(String[] args) {
        LinkedList<String> sites = new LinkedList<String>();
        sites.add("Google");
        sites.add("Runoob");
        sites.add("Taobao");
        sites.add("Weibo");
        
        sites.removeFirst();
        System.out.println(sites);
    }
}
```

以上实例，执行输出结果为：

```
[Runoob,Taobao,Weibo]
```

在列表结尾移除元素：

```java
import java.util,LinkedList;

public class RunoobTest {
    public static void main(String[] args) {
        LinkedList<String> sites = new LinkedList<String>();
        sites.add("Google");
        sites.add("Runoob");
        sites.add("Taobao");
        sites.add("Weibo");
        
        sites.removeLast();
        System.out.println(sites);
    }
}
```

以上实例：执行输出结果为：

```
[Google,Runoob,Taobao]
```

获取列表开头的元素：

```java
import java.util,LinkedList;

public class RunoobTest {
    public static void main(String[] args) {
        LinkedList<String> sites = new LinkedList<String>();
        sites.add("Google");
        sites.add("Runoob");
        sites.add("Taobao");
        sites.add("Weibo");
        
        System.out.println(sites.getFirst());
    }
}
```

以上实例，执行输出结果为：

```
Google
```

获取列表结尾的元素：

```java
import java.util,LinkedList;

public class RunoobTest {
    public static void main(String[] args) {
        LinkedList<String> sites = new LinkedList<String>();
        sites.add("Google");
        sites.add("Runoob");
        sites.add("Taobao");
        sites.add("Weibo");
        
        System.out.println(sites.getLast());
    }
}
```

以上实例，执行输出结果为：

```
Weibo
```

### 迭代元素

**我们可以使用for配合size()方法来迭代列表中的元素：**

```java
import java.util.LinkedList;

public class RunoobTest {
    public static void main(String[] args) {
        LinkedList<String> sites = new LinkedList<String>();
        sites.add("Google");
        sites.add("Runoob");
        sites.add("Taobao");
        sites.add("Weibo");
        
        for(int size = sites.size(),i=0;i < size;i++) {
            System.out.println(sites.get(i));
        }
    }
}
```

size()方法用于计算链表的大小。

以上实例，执行输出结果为：

```
Google
Runoob
Taobao
Weibo
```

**也可以使用for-each来迭代元素：**

```java
import java.util.LinkedList;

public class RunoobTest {
    public static void main(String[] args) {
        LinkedList<String> sites = new LinkedList<String>();
        sites.add("Google");
        sites.add("Runoob");
        sites.add("Taobao");
        sites.add("Weibo");
        
        for(String i : sites) {
            System.out.println(i);
        }
    }
}
```

以上实例，执行输出结果为：

```
Google
Runoob
Taobao
Weibo
```

### 常用方法

| 方法                                          | 描述                                             |
| --------------------------------------------- | ------------------------------------------------ |
| public boolean add(E e)                       | 链表末尾添加元素，返回是否成功                   |
| public void add(int index,E e)                | 向指定位置插入元素                               |
| public boolean addAll(Collection c)           | 将一个集合的所有元素添加到链表后面，返回是否成功 |
| public boolean addAll(int index,Collection c) | 将一个集合的所有元素添加到链表的指定位置后面     |
| public void addFirst(E e)                     | 元素添加到头部                                   |
| public void addLast(E e)                      | 元素添加到尾部                                   |
| public boolean offer(E e)                     | 向链表末尾添加元素，返回是否成功                 |
| public boolean offerFirst(E e)                | 头部插入元素，返回是否成功                       |
| public boolean offerLast(E e)                 | 尾部插入元素，返回是否成功                       |
| public void clear()                           | 清空链表                                         |
| public E removeFirst()                        | 删除并返回第一个元素                             |
| public E removeLast()                         | 删除并返回最后一个元素                           |
| public boolean remove(Object o)               | 删除某一元素，返回是否成功                       |
| public E remove(int index)                    | 删除指定位置的元素                               |
| public E poll()                               | 删除并返回第一个元素                             |
| public E remove()                             | 删除并返回第一个元素                             |
| public boolean contains(Object o)             | 判断是否含有某一个元素                           |
| public E get(int index)                       | 返回指定位置的元素                               |
| public E getFirst()                           | 返回第一个元素                                   |
| public E getLast()                            | 返回最后一个元素                                 |
| public int indexOf(Object o)                  | 查找指定元素从前往后第一次出现的索引             |
| public int lastIndexOf(Object o)              | 查找指定元素最后一次出现的索引                   |
| public E peek()                               | 返回第一个元素                                   |
| public E element()                            | 返回第一个元素                                   |
| public E peekFirst()                          | 返回头部元素                                     |
| public E peekLast()                           | 返回尾部元素                                     |
| public E set(int index,E e)                   | 设置指定位置的元素                               |
| public Object clone()                         | 克隆该列表                                       |
| public Iterator descendingIterator()          | 返回倒序迭代器                                   |
| public int size()                             | 返回链表元素个数                                 |
| public ListIterator(int index)                | 返回从指定位置开始到末尾的迭代器                 |
| public Object[] toArray()                     | 返回一个由链表元素组成的数组                     |

# Java基础（四）：ArrayList

## Java ArrayList

ArrayList类是一个可以动态修改的数组，与普通数组的区别就是它是没有固定大小的限制，我们可以添加或删除元素。

ArrayList继承了AbstractList，并实现了List接口。

![img](https://imgconvert.csdnimg.cn/aHR0cHM6Ly93d3cucnVub29iLmNvbS93cC1jb250ZW50L3VwbG9hZHMvMjAyMC8wNi9hcnJheWxpc3QucG5n?x-oss-process=image/format,png)

ArrayList类位于java.util包中，使用前需要引入它，语法格式如下：

```java
import java.util.ArrayList;
ArrayList<E> objectName = new ArrayList<>();
```

* E：泛型数据类型，用于设置objectName的数据类型，**只能为引用数据类型**。
* **objectName**：对象名。

ArrayList是一个数组队列，提供了相关的添加、删除、修改、遍历等功能。

### 添加元素

ArrayList类提供了很多有用的方法，添加元素到ArrayList可以使用add()方法：

```java
import java.util.ArrayList;

public class RunoobTest {
    public static void main(String[] args) {
        ArrayList<String> sites = new ArrayList<String>();
        sites.add("Google");
        sites.add("Runoob");
        sites.add("Taobao");
        sites.add("Weibo");
        System.out.println(sites);
    }
}
```

以上实例，执行输出结果为：

```
[Google,Runoob,Taobao,Weibo]
```

### 访问元素

访问ArrayList中的元素可以使用get()方法：

```java
import java.util.ArrayList;

public class RunoobTest {
        public static void main(String[] args) {
        ArrayList<String> sites = new ArrayList<String>();
        sites.add("Google");
        sites.add("Runoob");
        sites.add("Taobao");
        sites.add("Weibo");
        System.out.println(sites.get(1));
    }
}
```

注意：数组的索引值从0开始。

以上实例，执行输出结果为：

```
Runoob
```

### 修改元素

如果要修改ArrayList中的元素可以使用set()方法：

```java
import java.util.ArrayList;

public class RunoobTest {
        public static void main(String[] args) {
        ArrayList<String> sites = new ArrayList<String>();
        sites.add("Google");
        sites.add("Runoob");
        sites.add("Taobao");
        sites.add("Weibo");
        sites.set(2,"Wiki");
            
        System.out.println(sites);
    }
}
```

以上实例，执行输出结果为：

```
[Google,Runoob,Wiki,Weibo]
```

### 删除元素

如果要删除ArrayList中的元素可以使用remove()方法：

```java
import java.util.ArrayList;

public class RunoobTest {
        public static void main(String[] args) {
        ArrayList<String> sites = new ArrayList<String>();
        sites.add("Google");
        sites.add("Runoob");
        sites.add("Taobao");
        sites.add("Weibo");
        sites.remove(3);
            
        System.out.println(sites);
    }
}
```

以上实例，执行输出结果为：

```
[Google,Runoob,Taobao]
```

### 计算大小

如果要计算ArrayList中的元素数量可以使用size()方法：

```java
import java.util.ArrayList;

public class RunoobTest {
        public static void main(String[] args) {
        ArrayList<String> sites = new ArrayList<String>();
        sites.add("Google");
        sites.add("Runoob");
        sites.add("Taobao");
        sites.add("Weibo");
            
        System.out.println(sites.size());
    }
}
```

以上实例，执行输出结果为：

```
4
```

### 迭代数组列表

我们可以使用for来迭代数组列表中的元素：

```java
import java.util.ArrayList;

public class RunoobTest {
        public static void main(String[] args) {
        ArrayList<String> sites = new ArrayList<String>();
        sites.add("Google");
        sites.add("Runoob");
        sites.add("Taobao");
        sites.add("Weibo");
            
        for(int i = 0,i < sites.size();i++) {
            System.out.println(sites.get(i));
        }
    }
}
```

以上实例，执行输出结果为：

```
Google
Runoob
Taobao
Weibo
```

也可以使用for-each来迭代元素：

```java
import java.util.ArrayList;

public class RunoobTest {
        public static void main(String[] args) {
        ArrayList<String> sites = new ArrayList<String>();
        sites.add("Google");
        sites.add("Runoob");
        sites.add("Taobao");
        sites.add("Weibo");
        
        for(String i,sites) {
        	System.out.println(i);
        }
    }
}
```

以上实例，执行输出结果为：

```
Google
Runoob
Taobao
Weibo
```

### 其他引用类型

ArrayList中的元素实际上是对象，在以上对象中，数组列表元素都是字符串String类型。

如果我们要存储其他类型，而只能为引用数据类型，这时我们就需要使用到基本类型的包装类。

基本类型对应的包装类表如下：

| 基本类型 | 引用类型  |
| -------- | --------- |
| boolean  | Boolean   |
| byte     | Byte      |
| short    | Short     |
| int      | Inteter   |
| long     | Long      |
| float    | Float     |
| double   | Double    |
| char     | Character |

此外，BigInteger、BigDecimal用于高精度的运算，BigInteger支持任意精度的整数，也是引用类型，但它们没有相对应的基本类型。

```java
ArrayList<Integer> li = new ArrayList<>();
ArrayList<Character> li = new ArrayList<>();
```

以下实例使用ArrayList存储数字（使用Integer 类型）：

```java
import java.util.ArrayList;

public class RunoobTest {
    public static void main(String[] args) {
        ArraysList<Integer> myNumber = new ArrayList<Integer>();
        myNumbers.add(10);
        myNumbers.add(15);
        myNumbers.add(20);
        myNumbers.add(25);
        for(int i : myNumbers) {
            System.out println(i);
        }
    }
}
```

以上实例，执行输出结果为：

```
10
15
20
25
```

### ArrayList 排序

Collections类也是一个非常有用对类，位于java.util包中，提供的sort()方法可以对字符或数字列表进行排序。

以下实例对字母进行排序：

```java
import java.util.ArrayList;
import java.util.Collections;

public class RunoobTest {
    public static void main(String[] args) {
        ArrayList<String> sites = new ArrayList<String>();
        sites.add("Taobao");
        sites.add("Wiki");
        sites.add("Runoob");
        sites.add("Weibo");
        sites.add("Google");
        Collection.sort(sites);
        for(String i : sites) {
            System.out.println(i);
        }
    }
}
```

以上实例，执行输出结果为：

```
Google
Runoob
Taobao
Weibo
Wiki
```

以下实例对数字进行排序：

```java
import java.util.ArrayList;
import java.util.Collections;

public class RunoobTest {
    public static void main(String[] args) {
        ArrayList<Integer> myNumbers = new ArrayList<Integer>();
        myNumbers.add(33);
        myNumbers.add(15);
        myNumbers.add(20);
        myNumbers.add(34);
        myNumbers.add(8);
        myNumbers.add(12);
        
        Collections.sort(myNumbers);
        
        for(int i : myNumbers) {
            System.out.println(i);
        }
    }
}
```

以上实例，执行输出结果为：

```
8
12
15
20
33
34
```

# Java基础（五）：HashSet

## Java HashSet

HashSet基于HashMap来实现的，是一个不允许有重复元素的集合。

HashSet允许有Null值。

HashSet是无序的，即不会记录插入的顺序。

HashSet不是线程安全的，如果多个线程尝试同时修改HashSet，则最终结果是不确定的。您必须在多线程访问时显式同步对HashSet的并发访问。

HashSet实现了Set接口。

![img](https://imgconvert.csdnimg.cn/aHR0cHM6Ly93d3cucnVub29iLmNvbS93cC1jb250ZW50L3VwbG9hZHMvMjAyMC8wNy9qYXZhLWhhc2hzZXQtaGllcmFyY2h5LnBuZw?x-oss-process=image/format,png)

HashSet中的元素实际上是对象，一些常见的基本类型可以使用它的包装类。

HashSet类位于java.util包中，使用前需要引入它，语法格式如下：

```java
import java.util.HashSet;
```

以下实例我们创建一个HashSet对象sites，用于保存字符串元素：

```java
HashSet<String> sites = new HashSet<String>();
```

### 添加元素

HashSet类提供类很多有用的方法，添加元素可以使用add()方法：

```java
import java.util.HashSet;

public class RunoobTest {
    public static void main(String[] args) {
        HashSet<String> sites = new HashSet<String>();
        sites.add("Google");
        sites.add("Runoob");
        sites.add("Taobao");
        sites.add("Zhihu");
        sites.add("Runoob");
        
        System.out.println(sites);
    }
}
```

执行以上代码，输出结果如下：

```
[Google,Runoob,Zhihu,Taobao]
```

在上面的实例中，Runoob被添加了两次，它在集合中也只会出现一次，因为集合中的每个元素都必须是唯一的。

### 判断元素是否存在

我们可以使用contains()方法来判断元素是否存在于集合当中：

```java
import java.util.HashSet;

public class RunoobTest {
    public static void main(String[] args) {
        HashSet<String> sites = new HashSet<String>();
        sites.add("Google");
        sites.add("Runoob");
        sites.add("Taobao");
        sites.add("Zhihu");
        
        System.out.println(sites.contains("Taobao"));
    }
}
```

执行以上代码，输出结果如下：

```
true
```

### 删除元素

我们可以使用remove()方法来删除集合中的元素：

```java
import java.util.HashSet;

public class RunoobTest {
    public static void main(String[] args) {
        HashSet<String> sites = new HashSet<String>();
        sites.add("Google");
        sites.add("Runoob");
        sites.add("Taobao");
        sites.add("Zhihu");
        sites.remove("Taobao");
        
        System.out.println(sites);
    }
}
```

执行以上代码，输出结果如下：

```
[Google,Runoob,Zhihu]
```

删除集合中所有元素可以使用clear()方法：

```java
import java.util.HashSet;

public class RunoobTest {
    public static void main(String[] args) {
        HashSet<String> sites = new HashSet<String>();
        sites.add("Google");
        sites.add("Runoob");
        sites.add("Taobao");
        sites.add("Zhihu");
        
        sites.clear();
        System.out.println(sites);
    }
}
```

执行以上代码，输出结果如下：

```
[]
```

### 计算大小

如果要计算HashSet中的元素数量可以使用size()方法：

```java
import java.util.HashSet;

public class RunoobTest {
    public static void main(String[] args) {
        HashSet<String> sites = new HashSet<String>();
        sites.add("Google");
        sites.add("Runoob");
        sites.add("Taobao");
        sites.add("Zhihu");
        
        System.out.println(sites.size());
    }
}
```

执行以上代码，输出结果如下：

```
4
```

### 迭代HashSet

可以使用for-each来迭代HashSet中的元素。

```java
import java.util.HashSet;

public class RunoobTest {
    public static void main(String[] args) {
        HashSet<String> sites = new HashSet<String>();
        sites.add("Google");
        sites.add("Runoob");
        sites.add("Taobao");
        sites.add("Zhihu");
        
       	for(String i : sites) {
            System.out.println(i);
        }
    }
}
```

执行以上代码，输出结果如下：

```
Google
Runoob
Zhihu
Taobao
```

# Java基础（七）：栈 Stack

## Java Stack类

栈是Vector的一个子类，它实现了一个标准的后进先出的栈。

堆栈只定义了默认构造函数，用来创建一个空栈。堆栈除了包括由Vector定义的所有方法，也定义了这家的一些方法。

```
Stack()
```

除了由Vector定义的所有方法，自己也定义了一些方法：

| 方法                  | 方法描述                                       |
| --------------------- | ---------------------------------------------- |
| boolean empty()       | 测试堆栈是否为空                               |
| Object peek()         | 查看堆栈顶部的对象，但不从堆栈中移除它。       |
| Object pop()          | 移除堆栈顶部的对象，并作为此函数的值返回该对象 |
| Object push(Object e) | 把对象压入堆栈顶部                             |
| int search(Object e)  | 返回对象在堆栈中的位置，以1位基数              |

下面的程序说明这个集合所支持的集中方法

```java
import java.util.*;
public class StackDemo {
    
    static void showPush(Stack<Integer> st,int a) {
        st.push(new Integer(a));
        System.out.println("push(" + a + ")");
        System.out.println("stack: " + st);
    }
    
    static void showPop(Stack<Integer> st) {
        System.out.print("pop -> ");
        Integer a = (Integer) st.pop();
        System.out.println(a);
        System.out.println("stack: " + st);
    }
    
    public static void main(String[] args) {
        Stack<Integer> st = new Stack<Integer>();
        System.out.println("stack: " + st);
        showPush(st,42);
        showPush(st,66);
        showPush(st,99);
        showPop(st);
        showPop(st);
        showPop(st);
        try {
            showPop(st)
        } catch (EmptyStackException e) {
            System.out.println("empty stack");
        }
    }
}
```

以上实例编译运行结果如下：

```
stack: [ ]
push(42)
stack: [42]
push(66)
stack: [42, 66]
push(99)
stack: [42, 66, 99]
pop -> 99
stack: [42, 66]
pop -> 66
stack: [42]
pop -> 42
stack: [ ]
pop -> empty stack
```

# Java基础：详解Arrays.asList()

## 1.要点

该方法是将数组转化为List集合的方法。

List list = Arrays.asList("a","b","c");

注意：

(1) 该方法适用于对象型数据的数组（String、Integer...）

(2) 该方法不建议使用于基础数据类型的数组（byte,short,int,long,float,double,boolean）

(3) 该方法将数组与List列表链接起来：当更新其一个时，另一个自动更新

(4) 不支持add()、remove()、clear()等方法

## 2.Arrays.asList()是个坑

用此方法得到的List的长度是不可改变的，

当你向这个List添加或删除一个元素时（例如 list.add("d");）程序就会抛出异常（java.lang.UnsupportedOperationException）。怎么会这样？只需要看看asList()方法是怎么实现的就行了：

```java
public static <T> List<T> asList(T... a) {
    return new ArrayList<>(a);
}
```

当你看到这段代码时可能觉得没啥问题啊，不就是返回了一个ArrayList对象吗？问题就出在这里。

这个ArrayList不是java.util包下的，而是java.util.Arrays.ArrayList

它是Arrays类自己定义的一个静态内部类，这个内部类没有实现add()、remove()方法，而是直接使用了它的父类AbstractList的相应方法。

而AbstractList中的add()和remove()是直接抛出java.lang.UnsupportedPoerationException异常的！

```java
public void add(int index,E element) { throw new UnsupportedOperationException();}

public E remove(int index) { throw new UnsupportedOperationException();}
```

总结：

如果你的List只是用来遍历，就用Arrays.asList()。

如果你的List还要添加或删除元素，还是乖乖地new一个java.util.ArrayList，然后一个一个的添加元素。

## 示例代码

```java
import java.util.Arryas;
import java.util.List;

public class Test {
    public static void main(String[] args) {
        //1. 对象类型（String型）的数组使用asList()，正常
        String[] strings = {"aa","bb","cc"};
        List<String> stringList = Arrays.asList(strings);
        System.out.print("1、String类型数组使用asList()，正常： ");
        for(String str : stringList) {
            System.out.print(str + " ");
        }
        System.out.println();
        
        //2.对象类型（Integer）的数组使用asList，正常
        Integer[] integers = new Integer[] {1,2,3};
        List<Integer> integerList = Array.asList(integers);
        System.out.print("2、对象类型的数组使用asList()，正常： ");
        for(int i : inetegerList) {
            System.out.print(i + " ");
        }
        
        System.out.println();
        
        //3、基本数据类型的数组使用asList()，出错
        int[] ints = new int[]{1,2,3};
        List intList = Arrays.asList(ints);
        System.out.print("3、基本数据类型的数组使用asList()，出错（输出的是一个引用把ints当成一个元素了）： ");
        for(Object o : intList) {
            System.out.print(o.toString());
        }
        System.out.println();
        
        System.out.print("   " + "这样遍历才能正确输出：");
        int[] ints1 = (int[]) intList.get(0);
        for(int i : ints1) {
            System.out.print(i + " ");
        }
        System.out.println();
        
        //4、当更新数组或者list，另一个将自动获得更新
        System.out.print("4、当更新数组或者List，另一个将自动获得更新： ");
        intergerList.set(0,5);
        for(Object o : integerList) {
            System.out.print(0 + " ");
        }
        
        for(Object o : integers) {
            System.out.print(o + " ");
        }
        System.out.println();
        
        //5、add()	remove()	报错：
        System.out.print("5、add()	remove() 报错：  ");
        
    }
}
```

输出结果：

```
1、String类型数组使用asList()，正常：  aa bb cc 
2、对象类型的数组使用asList()，正常：  1 2 3 
3、基本数据类型的数组使用asList()，出错(输出的是一个引用，把ints当成一个元素了)：[I@1540e19d
   这样遍历才能正确输出：1 2 3 
4、当更新数组或者List,另一个将自动获得更新：  5 2 3 5 2 3 
5、add()、remove()、clear() 报错： 
```

```java
public static void main(String[] args) {
    int[] data = {1,2,3,4,5};
    
    List list = Arrays.asList(data);
    
    System.out.println("元素类型：" + list.get(0).getClass());
    
    System.out.println("前后是否相等：" + data.equals(list.get(0)));
}
```

可以看到，输出的为

元素类型：class [I
前后是否相等：true

因为jvm不可能输出array类型，array类型属于java.lang.reflect包，通过反射访问这个数组的这个类，编译时候生成的。所以要改为：

```java
Integer[] data = {1,2,3,4,5};

List list = Arrays.asList(data);

System.out.println("列表中的元素数量是：" + list.size());
```

输出结果：

列表中的元素数量是：5

说明编译器对Integer[]处理不一样。Integer是可变长参数。传入过程中asList()方法实际是将Integer数组里的元素进行存储。

# Java基础知识：super关键字

java中的`super`关键字是一个引用变量，用于引用直接父类对象。

每当创建子类的实例时，父类的实例被隐式创建，由`super`关键字引用变量引用。

java `super`关键字的用法如下：

* `super`可以用来引用直接父类的实例变量。
* `super`可以用来调用直接父类的方法。
* `super`可以用来调用直接父类构造函数。

## 1.super用于引用直接父类实例变量

可以使用`super`关键字来访问父类的数据成员或字段。如果父类和子类具有相同的字段，则使用`super`来指定为父类数据成员或字段。

```java
class Animal {
    String color = "white";
}

class Dog extends Animal {
    String color = "black";
    
    void printColor() {
        System.out.println(color);
        System.out.println(super.color);
    }
}

class TestSuper1 {
    public static void main(String[] args) {
        Dog d = new Dog();
        d.printColor();
    }
}
```

执行上面代码，输出结果如下：

```
black
white
```

在上面的例子中，`Animal`和`Dog`都有一个共同的属性：`color`。如果我们打印`color`属性，它将默认打印当前类的颜色。要访问父属性，需要使用`super`关键字指定。

## 2.通过super来调用父类方法

`super`关键字也可以同于调用父类方法。如果子类包含于父类相同的方法，则应使用`super`关键字指定父类的方法。换句话说，如果方法被覆盖就可以使用`super`关键字来指定父类方法。

```java
class Animal {
    void eat() {
        System.out.println("eating...");
    }
}

class Dog extends Animal {
    void eat() {
        System.out.println("eating bread...");
    }
    
    void brak() {
        System.out.println("barking...");
    }
    
    void work() {
        super.eat();
        bark();
    }
}

class TestSuper2 {
    public static void main(String[] args) {
        Dog d = new Dog();
        d.work();
    }
}
```

执行上面代码，输出结果如下：

```
eating...
barking...
```

在上面的例子中，`Animal`和`Dog`两个类都有`eat()`方法，如果要调用`Dog`类中的`eat()`方法，它将默认调用`Dog`类的`eat()`方法，因为当前类的优先级比父类的高。

所以要调用父类方法，需要使用`super`关键字指定。

## 3.使用super来调用父类构造函数

`super`关键字也可以用于调用父类构造函数。下面来看一个简单的例子：

```java
class Animal {
    Animal() {
        System.out.println("animal is created");
    }
}

class Dog extends Animal {
    Dog() {
        super();
        System.out.println("dog is created");
    }
}

class TestSuper3 {
    public static void main(String[] args) {
        Dog d = new Dog();
    }
}
```

> 注意：如果没有使用`super()`或`this()`，则`super()`在每个类构造函数中由编译器自动添加。

![img](https://img-service.csdnimg.cn/img_convert/9174f630c1c1c68b65d1c8889e3739e2.png)

我们知道，如果没有构造函数，编译器会自动提供默认构造函数。但是，它还添加了`super()`作为第一个语句。

下面是`super`关键字的另一个例子，这里`super()`由编译器隐式提供。

```java
class Animal {
    Animal() {
        System.out.println("animal is created");
    }
}

class Dog extends Animal {
    Dog() {
        System.out.println("dog is created");
    }
}

class TestSuper4 {
    public static void main(String[] args) {
        Dog d = new Dog();
    }
}
```

执行上面代码，输出结果如下：

```
animal is created
dog is created
```

## super实际使用示例

下面来看看`super`关键字的实际用法。在这里，`Emp`类继承了`Person`类，所以`Person`的所有属性都将默认继承到`Emp`。要初始化所有的属性，可使用子类的父类构造函数。这样，我们重用了父类的构造函数。

```java
class Person {
    int id;
    String name;
    
    Person(int id,String name) {
        this.id = id;
        this.name = name;
    }
}

class Emp extends Person {
    float salary;
    
    Emp(int id,String name,float salary) {
        super(id,name);
        this.salary = salary;
    }
    
    void display() {
        System.out.println(id + " " + name + " " + salary);
    }
}

class TestSuper5 {
    public static void main(String[] args) {
        Emp e1 = new Emp(1,"ankit",45000f);
        e1.display();
    }
}
```

执行上面代码，输出结果如下：

```
1 ankit 45000
```

# Java基础知识：多态

Java中的多态是一个概念，通过它我们可以通过不同的方式执行单个动作（方法）。多态性派生自2个希腊词：“`poly`”和"`morphs`"。词语"`poly`"意为许多，“`morphs`”意为形式。所以多态表示为多种形式。

在Java中由两种类型的多态性：编译时多态性和运行时多态性。我们可以通过方法重载和方法覆盖在java中执行多态性。

如果在Java中重载静态方法，它就是编译时多态性的例子。这里，我们将关注Java中的运行时多态性。

## 1.Java运行时多态性

运行时多态性或动态方法分派是一个过程，它对重写方法的调用在运行时体现而不是编译时。

在此过程中，通过超类的引用变量调用重写的方法。要调用的方法基于引用的对象。

了解运行时多态性之前，让我们先来向上转换。

**向上转换**

在父类的引用变量引用子类的对象时，称为向上转换。例如：

```java
class A{}
class B extends A{}
A a = new B();
```

**Java运行时多态性示例1**

在这个例子中，我们创建两个类：`Bike`和`Splendar`。`Splendar`类扩展`Bike`类并覆盖其`run()`方法。通过父类`Bike`的引用变量调用`run`方法。因为它引用子类对象，并且子类方法覆盖父类方法，子类方法在运行时被调用。

```java
class Bike {
    void run() {
        System.out.println("running");
    }
}

class Splender extends Bike {
    void run() {
        System.out.println("runing safely with 60km");
    }
    
    public static void main(String[] args) {
        Bike b = new Splender();
        b.run();
    }
}
```

执行上面代码得到以下结果：

```
runing safely with 60km
```

**Java运行时多态性示例2：Bank**

考虑一个情况，`Bank`类是一个提供获得利率的方法的类。但是，利率可能因银行而异。例如，`SBI`，`ICICI`，`AXIS`银行分别提供`8.4%`，`7.3%`，`9.7%`的利率。

![img](https://img-service.csdnimg.cn/img_convert/7e2c8fd7c719803c27ad275920e77a16.png)

> 注意：此示例也是方法覆盖中给出，但没有向上转换。

```java
class Bank {
    float getRateOfInterest() {
        return 0;
    }
}

class SBI extends Bank {
    float getRateOfInterest() {
        return 8.4f;
    }
}

class ICICI extends Bank {
    float getRateOfInterest() {
        return 7.3f;
    }
}

class AXIS extends Bank {
    float getRateOfInterest() {
        return 9.7f;
    }
}

class TestPolyMorphism {
    public static void main(String[] args) {
        Bank b;
        b = new SBI();
        System.out.println("SBI Rate of Interest：" + b.getRateOfInterest());
        b = new ICICI();
        System.out.println("ICICI Rate of Interest：" + b.getRateOfInterest());
        b = new AXIS();
        System.out.println("AXIS Rate of Interest：" + b.getRateOfInterest());
    }
}
```

上面代码执行结果如下：

```
SBI Rate of Interest：8.4
ICICI Rate of Interest：7.3
AXIS Rate of Interest：9.7
```

**Java运行时多态性示例3：Shape**

```java
class Shap {
    void draw() {
        System.out.println("drawing...");
    }
}

class Rectangle extends Shape {
    void draw() {
        System.out.println("drawing rectangle...");
    }
}

class Circle extends Shape {
    void draw() {
        System.out.println("drawing circle...");
    }
}

class Triangle extends Shape {
    void draw() {
        System.out.println("drawing triangle...");
    }
}

class TestPolyMorphism2 {
    public static void main(String[] args) {
        Shape s;
        s = new Rectangle();
        s.draw();
        s = new Circle();
        s.draw();
        s = new Triangle();
        s.draw();
    }
}
```

上面代码执行结果如下：

```
drawing rectangle...
drawing circle...
drawing triangle...
```

**Java运行时多态性示例4：Animal**

``` java
class Animal {
    void eat() {
        System.out.println("eating...");
    }
}

class Dog extends Animal {
    void eat() {
        System.out.println("eating bread...");
    }
}

class Cat extends Animal {
    void eat() {
        System.out.println("eating rat...");
    }
}

class Lion extends Animal {
    void eat() {
        System.out.println("eating meat...");
    }
}

class TestPolyMorphism3 {
    public static void main(String[] args) {
        Animal a;
        a = new Dog();
        a.eat();
        a = new Cat();
        a.eat();
        a = new Lion();
        a.eat();
    }
}
```

上面代码执行结果如下：

```
eating bread...
eating rat...
eating meat...
```

### Java运行时多态性与数据成员

上面示例中，都是有关方法被覆盖而不是数据成员，因此运行时多态性不能由数据成员实现。在下面给出的例子中，这两个类都有一个数据成员：`speedlimit`，通过引用子类对象的父类的引用变量来访问数据成员。由于我们访问的数据成员没有被重写，因此它将访问父类的数据成员。

> **规则：**运行时多态性不能由数据成员实现。

```java
class Bike {
    int speedlimit = 90;
}

class Honda3 extends Bike {
    int speedlimit = 150;
    
    public static void main(String[] args) {
        Bike obj = new Honda3();
        System.out.println(obj.speedlimit);
    }
}
```

上面代码执行结果如下：

```
90
```

### Java运行时多态性与多态继承

下面让我们来看看一个带有多级继承的运行时多态性的简单例子。

```java
class Animal {
    void eat() {
        System.out.println("eating");
    }
}

class Dog extends Animal {
    void eat() {
        System.out.println("eating fruits");
    }
}

class BabyDog extends Dog {
    void eat() {
        System.out.println("drinking milk");
    }
    
    public static void main(String[] args) {
        Animal a1,a2,a3;
        a1 = new Animal();
        a2 = new Dog();
        a3 = new BabyDog();
        a1.eat();
        a2.eat();
        a3.eat();
    }
}
```

上面代码执行结果如下：

```
eating
eating fruits
drinking milk
```

**尝试下面一段代码的输出：**

```java
class Animal {
    void eat() {
        System.out.println("animal is eating...");
    }
}

class Dog extends Animal {
    void eat() {
        System.out.println("dog is eating...");
    }
}

class BabyDog1 extends Dog {
    public static void main(String[] args) {
        Animal a = new BabyDog1();
        a.eat();
    }
}
```

执行上述代码，结果如下：

```
dog is eating
```

因为，`BabyDog`不会覆盖`eat()`方法，所以这里是`Dog`类的`eat()`方法被调用。

# Java基础知识：Java继承

Java中的继承是一种机制，表示为一个对象获取父对象的所有属性的行为。

在Java中继承是：可以创建基于现有类构建新的类。当您从现有类继承时，就可以重复使用父类的方法和字段，也可以在继承的新类中添加新的方法和字段。

继承表示IS-A关系，也称为父子关系。

## 为什么在java中使用继承？

对于方法覆盖（因此可以实现运行时的多态性），提高代码可重用性。在Java中，子类可继承父类中的方法，而不需要重新编写相同的方法。但有时子类并不想原封不动地继承父类的方法，而是想作一定的修改，这就需要采用方法的重写（覆盖）。

**Java继承的语法**

```java
class Subclass-name extends Superclass-name {
    
}
```

`extends`关键字表示正在从现有类派生创建的新类。“`extends`”的含义是增加功能。在Java的术语中，继承的类称为父类或超类，新类称为子或子类。

**Java继承示例**

![img](https://img-service.csdnimg.cn/img_convert/f7d4568ec75177bfc2ba0bdf22868a8d.png)

如上图所示，`Programmer`是子类，`Employee`是超类。两个类之间的关系时`Programmer IS-A Employee`。它表示`Programmer`是一种`Employee`的类型。

参考下面示例代码的实现：

```java
class Employee {
    float salary = 40000;
}

class Programmer extends Employee {
    int bonus = 10000;
    
    public static void main(String[] args) {
        Programmer p = new Programmer();
        System.out.println("Programmer salary is:" + p.salary);
        System.out.println("Bonus of Programmer is:" + p.bonus);
    }
}
```

执行上面代码得到以下结果：

```
Programmer salary is:40000.0
Bonus of programmer is:10000
```

在上面的例子中，`Programmer`对象可以访问自身类以及`Employee`类的字段，即提高了代码可重用性。

## java继承类型

在类的基础上，在java中可以有三种类型的继承：单一，多级和多层。在Java编程中，仅能通过接口支持多重和混合继承。稍后章节中我们将了解学习接口的应用。

![image-20200915234513157](https://img-service.csdnimg.cn/img_convert/7c78300802ea2c10c49795f28ddd5266.png)

> 注意：在java中的类不支持多继承。

当一个类扩展多个类，即被称为多重继承。例如：

![image-20200915234532142](https://img-service.csdnimg.cn/img_convert/bb5fa80e1975d73f0cbc220f88339fb7.png)

### (1).单一继承示例

文件`TestInheritance.java`中的代码如下：

```java
class Animal {
    void eat() {
        System.out.println("eating...");
    }
}

class Dog extends Animal {
    void bark() {
        System.out.println("barking...");
    }
}

class TestInheritance {
    public static void main(String[] args) {
        Dog d = new Dog();
        d.bark();
        d.eat();
    }
}
```

执行上面代码得到以下结果：

```
barking...
eating...
```

### (2).多级继承示例

文件`TestInheritance2.java`中的代码如下：

```java
class Animal {
    void eat() {
        System.out.println("eating...");
    }
}

class Dog extends Animal {
    void bark() {
        System.out.println("barking...");
    }
}

class BabyDog extends Dog {
    void weep() {
        System.out.println("weeping...");
    }
}

class TestInheritance2 {
    public static void main(String args[]) {
        BabyDog d = new BabyDog();
        d.weep();
        d.bark();
        d.eat();
    }
}
```

执行上面代码得到以下结果：

```
weeping...
barking...
eating...
```

### (3).多级继承示例

文件`TestInheritance3.java`中的代码如下：

```java
class Animal {
    void eat() {
        System.out.println("eating...");
    }
}

class Dog extends Animal {
    void bark() {
        System.out.println("barking...");
    }
}

class Cat extends Animal {
    void meow() {
        System.out.println("meowing...");
    }
}

class TestInheritance3 {
    public static void main(String[] args) {
        Cat c = new Cat();
        c.meow();
        c.eat();
        // c.bark();//C.T.Error
    }
}
```

执行上面代码得到以下结果：

```
meowing...
eating...
```

**问题：为什么在Java中不支持多重继承？**

为了降低复杂性并简化语言，Java中不支持多种继承。想象以下：A，B和C是三个类。C类继承A和B类。如果A和B类有相同的方法，并且从子类对象调用它，A或B类的调用方法会有歧义。

因为编译是错误比运行时错误好，如果继承2个类，java会在编译时报告错误。所以无论子类是否有相同的方法，都会有报告编译时错误。例如下面的代码时编译出错的。

```java
class A {
    void msg() {
        System.out.println("Hello");
    }
}

class B {
    void msg() {
        System.out.println("Welcome");
    }
}

class C extends A,B {
    public static void main(String[] args) {
        C obj = new C();
        obj.msg(); //Now which msg() method would be invoked?
    }
}
```

# Java基础知识：this关键字

在Java中，`this`关键字有很多种用法。在java中，这是一个引用当前对象的引用变量。

java `this`关键字的用法如下：

1. `this` 关键字可用来引用当前类的实例变量。
2. `this` 关键字可用于调用当前类方法（隐式）。
3. `this()` 可以用来调用当前类的构造函数。
4. `this` 关键字可作为调用方法中的参数传递。
5. `this` 关键字可作为参数在构造函数调用中传递。
6. `this` 关键字可用于从方法返回当前类的实例。

![img](https://img-service.csdnimg.cn/img_convert/4317f1e21e5f992fb3798c1a5184d08e.png)

## 1. this：引用当前类的实例变量

`this` 关键字可以用来引用当前类的实例变量。如果实例变量和参数直接存在歧义，则 `this` 关键字可用于明确地指定类变量以解决歧义问题。

**了解没有 this 关键字的问题**

下面先来理解一个不使用 `this` 关键字的示例：

```java
class Student {
    int rollno;
    String name;
    float fee;
    
    Student(int rollno, String name, float fee) {
        rollno = rollno;
        name = name;
        fee = fee;
    }
    
    void display() {
        System.out.println(rollno + " " + name + " " + fee);
    }
}

class TestThis1 {
    public static void main(String[] args) {
        Student s1 = new Student(111,"ankit",5000f);
        Student s2 = new Student(112,"sumit",6000f);
        s1.display();
        s2.display();
    }
}
```

执行上面代码输出结果如下：

```
0 null 0.0
0 null 0.0
```

在上面的例子中，参数（形式参数）和实例变量（`rollno` 和 `name`）是相同的。所以要使用 `this` 关键字来区分局部变量和实例变量。

**使用 this 关键字解决了上面的问题**

```java
class Student {
    int rollno;
    String name;
    float fee;
    
    Student(int rollno,String name,float fee) {
        this.rollno = rollno;
        this.name = name;
        this.fee = fee;
    }
    
    void display() {
        System.out.println(rollno + " " + name + " " + fee);
    }
}

class TestThis2 {
    public static void main(String[] args) {
        Student s1 = new Student(111,"ankit",5000f);
        Student s2 = new Student(112,"sumit",6000f);
        s1.display();
        s2.display();
    }
}
```

执行上面代码输出结果如下：

```
111 ankit 5000
112 sumit 6000
```

如果局部变量（形式参数）和实例变量不同，这不需要像下面的程序一样使用 `this` 关键字：

**不需要 this 关键字的程序示例**

```java
class Student {
    int rollno;
    String name;
    float fee;
    
    Student(int r,String n,float f) {
        rollno = r;
        name = n;
        fee = f;
    }
    
    void display() {
        System.out.println(rollno + " " + name + " " + fee);
    }
}

class TestThis2 {
    public static void main(String[] args) {
        Student s1 = new Student(111,"ankit",5000f);
        Student s2 = new Student(112,"sumit",6000f);
        s1.display();
        s2.display();
    }
}
```

执行上面代码输出结果如下：

```
111 ankit 5000
112 sumit 6000
```

> 对变量使用有意义的名称是一种好的编程习惯。所以使用相同名称的实例变量和参数，并且总是使用 `this` 关键字。

## 2. this：调用当前类方法

可以使用 `this` 关键字调用当前类的方法。如果不使用 `this` 关键字，编译器会在调用方法时自动添加此 `this` 关键字。

![img](https://img-service.csdnimg.cn/img_convert/532c4ed68cb0482fadd1f728079fe537.png)

## 3. this()：调用当前类的构造函数

`this()` 构造函数调用可以用来调用当前类的构造函数。它用于重用构造函数。换句话说，它用于构造函数链接。

**从参数化构造函数调用默认构造函数：**

```java
class A {
    A() {
        System.out.println("hello a");
    }
    
    A(int x) {
        this();
        System.out.println(x);
    }
}

class TestThis5 {
    public static void main(String[] args) {
        A a = new A(10);
    }
}
```

执行上面代码输出结果如下：

```
hello a
10
```

**从默认构造函数调用参数化构造函数：**

```java
class A {
    A() {
        this(5);
        System.out.println("hello a");
    }
    
    A(int x) {
        System.out.println(x);
    }
}

class TestThis6 {
    public static void main(String[] args) {
        A a = new A();
    }
}
```

执行上面代码输出结果如下：

```
5
hello a
```

### 使用构造函数调用

`this()` 构造函数调用用于从构造函数重用构造函数。它维护构造函数之间的链，即它用于构造函数链接。看看下面给出的示例，显示 `this` 关键字的实际使用。

```java
class Student {
    int rollno;
    String name, course;
    float fee;
    
    Student(int rollno,String name,String course) {
        this.rollno = rollno;
        this.name = name;
        this.course = course;
    }
    
    Student(int rollno,String name,String course,float fee) {
        this(rollno,name,course);
        this.fee = fee;
    }
    
    void display() {
        System.out.println(rollno + " " + name + " " + course + " " + fee);
    }
}

class TestThis7 {
    public static void main(String[] args) {
        Student s1 = new Student(111,"ankit","java");
        Student s2 = new Student(112,"sumit","java",6000f);
        s1.display();
        s2.display();
    }
}
```

执行上面代码输出结果如下：

```
111 ankit java null
112 sumit java 6000
```

>注意：调用 `this()` 必须是构造函数中的第一个语句。

下面示例为不把 `this()` 语句放在第一行，因此编译不通过。

```java
class Student {
    int rollno;
    String name, course;
    float fee;
    
    Student(int rollno,String name,String course) {
        this.rollno = rollno;
        this.name = name;
        this.course = course;
    }
    
    Student(int rollno,String name,String course,float fee) {
        this.fee = fee;
        this(rollno,name,course);//C.T.Error
    }
    
    void display() {
        System.out.println(rollno + " " + name + " " + course + " " + fee);
    }
}

class TestThis7 {
    public static void main(String[] args) {
        Student s1 = new Student(111,"ankit","java");
        Student s2 = new Student(112,"sumit","java",6000f);
        s1.display();
        s2.display();
    }
}
```

执行上面代码输出结果如下：

```
Compile Time Error: Call to this must be first statement in constructor
```

## 4. this：作为参数传递给方法

`this` 关键字也可以作为方法中的参数传递。它主要用于事件处理。看看下面的一个例子：

```java
class S2 {
    void m(S2 obj) {
        System.out.println("method is invoked");
    }
    
    void p() {
        m(this);
    }
    
    public static void main(String[] args) {
        S2 s1 = new S2();
        s1.p();
    }
}
```

执行上面代码输出结果如下：

```
method is invoked
```

**这个应用程序可以作为参数传递：**

在事件处理（或）的情况下，必须提供一个类的引用到另一个。它用于在多个方法中重用一个对象。

### this：在构造函数调用中作为参数传递

也可以在构造函数中传递 `this` 关键字。如果必须在多个类中使用一个对象，可以使用这种方式。看看下面的一个例子：

```java
class B {
 	A4 obj;
    
    B(A4 obj) {
        this.obj = obj;
    }
    
    void display() {
        System.out.println(obj.data);//using data member of A4 class
    }
}

class A4 {
    int data = 10;
    
    A4() {
        B b = new B(this);
        b.display();
    }
    
    public static void main(String[] args) {
        A4 a = new A4();
    }
}
```

执行上面代码输出结果如下：

```
10
```

## 6. this 关键字用来返回当前类的实例

可以从方法中 `this` 关键字作为语句返回。在这种情况下，方法的返回类型必须是类类型（非原始）。看看下面的一个例子：

**作为语句返回的语法**

```java
return_type method_name() {
    return this;
}
```

**从方法中返回为语句的 this 关键字的示例**

```java
class A {
    A getA() {
        return this;
    }
    
    void msg() {
        System.out.println("Hello java");
    }
}

class Test1 {
    public static void main(String[] args) {
        new A().getA().msg();
    }
}
```

执行上面代码输出结果如下：

```
Hello java
```

**验证 this 关键字**

现在来验证 `this` 关键字引用当前类的实例变量。在这个程序中将打印参考变量，这两个变量的输出是相同的。

```java
class A5 {
    void m() {
        System.out.println(this);//print the reference ID
    }
    
    public static void main(String[] args) {
        A5 obj = new A5();
        System.out.println(obj); //pirnt the reference ID
        obj.m();
    }
}
```

执行上面代码输出结果如下：

```
A5@22b3ea59
A5@22b3ea59
```

# Java基础知识：Java抽象

在Java中用 `abstract` 关键字声明的类称为抽象类。它可以有抽象和非抽象方法（带主体的方法）。

在学习java抽象类之前，先来了解java中的抽象。

## Java 中的抽象

抽象是隐藏实现细节并仅向用户显示功能的过程。

另一种方式，它只向用户显示重要的事情，并隐藏内部详细信息，例如：发送信息，只需要输入文本并发送消息。您也不需要知道有关邮件传递的内部处理过程。

抽象可以让你专注于对象做什么（实现的功能），而不是它如何做。

### 实现抽象的方法

在 java 中由两种实现抽象的方法，它们分别是：

1. 抽象类（部分）
2. 接口（完全）

## 1.Java 中的抽象类

使用 `abstract` 关键字声明的类被称为抽象类。需要扩展和实现它的方法。它不能被实例化。

**抽象类示例**

```java
abstract class A{}
```

**抽象方法**

一个被声明为 `abstract` 而没有实现的方法被称为抽象方法。

**抽象方法示例**

```java
abstract void printStatus();
```

**具有抽象方法的抽象类的示例**

在这个例子中，`Bike` 是一个抽象类，只包含一个抽象方法 `run()` 。它由 `Honda` 类提供实现。

``` java
abstract class Bike {
    abstract void run();
}

class Honda4 extends Bike {
    void run() {
        System.out.println("running safely..");
    }
    
    public static void main(String[] args) {
        Bike obj = new Honda4();
        obj.run();
    }
}
```

上面示例中的执行代码如下：

```
running safely..
```

### 理解抽象类的真实应用场景

在这个例子中，`Shape` 是一个抽象类，它的实现分别由 `Rectangle` 和 `Circle` 类提供。大多数情况下，我们不知道实现类（即对最终用户隐藏），实现类的对象由工厂方法提供。

**工厂方法**是用于返回类的实例的方法。稍后我们将在下一节中了解和学习**工厂方法**。

在这个例子中，创建 `Rectangle` 类的实例，`Rectangle` 类的 `draw()` 方法将被调用。创建一个类文件：`TestAbstraction1.java`，它的代码如下所示：

```java
abstract class Shape {
    abstract void draw();
}

class Rectangle extends Shape {
    void draw() {
        System.out.println("drawing rectangle");
    }
}

class Circle1 extends Shape {
    void draw() {
        System.out.println("drawing circle");
    }
}

class TestAbstraction1 {
    public static void main(String[] args) {
        Shape s = new Circle1();
        s.draw();
    }
}
```

上面代码执行结果如下：

```
drawing circle
```

**在java中抽象类的另一个例子**

创建一个Java文件：`TestBank.java`，代码如下所示：

```java
abstract class Bank {
    abstract int getRateOfInterest();
}

class SBI extends Bank {
    int getRateOfInterest() {
        return 7;
    }
}

class PNB extends Bank {
    int getRateOfInterest() {
        return 8;
    }
}

class TestBank {
    public static void main(String[] args) {
        Bank b;
        b = new SBI();
        System.out.println("Rate of Interest is: " + b.getRateOfInterest() + "%");
        b = new PNB();
        System.out.println("Rate of Interest is: " + b.getRateOfInterest() + "%");
    }
}
```

上面代码执行结果如下：

```
Rate of Interest is: 7%
Rate of Interest is: 8%
```

**具有构造函数，数据成员，方法等的抽象类**

抽象类可以有数据成员，抽象方法，方法体，构造函数甚至 `main()` 方法。创建一个Java文件：`TestAbstraction2.java`，代码如下所示：

```java
abstract class Bike {
    Bike() {
        System.out.println("bike is created");
    }
    
    abstract void run();
    
    void changeGear() {
        System.out.println("gear changed");
    }
}

class Honda extends Bike {
    void run() {
        System.out.println("running safely..");
    }
}

class TestAbstraction2 {
	public static void main(String[] args) {
        Bike obj = new Honda();
        obj.run();
        obj.changeGear();
    }
}
```

上面代码执行结果如下：

```
bike is created
running safely
gear changed
```

> 规则：如果在类中有任何抽象方法，那个类必须声明为抽象的。

```java
class Bike12 {
    abstract void run();
}
```

上面的 `Bike12` 是无法编译通过的。

> 规则：如果你扩展任何具有抽象方法的抽象类，必须提供方法的实现或使这个类抽象化。

### 抽象类的另一个真实场景

抽象类也可以用于提供接口的一些实现。在这种情况下，终端用户可能不会被强制覆盖接口的所有方法。

```java
interface A {
    void a();
    
    void b();
    
    void c();
    
    void d();
}

abstract class B implements A {
    public void c() {
        System.out.println("I am c");
    }
}

class M extends B {
    public void a() {
        System.out.println("I am a");
    }
    
    public void b() {
        System.out.println("I am b");
    }
    
    public void d() {
        System.out.println("I am d");
    }
}

class Test5 {
    public static void main(String[] args) {
        A a = new M();
        a.a();
        a.b();
        a.c();
        a.d();
    }
}
```

上面代码执行结果如下：

```
I am a
I am b
I am c
I am d
```

