- [集合概述](#集合概述)
  - [Java集合概述](#java集合概述)
  - [说说List、Set、Queue和Map四者的区别](#说说listsetqueue和map四者的区别)
  - [集合框架底层数据结构总结](#集合框架底层数据结构总结)
    - [Collection接口下面的集合](#collection接口下面的集合)
      - [List](#list)
        - [1.ArrayList: Object[]数组](#1arraylist-object数组)
        - [2.Vector:Object[] 数组](#2vectorobject-数组)
        - [3.LinkedList:双向链表(jdk1.7之后取消了循环链表)](#3linkedlist双向链表jdk17之后取消了循环链表)
      - [Set](#set)
        - [1.HashSet(无序,唯一):基于HashMap实现的,底层采用HashMap来保存元素](#1hashset无序唯一基于hashmap实现的底层采用hashmap来保存元素)
        - [2.LinkedHashSet:**LinkedHashSet是HashSet的子类**,并且内部是通过LinkedHashMap来实现的,而LinkedHashMap其内部是基于HashMap实现的一样。](#2linkedhashsetlinkedhashset是hashset的子类并且内部是通过linkedhashmap来实现的而linkedhashmap其内部是基于hashmap实现的一样)
        - [3.TreeSet(有序,唯一):红黑树(自平衡的二叉排序树)。](#3treeset有序唯一红黑树自平衡的二叉排序树)
      - [Queue](#queue)
        - [1.PriorityQueue:Obejct[] 数组来实现二叉堆](#1priorityqueueobejct-数组来实现二叉堆)
        - [2.ArrayQueue:Object[] 数组+双指针](#2arrayqueueobject-数组双指针)
  - [Map接口下的集合](#map接口下的集合)
    - [HashMap](#hashmap)
    - [LinkedHashMap](#linkedhashmap)
  - [如何选用集合](#如何选用集合)
  - [为什么使用集合](#为什么使用集合)
- [Collection子接口之List](#collection子接口之list)
  - [ArrayList与Vector的区别？](#arraylist与vector的区别)
  - [ArrayList与LinkedList区别？](#arraylist与linkedlist区别)
- [Collection的子接口之Set](#collection的子接口之set)
  - [Comparable和Comparator的区别](#comparable和comparator的区别)
  - [无序性和不可重复性的含义是什么](#无序性和不可重复性的含义是什么)
  - [比较HashSet、LinkedHashSet和TreeSet三者的异同](#比较hashsetlinkedhashset和treeset三者的异同)
- [Collection子接口之Queue](#collection子接口之queue)
  - [Queue与Dequeue的区别](#queue与dequeue的区别)
  - [ArrayDeque与LinkedList的区别](#arraydeque与linkedlist的区别)
  - [PriorityQueue](#priorityqueue)
- [Map接口](#map接口)
  - [HashMap和HashTable的区别](#hashmap和hashtable的区别)
    - [1.线程是否安全](#1线程是否安全)
    - [2.效率](#2效率)
    - [3.对NULL key和NULL Value的支持](#3对null-key和null-value的支持)
    - [4.初始容量大小和每次扩容量大小的不同](#4初始容量大小和每次扩容量大小的不同)
    - [5.底层数据结构](#5底层数据结构)
  - [HashMap和HashSet](#hashmap和hashset)
  - [HashMap和TreeMap的区别](#hashmap和treemap的区别)
  - [HashSet如何检查重复](#hashset如何检查重复)
    - [hashCode()与equals()](#hashcode与equals)
    - [==与equals的区别](#与equals的区别)
  - [HashMap的底层实现](#hashmap的底层实现)
  - [HashMap的长度为什么是2的幂次方](#hashmap的长度为什么是2的幂次方)
  - [HashMap多线程导致死循环问题](#hashmap多线程导致死循环问题)
  - [HashMap常见的遍历方式](#hashmap常见的遍历方式)
  - [ConcurrentHashMap线程安全的具体实现方式/底层具体实现](#concurrenthashmap线程安全的具体实现方式底层具体实现)
    - [1.底层数据结构](#1底层数据结构)
    - [2.实现线程安全的主要方式(重要)](#2实现线程安全的主要方式重要)
# 集合概述

## Java集合概述

集合也被称作容器,主要由两大接口派生而来,一个是Collection接口,另一个是Map接口,主要用于存放键值对。

![](https://gitee.com/aryangzhu/picture/raw/master/java/Java%E9%9B%86%E5%90%88%E4%BD%93%E7%B3%BB.png)

## 说说List、Set、Queue和Map四者的区别

List:存储的元素是**有序的、可重复的**。

Set:存储的元素是无序的、不可重复的。

Queue:按特定的排队规则来确定先后顺序,存储的元素是有序的、可重复的。

Map:使用键值对(key-value)存储,类似于数学上的函数y=f(x)

## 集合框架底层数据结构总结

### Collection接口下面的集合

#### List

##### 1.ArrayList: Object[]数组

##### 2.Vector:Object[] 数组

##### 3.LinkedList:双向链表(jdk1.7之后取消了循环链表)

#### Set

##### 1.HashSet(无序,唯一):基于HashMap实现的,底层采用HashMap来保存元素

##### 2.LinkedHashSet:**LinkedHashSet是HashSet的子类**,并且内部是通过LinkedHashMap来实现的,而LinkedHashMap其内部是基于HashMap实现的一样。

##### 3.TreeSet(有序,唯一):红黑树(自平衡的二叉排序树)。

#### Queue

##### 1.PriorityQueue:Obejct[] 数组来实现二叉堆
##### 2.ArrayQueue:Object[] 数组+双指针

## Map接口下的集合
### HashMap
jdk1.8之前HashMap由数组+ 链表组成的。数组是HashMap的主体,链表主要是为了解决哈希冲突而存在的("拉链法"解决冲突)。**JDK1.8之后在解决哈希冲突时有了较大的变化,当链表长度大于阈值(默认为8)(将链表转换成红黑树前会判断,如果当前数组的长度小于64,那么先会对数组进行扩容而不是转换成红黑树)时,将链表转换成红黑树,以减少搜索时间**。

### LinkedHashMap
LinkedHashMap继承自HashMap,所以在**它的底层仍然是基于拉链式散列结构即由数组和链表或红黑树组成**。另外,LinkedHashMap在上面结构的基础上,增加了一条由**双向链表**,使得上面的结构可以保持键值对的插入顺序。同时通过对链表进行相应的操作,实现了访问顺序的相关逻辑。

## 如何选用集合
根据**集合特点**来选用,比如需要根据键值获取到元素时就选用Map接口下的集合,需要排序时选择TreeMap,不需要排序时就选择HashMap,需要**保证线程安全就选用ConcurrentHashMap**。

当我们只需要存放元素值时,就选择实现Collection接口的集合,需要保证元素唯一时选择实现Set接口的集合比如TreeSet或HashSet,不需要就选择实现List接口的比如ArrayList或LinkedList,然后再根据这些接口的集合的特点来选用。

## 为什么使用集合

1.为了应对数组长度不可变的场景;

2.同时,声明数组时的数据类型也决定了该数组存储的数据的类型;

3.数组存储的数据是有序的、可重复的,特点单一。但是集合提高了数据存储的灵活性,Java集合不仅可以用来存储不同类型不同数量的对象,还可以保存具有映射关系的数据。

# Collection子接口之List

## ArrayList与Vector的区别？

ArrayList是List的主要实现类,底层使用Object[]存储,适用于频繁的查找工作,线程不安全;

Vector是List的古老实现类,底层使用Object[]存储,线程安全的。

## ArrayList与LinkedList区别？

1.是否保证**线程安全**:ArrayList和LinkedList都是不同步的,也就是不保证线程安全;

2.**底层数据结构**:ArrayList底层使用的是**Object数组**;LinkedList底层使用的是**双向链表**数据结构。

3.**插入和删除是否受元素位置**的影响:

a.ArrayList采用数组存储,所以插入和删除元素的时间复杂度受元素位置的影响。很容易理解,插入第i个位置时,时间复杂度就是O(n-i),因为在进行上述操作的时候集合中第i和第i个元素之后的(n-i)个元素都要执行向后位/向前位移一位的操作。

b.LinkedList采用的是链表存储,所以,如果是在头尾插入或者删除不受元素位置的影响(add(E e)、addFirst(E e)、addLast(E e)、removeFirst()、removeLast())近似O(1),如果是要在指定位置i插入和删除元素的话时间复杂度近似为O(n),因为需要先移动到指定位置再插入。

4.**是否支持快速随机访问**:LinkedList不支持高效的随机元素访问,而ArrayList支持。快速随机访问就是通过元素的序号快速获取元素对象(对应于get(int index)方法)。

5.**内存占用**:ArrayList的空间浪费主要体现在list列表的结尾会预留一定的容量空间,而LinkedList的空间花费则体现在它的每一个元素都需要消耗比ArrayList更多的空间(因为要存放直接后继和直接前驱以及数据)。

JavaGuide还提到了**RandomAccess**接口,在之前的IO中同样有RandomAccessFile接口。

ArrayList实现了RandomAccess接口而LinkedList没有实现。因为ArrayList底层是数组,而LinkedList底层是链表。数组天然支持随机访问,而链表不支持。

# Collection的子接口之Set

## Comparable和Comparator的区别

Comparable接口实际上出自java.lang包,它有一个compareTo(Object obj)方法用来排序。

Comparator接口实际上是出自java.util包它有一个compare(Object obj1,Object obj2)方法用来排序。

Guide哥的解释是自定义排序时就需要重写compareTo()方法或compare()方法,当我们需要对某一个集合实现两种排序方式,比如一个song对象中的歌名和歌手名分别采用一种排序方法的话,就可以重写compareTo()方法和使用自制的Comparator方法或者两个Comparator来实现歌名排序和歌手名排序。

下面的代码是定制的Comparator(比较器,作为参数传入)

```java
Collections.sort(arrayList,new Comparator<Integer>(){
    
    public compare(Integer o1,Integer o2){
        return o2.compareTo(o1);
    }
    
});
```

重写compareTo方法实现年龄排序(实现方法,并且还是一个函数式接口)

```java
public class Person implements Comparable<Person>{
    @override
    public int compareTo(Person o){
        if(this.age>o.getAge()){
            return 1;
        }
        if(this.age<o.getAge()){
            return -1;
        }
        return 0;
    }
}
```

## 无序性和不可重复性的含义是什么

1.什么是无序性?**无序性不等于随机性,无序性是指存储的数据在底层数组中并非按照数组索引的顺序添加,而是根据数据的哈希值决定的**。

2.什么是不可重复性？不可重复性是指添加的元素按照equals()判断时,返回false,需要同时重写equals()方法和hashCode()方法。

## 比较HashSet、LinkedHashSet和TreeSet三者的异同

1.HashSet、LinkedLHashSet和TreeSeet都是Set接口的实现类,都能保证元素唯一,并且都不是线程安全的。

2.HashSet、LinkedHashSet和TreeSet的主要区别是在于底层数据结构不同。**HashSet**的底层数据结构是**哈希表(基于HashMap实现)**。**LinkedHashSet**的底层数据结构是**链表和哈希表**,元素的**插入和取出顺序满足FIFO**。TreeSet的底层数据结构是**红黑树**,元素是有序的,排序的方式有**自然排序和定制排序**。

3.底层数据结构不同导致这三者的应用场景不同。HashSet用于不需要保证元素插入和取出顺序的场景,LinkedHashSet用于保证元素的插入和取出顺序满足FIFO的场景,TreeSet用于支持元素自定义排序规则的场景。

# Collection子接口之Queue

## Queue与Dequeue的区别

queue是单端队列,只能从一端插入元素,另一端删除元素,实现上一般遵循**先进先出**(FIFO)规则。

Queue扩展了Collection接口,根据**因为容量问题导致操作失败后处理方式的不同**可以分为两类方法:一种在操作失败后会抛出异常,另一种则会返回特殊值。

| `Queue` 接口 | 抛出异常  | 返回特殊值 |
| ------------ | --------- | ---------- |
| 插入队尾     | add(E e)  | offer(E e) |
| 删除队首     | remove()  | poll()     |
| 查询队首元素 | element() | peek()     |

Deque是双端队列,在队列的**两端均可插入或删除元素**。

Dequeue扩展了Queue的接口,**增加了在队首和队尾进行插入和删除的方法**,同样根据失败后处理方式的不同分为两类:

| `Deque` 接口 | 抛出异常      | 返回特殊值      |
| ------------ | ------------- | --------------- |
| 插入队首     | addFirst(E e) | offerFirst(E e) |
| 插入队尾     | addLast(E e)  | offerLast(E e)  |
| 删除队首     | removeFirst() | pollFirst()     |
| 删除队尾     | removeLast()  | pollLast()      |
| 查询队首元素 | getFirst()    | peekFirst()     |
| 查询队尾元素 | getLast()     | peekLast()      |

Deque还提供有push()和pop()等其他方法,可用于模拟栈。

## ArrayDeque与LinkedList的区别

ArrayDeque和LinkedList都实现了**Deque接口**,两者都具有队列的功能,两者有什么区别呢?

ArrayDeque是基于**可变长的数组和双指针**来实现,而LinkedList则通过**链表**来实现。

ArrayDeque**不支持存储NULL数据**,但LinkedList支持。
ArrayDeque是在JDK1.6才被引入的,而LinkedList早在JDK1.2时就已经存在。

ArrayDeque插入时可能存在扩容过程,不过均摊后的插入操作依然为O(1)。LinkedList每次插入数据都需要申请新的空间,均摊性能相比更慢。

## PriorityQueue

在JDk1.5中被引入,与Queue的区别在于元素出队顺序是与优先级相关的,即总是优先级最高的元素出队。

1.PriorityQueue利用了**二叉堆的数据结构来实现的,底层使用可变长的数组来存储数据**。

2.PriorityQueue通过堆元素的上浮和下沉,**实现了在O(logn)的时间复杂度插入元素和删除堆顶元素**。

3.PriorityQueue默认是小顶堆,但**可以接受一个Comparator作为构造参数,从而来自定义元素优先级的先后**。

# Map接口

## HashMap和HashTable的区别

### 1.线程是否安全

HashMap是非线程安全的,HashTable是线程安全的,因为HashTable内部的方法基本都经过synchronized修饰。

### 2.效率

HashMap更加高效一点(因为不用考虑线程安全),而且HashTable基本被淘汰。

### 3.对NULL key和NULL Value的支持

HashMap可以存储null的key和value,但null作为键只能有一个,null作为值可以有多个;HashTable不允许有null键和null值,否则会抛出NullPointerException。

### 4.初始容量大小和每次扩容量大小的不同

1.创建时如果不指定容量初始值,HashTable默认的**初始值为11,之后每次扩容,容量变为原来的2n+1**。HashMap默认的**初始化大小为16**,之后每次扩容,容量变为原来的2倍。

2.创建时如果给定了容量初始值,HashTable会直接使用你给定的大小,而HashMap会将其扩充为2的幂次方大小(HashMap中的tableSizeFor()方法保证),也就是**说HashMap总是使用2的幂作为哈希表的大小**。

### 5.底层数据结构

JDK1.8以后的HashMap在解决哈希冲突时有了较大的变化,当链表长度大于阈值(**默认为8**)(**将链表转换为红黑树前会判断,如果当数组的长度小于64,那么会选择先进性数组扩容,而不是转换为红黑树**)时,将链表转换为红黑树,以减少搜索时间。HashTable没有这样的机制。

HashMap中带有初始容量的构造函数:

```java
public HashMap(int initialCapacity, float loadFactor) {
        if (initialCapacity < 0)
            throw new IllegalArgumentException("Illegal initial capacity: " +
                                               initialCapacity);
        if (initialCapacity > MAXIMUM_CAPACITY)
            initialCapacity = MAXIMUM_CAPACITY;
        if (loadFactor <= 0 || Float.isNaN(loadFactor))
            throw new IllegalArgumentException("Illegal load factor: " +
                                               loadFactor);
        this.loadFactor = loadFactor;
        this.threshold = tableSizeFor(initialCapacity);
    }
     public HashMap(int initialCapacity) {
        this(initialCapacity, DEFAULT_LOAD_FACTOR);
    }
```

下面这个方法保证了HashMap总是使用2的幂作为哈希表的大小

```java
    static final int tableSizeFor(int cap) {
        int n = cap - 1;
        n |= n >>> 1;
        n |= n >>> 2;
        n |= n >>> 4;
        n |= n >>> 8;
        n |= n >>> 16;
        return (n < 0) ? 1 : (n >= MAXIMUM_CAPACITY) ? MAXIMUM_CAPACITY : n + 1;
    }
```

## HashMap和HashSet

前面学习Set的子接口的时候,我们已经知道HashSet的底层就是基于HashMap实现的。HashSet的源码非常非常少,因为除了**clone()、writeObject()、readObject()**是HashSet自己需要实现的以外,其他的方法都是直接调用HashMap中的方法。

|               `HashMap`                |                          `HashSet`                           |
| :------------------------------------: | :----------------------------------------------------------: |
|           实现了 `Map` 接口            |                       实现 `Set` 接口                        |
|               存储键值对               |                          仅存储对象                          |
|     调用 `put()`向 map 中添加元素      |             调用 `add()`方法向 `Set` 中添加元素              |
| `HashMap` 使用键（Key）计算 `hashcode` | `HashSet` 使用成员对象来计算 `hashcode` 值，对于两个对象来说 `hashcode` 可能相同，所以`equals()`方法用来判断对象的相等性 |

## HashMap和TreeMap的区别

TreeMap和HashMap都继承自**AbstractMap**,但是需要注意的是TreeMap它还实现了Navigable接口和Sorted接口。

实现Navigable接口让TreeMap有了对集合内元素的搜索的能力。

实现SortedMap接口让TreeMap有了对集合中的元素根据键排序的能力。默认是按key的升序排序,不过也可以指定排序的比较器。

```java
public static void main(String[] args){
    TreeMap<Person,String> treeMap=new TreeMap<>(new Comparator<Person>(){
        @override
        public int compare(Person person1,Person person2){
            int result=person1.getAge()-person2.getAge();
            return Integer.compare(num,0);
        }
    });
}
```

总结:相比HashMap来说TreeMap主要多了对集合中的元素**根据键值排序的能力以及对集合内元素的搜索能力**。

## HashSet如何检查重复

当你把对象加入`HashSet`时，`HashSet` 会先计算对象的`hashcode`值来判断对象加入的位置，同时也会与其他加入的对象的 `hashcode` 值作比较，如果没有相符的 `hashcode`，`HashSet` 会假设对象没有重复出现。但是如果发现有相同 `hashcode` 值的对象，这时会调用`equals()`方法来检查 `hashcode` 相等的对象是否真的相同。如果两者相同，`HashSet` 就不会让加入操作成功。

在openJDK8中,实际上无论HashSet中是否已经存在了某元素,HashSet都会直接插入,只是会在add()方法的返回值处告诉我们插入前是否存在相同元素。

### hashCode()与equals()

1.如果两个对象相等,hashcode一定相等。

2.两个对象相等,equals方法返回true

3.如果两个对象hashcode相等,他们也不一定相等

4.equals方法被覆盖过,则hashcode方法也必须被覆盖

5.hashcode()在堆上的对象产生独特值。**如果没有重写hashCode(),则该class的两个对象无论如何也不会相等**。

### ==与equals的区别

对于==来说,比较的是值是否相等

对于引用类型来说,==比较的是两个引用是否指向同一个对象地址(两者在内存中存放的地址(堆内地址)是否指向同一个地方)。

对于引用类型(包括包装类型)来说,equals如果没有被重写,对比的就是地址;而如果被重写那么对比的就是内容。

## HashMap的底层实现

jdk1.8之前是数组+链表,即**链表散列**。HashMap通过key的hashCode经过扰动函数处理得到hash值,**然后通过(n-1)&hash判断当前元素存放的位置(这里的n指的是数组的长度)**,如果当前位置存在元素的话,就判断该元素与要存入的元素的hash值以及key是否相同,如果相同的话，直接覆盖,不相同就通过拉链法解决冲突。

扰动函数就是HashMap的hash方法。使用hash方法也就是扰动函数是为了防止一些实现比较差的hashCode()方法,也就是为了减少碰撞。

JDk1.8中的源码

```java
static final int hash(Object key){
    int h;
    
    return (key==null)?0:(h=key.hashCode())^(h>>>16);
}
```

JDK1.7中源码

```java
 h ^= (h >>> 20) ^ (h >>> 12);
 return h ^ (h >>> 7) ^ (h >>> 4);
```

拉链法很容易理解:数据结构中也学过,这里不做过多阐述。

## HashMap的长度为什么是2的幂次方

为了让HashMap存取高效,需要尽可能地减少碰撞。Hash值的范围为-2147483648到2147483637,前后加起来大概40亿的映射空间,只要哈希函数映射得比较均匀松散,一般很难出现碰撞。但是40亿个地址计算机内存肯定放不下,所以使用模数取余的方法来对应数组存放位置下标(数组+链表),这个数组下标的计算方式是

```java
(n-1)&hash
```

也解释了HashMap的长度为什么是2的幂次方。

**取余(%)如果除数是2的幂次方则等价于与其除数减一的与(&)操作**,上面的代码等价于

```java
hash%length
```

前提是length是2的次方,并且&要%性能更好。

## HashMap多线程导致死循环问题

推荐使用ConcurrentHashMap。

## HashMap常见的遍历方式



## ConcurrentHashMap线程安全的具体实现方式/底层具体实现

主要体现在线程安全的方式上不同。

### 1.底层数据结构

JDK1.7的ConcurrentHashMap底层采用的是**分段的数组+链表**实现,JDK1.8采用的数据结构跟HashMap1.8的结构一样,数组+链表/红黑二叉树。HashTable和JDK1.8之前的HashMap的底层数据结构类似都是采用数组+链表形式,数组是HashMap的主体,链表则是为了解决哈希冲突而存在的。

### 2.实现线程安全的主要方式(重要)

1.在JDK1.7的时候,ConcurrentHashMap(分段锁)对整个桶数组进行了**分割分段**(segment),每一把锁只锁容器中一部分数据,多线程访问容器里不同数据端的数据,就不会存在锁竞争,提高了并发访问率。到了JDK1.8之后已经摒弃了Segment,转而采用的是数组+链表+红黑树的数据结构来实现,并发控制使用synchronized和CAS来操作。

JDK1.6之后对synchronized做了许多优化,所以ConcurrentHashMap就是线程安全的HashMap。

但是,JDK1.8之后的版本也有Segment的数据结构，为的是兼容以前的版本。

HashTable(同一把锁?):使用synchronized来保证线程安全,效率分厂低下。当一个线程访问同步方法时,其他线程也访问同步方法,可能会进入阻塞或者轮训状态,例如使用put方法,当一个线程持有锁时,另一个线程就不能put元素,竞争会越来越激烈。

