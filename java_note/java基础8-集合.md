- [Java集合框架](#java集合框架)
  - [集合接口与实现分离](#集合接口与实现分离)
  - [Collection接口](#collection接口)
  - [泛型实用方法](#泛型实用方法)
    - [常用API](#常用api)
      - [java.util.Collection\<E>](#javautilcollectione)
      - [java.util.Iterator\<E>](#javautiliteratore)
- [集合框架中的接口](#集合框架中的接口)
- [具体集合](#具体集合)
  - [链表](#链表)
  - [数组列表](#数组列表)
  - [散列集](#散列集)
  - [树集](#树集)
  - [队列与双端队列](#队列与双端队列)
  - [优先队列](#优先队列)
- [映射](#映射)
  - [基本映射操作](#基本映射操作)
  - [更新映射条目](#更新映射条目)
  - [映射视图](#映射视图)
  - [弱散列映射](#弱散列映射)
  - [枚举集与映射](#枚举集与映射)
- [视图与包装器](#视图与包装器)
  - [小集合](#小集合)
  - [子范围](#子范围)
  - [不可修改的视图](#不可修改的视图)
  - [同步视图](#同步视图)
  - [检查型视图](#检查型视图)
  - [关于可选操作的说明](#关于可选操作的说明)
- [算法](#算法)
  - [为什么使用泛型算法](#为什么使用泛型算法)
  - [排序与混排](#排序与混排)
  - [二分查找](#二分查找)
  - [简单算法](#简单算法)
  - [批操作](#批操作)
  - [集合与数组的转换](#集合与数组的转换)
  - [编写自己的算法](#编写自己的算法)
- [遗留的集合](#遗留的集合)
  - [Hashtable类](#hashtable类)
  - [枚举](#枚举)
  - [属性映射](#属性映射)
  - [栈](#栈)
  - [位集](#位集)
# Java集合框架
写这一部分是为了与Guide哥版本的笔记相互辅助参考。
## 集合接口与实现分离

Java集合类库将接口与实现进行分离。例如,队列数据结构要求可以在队尾进行插入队头删除元素(FIFO),所以接口应该形如下面:

```java
public interface Queue<E>{ 
    void add(E element);
    E remove();
    E getElement();
    int size;
}
```

但是,底层实现的时候摆在我们面前的有两个选择:循环数组实现

```java
public class CircularArrayQueue<E> implements Queue<E>{
    private int first;
    private int last;
    
    public CircularArrayQueue(int capacity){...}
    public void add(E element){...}
    public E remove(){...}
    public int size(){...}
    public E[] elements;
}
```

链表实现

```java
public class LinkedListQueue<E> implements Queue<E>{
    private Link head;
    private Link tail;
    
   	LinkListQueue(){...}
    public void add(E element){...}
    public E remove(){...}
    public int size(){...}
}
```

我们在程序中构造时需要考虑哪种具体的类更加符合当下的场景:

```java
Queue<Customer> queue=new CircularArrayQueue<>();
expressLane.add(new Customer("Harry"));
```

API文档中有许多的Abstract开头的类,这是专门为类库实现者而设计的,例如想要实现自己的队列会发现实现AbstractQueue比实现Queue接口轻松得多。

## Collection接口

这里直接阅读Guide版本。

##　迭代器

```java
public interface Iterator<E>{
	E next();
    boolean hasNext();
    void remove();
    default void forEachRemaing(Consumer<? super E> action);
}
```

hasNext方法和next一起使用,Collection扩展了Iterable接口,所以Collection体系下的集合都可以获取一个迭代器。

```java
Colleciton<String> =...; 
Iterator<String> iter=c.iterator();//通过iterator方法来获得迭代器
while(iter.hasNext()){
    String next=iter.next();
}
```

**for each(任意实现了Iterable接口的对象)**其实就是迭代器的简单用法(编译器将foreach转换为带迭代器的循环)。

```java
for(E e: elements){
    ...
}
```

Iterable接口

```java
public interface Iterable<E>{
    Iterator<E> iterator();
}
```

也可以不写循环而是调用forEachRemaing方法并提供一个lambda表达式(作用就是说明如何操作元素)。

Java中查找集合元素就只有next方法一种,next方法,**迭代器会越过下一个元素,并返回刚刚越过的元素的引用(可以理解为迭代器在两个元素之间)**。

注意:next方法和remove方法强依赖性

```
iter.remove();
iter.remvoe();//ERROR

//必须先调用next方法越过元素,才能remove掉刚刚越过的元素
iter.remove();
iter.next();
iter.remove();
```

## 泛型实用方法

### 常用API

#### java.util.Collection\<E>

#### java.util.Iterator\<E>



# 集合框架中的接口

# 具体集合

## 链表

ListIterator实现了Iterator接口,为的是**想在链表表头添加一个元素**。

书上的LinkedList添加元素的例子很形象:

```java
List<Employee> staff=new LinkedList<>();
list.add(e1);
list.add(e2);
ListIterator iter=list.listIterator();
iter.next();
iter.add(e3);
```

这时迭代器会越过第一个元素e1,将e3添加至表头(也就是说e3在e1后面)。

## 数组列表

ArrayList

## 散列集

HashSet

## 树集

TreeSet

## 队列与双端队列

Queue&Deque

## 优先队列

PriorityQueue

# 映射

## 基本映射操作

Java类库提供了两个通用的实现:HashMap和TreeMap。这两个类都实现了Map接口。

map.put();

map.get(key);

## 更新映射条目

map.put();

## 映射视图

有3中视图:键集、值集合和键/值对集。

## 弱散列映射



## 枚举集与映射

EnumSet是一个枚举类型元素集和高效实现。由于枚举类型只有有限个实例,所以EnumSet内部用位序列实现。如果对应的值在集中,则相应的位被置为1.





# 视图与包装器

## 小集合



## 子范围



## 不可修改的视图



## 同步视图



## 检查型视图



## 关于可选操作的说明



# 算法

## 为什么使用泛型算法



## 排序与混排

书上与Guide的主要区别就是使用了Lambda表达式。

## 二分查找



## 简单算法



## 批操作



## 集合与数组的转换

集合转数组list.toArray();

数组转集合staff.asList();

## 编写自己的算法



# 遗留的集合

## Hashtable类



## 枚举



## 属性映射



## 栈



## 位集

