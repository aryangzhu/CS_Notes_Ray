接口是一种技术，用来描述类应该做什么，但是不指定如何做。**一个类可以实现一个或者多个接口。**然后介绍lambda表达式，再讨论内部类(定义在其他类的内部，可以访问外部类的字段)，最后学习反射机制。

# 接口

## 接口的概念

接口不是类，而是希望实现它的所要完成的一组需求。

例如Comparable接口的代码

```java
public interface Comparable<T>{
    int comparableToS(T other);
}
```

**接口中的方法默认使用public**

在这里定义接口的时候使用了泛型，我们知道泛型即泛型数组列表，是让**编译器来帮我们检验我们数组列表中存放的类型是否一致**。

List<TreeNode> list=new LinnkedList<>();

菱形语法

Comparable<T>即是代表将来实现接口时，**开发者能够将原始参数类型转化为适当的类型**。

```java
class Employee implents Comparable<Employee>{
	//由此可以看出我们将泛型的T-原始参数类型转化为我们想要的类型
    public int comparableTo(Employee other){
        return Double.compareTo(salary,other.salary);
    }
}
```

实现接口时使用public，否则编译器会视为包权限。

注:正常情况下x.equals()y时x.compareTo(y)就应当为0，有一个类例外，BigDecimal,在比较1.0和1.00时comparable就会出现负值。

常用API

#### 常用API

java.lang.Comparable\<T>

​	int comparableTo(T other) 将**这个对象与other进行比较**

java.util.Arrays

​	static void sort(Object[] a) 对数组中**a中的元素进行排序**。要求**数组中的元素必须属于实现了Comparable接口的类**，元素之间也要能够进行比较。

java.lang.Integer

 	static int compare(int x ,int y) **如果x<y返回一个负整数;如果x和y相等，则返回0**;否则返回一个正整数。

java.lang.Double

​	static int compare(double x,double y) **如果x<y返回一个负整数**;如果x和y相等则返回0;否则返回一个正整数。

注:sgn(x.compareTo(y))=-sgn(y.compare(x))，如果前者有异常的话，那么后者也应该出现异常。这里的“sgn”可以理解为一种规则，即如果x>y的话，那么结果就是1。

​	如果Manager扩展了Employee,Employee实现的是Comparable\<Employee>,而在Manager中实现的是Comparble\<Manager>,Employee ep=new Employee(); Manager manager =new Manager();如果是ep.compareTo(manager)，那么没什么问题，因为经理也是员工。但是，如果manager.compareTo(ep),那么就会出现ClassCastException。

```java
class Manager extends Employtee{
	public int compareTo(Employee other){
		Manager m=(Manager) other; //很明显，这里是向下转型，会出问题
	}
}
```

## 接口的属性

接口不能被实例化

**可以使用instanceof来检验一个对象是否实现了某个接口**,如下

```java
if(anyObject instanceof Compable){...}            
```

和继承一样，接口也存在链,也就是说一个接口可以扩展另一个接口，使用extends关键字，被称为**接口链**。

接口中不允许有实例字段，但是可以定义常量。

```java
public interface Powered extends Mobeable{
	double SPEED_LIMIT=95;
}
```

接口中的字段总是public static final

## 接口与抽象类

在这里，我们提出了一个问题，既然抽行类也有定义需求的功能，为什么还要这么麻烦地引入接口呢？

这是因为类在设计时就只允许只能一个一个类，而一个类却可以实现多个接口。

注：C++设计者允许一个类有多个超类，但是Java不支持多重继承。

## 静态和私有方法

java8中允许在接口中增加静态方法，只是一般将静态方法放在伴随类中，所以我们**经常会在标准库中见到成对出现的接口和实用工具类**，

Java9中
