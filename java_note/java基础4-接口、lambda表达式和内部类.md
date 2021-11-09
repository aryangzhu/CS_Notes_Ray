接口是一种技术，用来描述类应该做什么，但是不指定如何做。**一个类可以实现一个或者多个接口。**然后介绍lambda表达式，再讨论内部类，最后学习反射机制。

### 1.接口

接口不是类，而是希望符合某些需求。

例如Comparable接口的代码

```java
public int而face ComparableS{
    int comparableToS(Object other);
}
```

泛型