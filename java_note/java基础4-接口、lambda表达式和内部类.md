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

List\<TreeNode> list=new LinnkedList<>();

菱形语法

Comparable\<T>即是代表将来实现接口时，**开发者能够将原始参数类型转化为适当的类型**。

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

​	static int compare(int x ,int y) **如果x<y返回一个负整数;如果x和y相等，则返回0**;否则返回一个正整数。

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

java8中允许在接口中增加静态方法，只是一般将静态方法放在伴随类中，所以我们**经常会在标准库中见到成对出现的接口和实用工具类**。

Java9中接口中方法可以是private，**private可以是静态或者实例方法，但是只能在接口本身中使用，用法有限，一般作其他方法的辅助方法**。

## 默认方法

可以为接口中的方法提供一个默认实现，使用default修饰。例如

```java
public interface Comparable<E>{
	default int compareTo(T other){
		return 0; //默认情况
	}
}
```

大多数情况下没有用处，因为Compable的具体实现会覆盖掉这个方法

迭代器

```java
public interface Iterator<E>{
	boolean hasNext();
	E next();
	default void remove(){
		throw new UnsupportededOperationException("remove");
	}
}

```

如果实现一个迭代器，那么必须提供hasNext()和next()方法。**这些方法没有默认实现-取决于你的数据结构**

默认方法的一个重要作用就是“接口演化”，很久之前定义了一个类，

public class Bag implements Collection

后来接口中增加了一个stream的方法，**由于Bag类没有实现stream方法，那么Bag类将无法编译(无法保证“源代码兼容”)**。

假设不编译这个类，而是**只使用原来的一个包含这个类的jar文件,这个类可以正常加载**，尽管没有新方法。程序仍然可以正常构造Bag实例，但是如果调用实例方法sream()，那么就会出现AbstactMethodError。

如果来使用了default的话，那么Bag类就能通过编译，另外，**如果重新编译而直接加载这个类的话并且在一个实力上使用sream方法，实际上调用的的是Collection.stream()**。

## 默认方法解决冲突

如果两个接口定义了形同的方法，或者一个超类和一个接口定义了相同的方法。

1.超类优先。

2.如果两个接口相互冲突，那么就要覆盖这个方法来解决冲突(二者择其一)。

```java
interface Person{
	default String getName(){
		return " ";
	};
}

interface Named{
    default Strting getName(){
        	return getClass()+"_"+hashCode();
    }
}
```

在子类中进行覆盖

```java
class Student implements Person,Named{
	public String getName(){
		return Person.super.getName();
	}
}
```

## 接口与回调

**回调(callback)是一种常见的程序设计模式**。在这种模式中，你可以指定某个特定事件发生后执行的操作。例如，点击菜单选项之后完成的某个特定动作。

在java.swing包中有一个Timer类，非常有用。

构造定时器时，需要设置时间间隔，并指定需要干什么。

在Java中，我们可以向定时器中传入一个对象，因为对象携带的信息要比单单一个参数更多，从代码中可以看出传递的是一个监听器对象，这个监听器对象干了什么事儿呢，它打印了语句，同时执行了beep()，这也是面向对象的优势。

我们要知道**定时器要调用哪个方法，并要求传递的对象所属的类实现了java.awt.event包下的ActionListener接口**。

```java
public interface ActionListener{
	void acitonPerformed(Action Event);
}
```

当到达指定的时间间隔时,定时器就调用actionPerformed方法。

```java
class TimePrinter implements AcitonListerner{
	public void acitionPerformed(ActionEvent event){
		System.out.println("At the tone,the time is..."+Instance.ofEpochMilli(event.getWhen()));
		Toolkit.getDefaultToolkit().beep();
	}
}
 
```

timer构造器传入的参数第一个是时间间隔，第二个是监听器对象

```java
Timeprinter listener=new Timeprinter();
Timer timer = new Timer(1000 ,listener);
timer.start();
```

   通过start()来启动定时器

## Comparator接口

​	之前我们已经了解了如何对一个对象数组进行排序，前提是对象数组中的元素(对象)需要实现Comparable接口，这样才可以将两个对象进行比较。例如，String.compareTo方法可以按字典顺序比较字符串，这是由于String类实现了Comparable\<String>。

​	现在，我们希望能够按字符串长度进行排序，显然我们无法对String类进行修改。

​	如何处理这种情况呢，在Arrays.sort方法中还有第二个版本，有一个数组和一个**比较器**(comparator)作为参数，比较器是实现了Comparator接口的类的实例。

```java
public interface Comparator<T>{
	int compare(T first ,T second);
}
```

```java
class LengthComparator implements Compararot<String>{
    public int comapre(String first,String second){
        return first.length()-second.length();
    }
}
```

```java
var comp=new LengthComparetor();
if(comp.compare(words[i],words[j]>0)...
```

将这个与words[i].comapreTo(words[j])进行比较，可以看出，这个compare是在比较器上调用。

要对一个数组进行排序，要为Arrays.sort()方法传入一个LengthComparator对象。

````java
String friends=new String("Peter","Mary","Paul");
Arrays.sort(friends,new LengthComparator());
````

**理解了这个例子我们学习lamda表达式才能更加容易**

## 对象克隆

Cloneable接口，用的比较少，但是这里的技术性很强。

为了解决什么问题，首先我们所学过创建一个包含对象引用的变量的副本，原对象和副本都是同一个对象的引用。如下

```java
Employee original=new Employe("jack",22,4000);
Employee copy=original;
copy.raiseSalary(10);
```

我们希望有一个对象的副本但不影响对象的后续状态

```java
Employee copy=original.clone();
copy.raiseSalary(10);
```

从下面这张图，我们直观地理解克隆的过程。

![](https://gitee.com/aryangzhu/picture/raw/master/%E5%85%8B%E9%9A%86.jpg)

​	通常说的克隆是“浅拷贝“即对象中引用的子对象并不会克隆，而会直接引用。这个时候，我们考虑的应当是子对象是否是可变的，如果子对象是final的那么就可以确保安全性，或者子对象没有修改器或者被引用的可能，那么同样也是安全的。例如，String是不可变的，共享就是安全的。

​	但是，通常子对象都是可变的，图中的hireDay是Datel类的对象，所以要克隆的话需要重新定义clone方法来进行深拷贝。                                                                                 

对于每一个类，要确定

1.默认的clone方法是否够用

2.是否可以在可变的子对象上调用clone()方法来修补默认的clone()方法。

3.是否不应该使用克隆。

如果前两项成立

1.实现Cloneable接口

2.重新定义clone方法，并指定public 访问修饰符。

**注意：**

1.**protected限制是同一个包下的类可以访问超类的字段**，所以，Date类型的hireDay肯定无法直接由Employee来clone()，引用对象需要单独复制。

2.Cloneable是一个标记接口，不包含任何方法，但是可以使用instanceof关键字。

# lamda表达式

## 为什么引入lamba表达式

lamba表达式是一个可传递的代码块

例如之前的TimerPrinter，可以构造一个实例提交到Timer对象。

再或者想要自己定制一个比较器，可以向数组传递一个Comparator对象

上面两者的共同特征是将一个代码块传递到到某个对象(定时器，Arrays的sort方法)

## lamba表达式的语法

用排序来说明，first.length()-second.length()使我们的主要任务。

```java
(String first,String second)
	->first.length()-second.length()
```

为什么被称为lamba，来源也很有趣

**表达形式**:参数、箭头(->)以及一个表达式

### 几种常见的用法

1.在{}中编写程序,如果需要运行的程序无法通过一个表达式完成，则应该放在{}中

```java
(String first,String second)->{
	if(first.length()>second.length()) return 1;
    else if (first.lenght()<second.length()) return -1;
    else return 0;
}
```

2.如果表达式没有参数，也要保留空括号，就像无参数的函数一样

```java
()->{for(int i=100;i>=0;i--){
		System.out.println(i);
	}
}
```

3.如果可以推导出参数的值，那么就可以忽略

```java
Comparator<String> comp=(first,second)->first.length()-second.length();
```

这里编译器能够推导出来first和second都是String字符串类型的。

4.如果只有一个参数，而且这个参数是可以推导出来的，那么可以省略小括号。

```java
Action listener=event->{
    System.out.println("At the tone,the time is..."+
                       Instant.ofEpochMilli(event.getWhen()));
}
```

### 函数式接口

Java中有许多接口中都有封装代码块，和lamba表达式兼容，接口中必须有且仅有一个**抽象方法**，并且在使用时可转换为lamba表达式的接口被称为函数式接口(Comparator和ActionListener)。

例如，之前的Arrays.sort方法

```java
Arrays.sort(friends,new LengthComparator());
```

Comparator接口转化为lamba表达式

```java
Arrays.sort(friends,( first, second)->first.length()-second.length()); //由于sort(Stirng str,Comparator<String>)会进行判断，所以前面的小括号中就不用写first和second的类
```

在sort底层，将接受一个实现了Comparator\<String>的类的对象，并在这个对象上调用compare方法执行lamba表达式主体。

再来看看ActionListener接口

```java
class TimePrinter implements AcitonListerner{
	public void acitionPerformed(ActionEvent event){
		System.out.println("At the tone,the time is..."+Instance.ofEpochMilli(event.getWhen()));
		Toolkit.getDefaultToolkit().beep();
	}
}

 Timeprinter listener=new Timeprinter();
 Timer timer = new Timer(1000 ,listener);
```

我们将其转化为lamba表达式

```java
var Timer =new Timer(1000,event->{
    	System.out.println("At the tone,the time is..."+Instance.ofEpochMilli(event.getWhen()));
		Toolkit.getDefaultToolkit().beep();
});
```

**lambda表达式最大的用途就是函数式接口,而且lambda表达式是一个函数而不是一个对象**

####  Java API在java.util.function包中定义的通用的函数式接口

##### Predicate接口

```java
public interface Predicate{
	boolean test(T t);
}
```

ArrayList类有一个removeIf方法，它的参数就是一个Predicate。这个接口专门用来传递lambda表达式。例如删除数组列表中的所有空值。

```java
list.removeIf(e->e==null);
```

## 方法引用

来看之前的一个例子

Timer timer=new Timer(1000,event->System.out.println(event));

直接将println()传递到Timer构造器

Timer timer=new Timer(1000,System.out::println);

表达式System.out::println是一个**方法引用(method reference)**,它指示编译器生成一个函数式接口的实例，并且覆盖掉接口中的方法，在上面的代码中，会生成一个ActionListener的实例，并且覆盖掉actionPerformed(ActionEvent e)方法，调用System.out.println(e);

**方法引用也不是一个对象**

### 使用::运算符的三种情况

#### 1.object::instanceMethod

对象::实例方法，相当于向方法传递参数的lambda表达式，对于System.out:println,对象是System.out,等价于x->System.out.println(x)

#### 2.Class::instanceMethod

类:实例方法，::之前的参数是隐式参数。例如，String::compareToIgnoreCase等同于(x,y)->x.compareToIgnoreCase(y)

#### 3.Class:StaticMethod

所有参数都传递到静态方法，例如Math::pow等价于(x,y)->Math.pow(x,y)

## 引用构造

## 变量的作用域

通过代码更加直观

```java
public static void repeatMessage(String text,int delay){
    ActionListener listener=event->{
        System.out.println(text);
		Toolkit.getDefaultToolkit().beep();
    }
    new Timer(delay,listener.start());
}
```

方法调用reapeatMessage("Hello",1000);

从上面的代码中我们可以知道text并不是lambda表达式中的值，而且lambda表达式的值有可能在repeatMessage方法返回参数许久之后才执行，如何保存text值？

lambda表达式一共有三个部分

1.一个代码块

2.参数

3.自由变量的值，就是在lambda表达式的代码块中没有出现过的值。

lambda表达式的数据结构必须保存自由变量的值，在上面的例子中就是“Hello”,这里称之为lambda表达式对于自由变量的“捕获”。

我们来看具体的实现细节，首先，**自由变量只能引用值而不能修改值**

```java
public static void repeatMessage(int start,int delay){
    ActionListener listener=event->{
        System.out.println(start--); //error can't captured
		Toolkit.getDefaultToolkit().beep();
    }
}
```

这是因为在并发执行的环境下会出现安全性问题。

**在lambda表达式内部引用的值如果在外部可以更改也是不合法的**

```Java
public static void repeatMessage(String text,int delay){
    for(int i=0;i<100;i++){
    	ActionListener listener=event->{
       		System.out.println(i+" "+text); //error can't captured
			Toolkit.getDefaultToolkit().beep();
    	}
	}
}
```



## 处理lambda表达式

使用lambda表达式的重点是**延迟执行(deferred execution)**

要延迟执行的情况可能有以下:

1.在一个单独的线程中执行代码

2.多次运行代码 

3.在算法的适当位置使用(排序时的比较)

4.发生某个事件时使用(点击了按钮)

5.只在必要时才运行的代码

假设有一种情况是我们需要循环执行一个操作

repeat(10,()->System.out.println("Hello world!"));

要执行这个lambda表达式就要有一个函数式接口，书上使用的是Runnable接口。

```java
public static void repeat(int n,Runnable action){
    for(int i=0;i<n;i++){
        action.run();
    }
}
```

我们知道Runnable是实现多线程操作时需要类实现的一个接口,既然启动了别的线程说明只有action.run()被调用时才会执行这个lambda表达式的主体。

为了让例子复杂一些，我们希望告诉它在哪次迭代中需要执行这个动作,需要选择一个合适的函数式接口。

```java
public interface IntConsumer{
    void accept(int value);
}
```

重写一下这个repeat方法

```java
public static  void repeat(int n,intConsumer action){
    for(int i=0;i<n;i++)action.accept(i);
}
```

转换为lambda表达式

```java
repeat(10,i->System.out.println("CountDown"+(9-i)));
```

## 再谈Comparator

Comparator接口中包含许多**静态方法**来创建比较器。这些方法可以用lambda表达式或方法引用。

静态comparing方法是一个“键提取器”函数，**它将类型T映射为一个可比较的类型**(如String)。

对要比较的对象应用这个函数，然后对**返回的键完成排序**。

例如，Arrays.sort(people,Comparator.comparing(Person::getName));

根据名字来排序,与手动实现Comparator相比较，代码更加直观。

也可以将比较器与thenComparing方法连接起来，来处理结果相同的情况，就是当前一个比较结果一致的时候在继续用别的条件进行比较。

```java
Arrays.sort(people,
            Comparator.comparing(Person::getName)
           .thenComparing(Person::getFirstName));
```

很多方法还有变体形式,可以为comparing和thenComparing方法提取的键指定与一个比较器，就像套娃一样，例如，根据姓名长度排序，姓名&长度

```java
Arrays.sort(people,Comparator.comparing(Person::getName,
       (s,t)->Integer.compare(s.length().t.length())));
```

另外的comparing和thenComparing方法的变体，可以避免int、long或者double值的装箱,也就是我们前面按长度比较时需要装箱(个人猜测)。

```java
Arrays.sort(people,Comparator.comparing(p->p.getName().length()));
```

最后，来看一下这个究极复杂的代码

```java
Arrays.sort(people,comparing(Person::getMiddleName,nullFirst(naturlOrder())));
```

尝试理解一下，如果中名为空,那么getMiddleName()返回的就是null,这时我们使用nullFirst()适配器来增加条件使用比较器不会抛出异常。nullFirst()方法需要一个字符串比较器，而naturalOrder()可以为任何实现了Comparator的类建立一个比较器。注意:naturalOrder()的类型可以导出，即Compator\<String>naturalOrder()。

# 内部类

内部类就是定义在其他类中的类。为什么使用内部类？

1.内部类可以对同一个包中的其他类隐藏。

2.内部类方法可以访问定义在这个类的作用域中的类，包括原有的私有方法。

## 使用内部类访问对象状态

与C++相比，Java内部类的对象会有一个隐式的引用，指向实例化这个对象的外部类对象。通过这个指针，它可以访问外部类的全部状态。

书上的例子是重构了TimerTest，抽象出一个TalkingClock，语音时钟，需要两个参数，发出通知的间隔和开关铃声的标志。

```java
public class TalkingClock{
    private int interval;
    private boolean beep;
    
    public TalkingClock(int interval,boolean beep){...}
    public void start(){...}
    
    public class TimerPrinter implements ActionListener{
        ....
    }
}
```

虽然TimerPrinter位于TalkingClock的内部，但是并不意味着每个TalkingClock对象都有TimerPrinter实例字段。TimerPrinter是在TalkingClock的方法中构造的(也就是说需要用到这个类的对象时才会构造，听起来像是废话)。

actionpPerformed方法在发出铃声之前会检查beep标志。

```java
public class TimerPrinter implements ActionListener{
    public void acitonPerformed(Action event){
        System.out.println("At the thone the time is"+
        Instant.ofEpochMilli(event.getWhen()));
        if(beep){
            Toolkit.getDefaultToolkit().beep();
        }
    }
}
```

从代码中可以看出TimerPrinter中并没有beep的实例字段，但是却可以访问TalkingClock中的beep。说明内部类的对象总有一个隐式引用，可以访问外部类对象。

我们将外围类称为outer,上面的代码可以等价为

````java
if(outer.beep){
            Toolkit.getDefaultToolkit().beep();
        }
````

外围类的引用由构造器设置。编译器会修改内部类的构造器，添加当前外围类的参数。

TimerPrinter中没有构造器，所以编译器会默认生成一个无参的构造器(???书上的这段话让我很迷惑,既然无参那么clock算什么)。

```java
public TimerPrinter(TalkingClock clock){//auto generated code
    outer=clock;
}
```

outer并不是Java中的关键字，只是为了方便我们理解。

在start方法中构造TimerPrinter之后，由于之前生成了内部类的构造器，这时会将当前与语音时钟的this引用传入TimerPrinter的构造器中。

## 内部类的特殊语法规则

### 1.OuterClass.this

这是正规语法，例如

```java
...
if(Talking.this.beep)...
```

### 2.outerObject.new innerClass(construction parameters)

```java
ActionListener listener=this.new TimePrinter();
```

假设IimePrinter是公共内部类，对于任意的语音始终都可以构造TimePrinter

```java
TalkingClock jabber=new TalkingClock(1000,true);
TalkingClock.TimePrinter timer=jabber.new TimerPrinter();
```

### 3.几个注意的点

#### 1.外围类的作用域之外调用内部类

outerClass.innerClass

#### 2.内部类中的静态字段都必须是final,并初始化为一个编译时常量

编译时常量指的是程序在编译时就能确定常量的具体指，与之对应的是运行时常量，例如

```java
public final int a = 1;     //编译时常量
public final static int d = 10;   //编译时常量 
public final static int d = 10;   //编译时常量 
public final static String str4 = "static str";  //编译时常量 
public final double e = Math.random(); //运行时常量
```

运行时常量是程序运行时才能确定的值。

#### 3.内部类中不允许有static方法

## 内部类是否有用、必要和安全



内部类是一个**编译器现象**,与虚拟机无关。编译器将会把内部类转换为常规的类文件，用$分隔外部类名与内部类名，而虚拟机则对此一无所知。

例如，TalkingClock类内部的TimePrinter类将被转换成类文件TalkingClock$TimePrinter.class。

![](https://gitee.com/aryangzhu/picture/raw/master/java/%E5%86%85%E9%83%A8%E7%B1%BB%E6%B5%8B%E8%AF%95.png)

可以看到，**编译器中生成了一个额外的实例字段this$0**,对应外围类的引用(名字this$0是编译器合成的，在你自己编写的代码中不能引用这个字段。)同时，构造器中也有TalkingClock参数。

能不能自己动手实现编译器的转换？假设我们将TimePrinter放在TalkingClock的外部，在构造TimePrinter对象的时候，传入它的对象的this指针。

肯定是有问题的，因为内部类可以访问外部类的私有字段，但是如果实在类的外面的话，那么就不能访问私有字段beep。

 那么问题来了，内部类是如何得到超越自身类的访问权限的呢？

![](https://gitee.com/aryangzhu/picture/raw/master/java/TalkingClock.png)

