## 接口
接口是一种技术，用来描述类应该做什么，但是不指定如何做。**一个类可以实现一个或者多个接口。**然后介绍lambda表达式，再讨论内部类(定义在其他类的内部，可以访问外部类的字段)，最后学习反射机制。
### 接口的概念
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
##### 常用API
java.lang.Comparable\<T>  
​	int comparableTo(T other) 将**这个对象与other进行比较**  
java.util.Arrays  
​	static void sort(Object[] a) 对数组中**a中的元素进行排序**。要求**数组中的元素必须属于实现了Comparable接口的类**，元素之间也要能够进行比较。  
java.lang.Integer  
​	static int compare(int x ,int y) **如果x<y返回一个负整数;如果x和y相等，则返回0**;否则返回一个正整数。  
java.lang.Double  
​	static int compare(double x,double y) **如果x<y返回一个负整数**;如果x和y相等则返回0;否则返回一个正整数。  
注:sgn(x.compareTo(y))=-sgn(y.compare(x))，如果前者有异常的话，那么后者也应该出现异常。这里的“sgn”可以理解为一种规则，即如果x>y的话，那么结果就是1。  
如果Manager扩展了Employee,Employee实现的是Comparable\<Employee>,而在Manager中实现的是Comparble\<Manager>,Employee ep=new Employee(); Manager manager =new Manager();如果是ep.compareTo(manager)，那么没什么问题，因为经理也是员工。但是，如果manager.compareTo(ep),那么就会出现ClassCastException。

```java
class Manager extends Employtee{
	public int compareTo(Employee other){
		Manager m=(Manager) other; //很明显，这里是向下转型，会出问题
	}
}
```
### 接口的属性
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
### 接口与抽象类 
在这里，我们提出了一个问题，既然抽行类也有定义需求的功能，为什么还要这么麻烦地引入接口呢？  
这是因为类在设计时就只允许只能一个一个类，而一个类却可以实现多个接口。  
注：C++设计者允许一个类有多个超类，但是Java不支持多重继承。  
#### 共同点 
1.都不能被实例化  
2.都可以包含抽象方法  
3.都可以有默认实现的方法  
#### 区别
1.接口规范行为,抽象类强调的是所属关系  
2.一个类只能继承一个类,但是可以实现多个接口  
3.抽象类的成员变量默认default,可在子类中被重新定义,也可以被重新赋值。  
### 静态和私有方法
java8中允许在接口中增加静态方法，只是一般将静态方法放在伴随类中，所以我们**经常会在标准库中见到成对出现的接口和实用工具类**。  
Java9中接口中方法可以是private，**private可以是静态或者实例方法，但是只能在接口本身中使用，用法有限，一般作其他方法的辅助方法**。  
### 默认方法
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
### 默认方法解决冲突  
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

### 接口与回调

**回调(callback)是一种常见的程序设计模式**。在这种模式中，你可以指定某个特定事件发生后执行的操作。例如，点击菜单选项之后完成的某个特定动作。

在java.swing包中有一个Timer类，非常有用。

构造定时器时，需要设置时间间隔，并指定需要干什么。

在Java中，我们可以向定时器中传入一个对象，因为对象携带的信息要比单单一个参数更多，从代码中可以看出传递的是一个监听器对象，这个监听器对象干了什么事儿呢，它打印了语句，同时执行了beep()，这也是面向对象的优势。

我们要知道**定时器要调用哪个方法，并要求传递的对象所属的类实现了java.awt.event包下的ActionListener接口**。

顺便一提，new Timer(1000 ,listener);是构造器，而构造完成的是定时器。

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
Timer构造器传入的参数第一个是时间间隔，第二个是监听器对象

```java
Timeprinter listener=new Timeprinter();
Timer timer = new Timer(1000 ,listener);
timer.start();
```
通过start()来启动定时器
### Comparator接口
​之前我们已经了解了如何对一个对象数组进行排序，前提是对象数组中的元素(对象)需要实现Comparable接口，这样才可以将两个对象进行比较。例如，String.compareTo方法可以按字典顺序比较字符串，这是由于String类实现了Comparable\<String>。  
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
### 对象克隆Cloneable
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
​	但是，通常子对象都是可变的，图中的hireDay是Date类的对象，所以要克隆的话需要重新定义clone方法来进行深拷贝。                                                                                 
对于每一个类，要确定  
1.默认的clone方法是否够用  
2.是否可以在可变的子对象上调用clone()方法来修补默认的clone()方法。  
3.是否不应该使用克隆。  
如果前两项成立  
1.实现Cloneable接口  
2.重新定义(覆盖掉Object类的方法)clone方法，并指定public 访问修饰符。  
**注意：**
1.**protected限制是同一个包下的类可以访问超类的字段**，所以，Date类型的hireDay肯定无法直接由Employee来clone()，引用对象需要单独复制。  
2.Cloneable是一个标记接口，不包含任何方法，但是可以使用instanceof关键字来检查。
## lamda表达式
### 为什么引入lamba表达式
**<font color="blue">lamba表达式是一个可传递的代码块</font>**  
例如之前的TimerPrinter，可以构造一个实例提交到Timer对象。  
再或者想要自己定制一个比较器，可以向数组传递一个Comparator对象  
上面两者的共同特征是将一个代码块传递到到某个对象(定时器，Arrays的sort方法)
### lamba表达式的语法  
用排序来说明，first.length()-second.length()使我们的主要任务。

```java
(String first,String second)
	->first.length()-second.length()
```  
为什么被称为lamba，来源也很有趣  
**表达形式**:参数、箭头(->)以及一个表达式  
#### 几种常见的用法  
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

#### 函数式接口

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

#####  Java API在java.util.function包中定义的通用的函数式接口

###### Predicate接口

```java
public interface Predicate{
	boolean test(T t);
}
```

ArrayList类有一个removeIf方法，它的参数就是一个Predicate。这个接口专门用来传递lambda表达式。例如删除数组列表中的所有空值。

```java
list.removeIf(e->e==null);
```

### 方法引用

来看之前的一个例子

```java
Timer timer=new Timer(1000,event->System.out.println(event));
```

直接将println()传递到Timer构造器

```java
Timer timer=new Timer(1000,System.out::println);
```

表达式System.out::println是一个**方法引用(method reference)**,**它指示编译器生成一个函数式接口的实例，并且覆盖掉接口中的方法**，在上面的代码中，会**生成一个ActionListener的实例，并且覆盖掉actionPerformed(ActionEvent e)方法**，调用System.out.println(e);

**方法引用也不是一个对象**,但是为一个类型为函数式接口的变量赋值时会生成一个对象。

PrintStream类(System.out就是PrintStream类的一个实例)中有10个重载的println方法。编译器需要根据上下文确定使用哪一个方法。在上面的例子中，方法引用System.out.println必须转换为一个包含以下方法的AcitonListener实例:

void actionPerformed(ActionEvent e)

这样会从10个重载的printl方法中选出println(Object x)方法，因为Object与ActionEvent最匹配。调用actionPerformed方法时，就会打印出这个事件对象。

假设，将同样的方法引用赋至一个不同的函数式接口:

Runable task=System.out::println();

这个Runable函数式接口有一个无参数的抽象方法:

void run()

这里会选择无参数的prinlnt()方法，调用task.run()方法会向System.out打印一个空行。

#### 使用::运算符的三种情况
##### 1.object::instanceMethod
对象::实例方法，相当于向方法传递参数的lambda表达式，对于System.out:println,对象是System.out,等价于x->System.out.println(x)  
##### 2.Class::instanceMethod
类::实例方法，::之前的参数会成为方法的隐式参数。例如，String::compareToIgnoreCase等同于(x,y)->x.compareToIgnoreCase(y)  
##### 3.Class:StaticMethod
所有参数都传递到静态方法，例如Math::pow等价于(x,y)->Math.pow(x,y)  
### 引用构造
### 变量的作用域
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

3.**自由变量的值**，就是在lambda表达式的代码块中没有出现过的值。

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



### 处理lambda表达式

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

### 再谈Comparator

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

## 内部类

内部类就是定义在其他类中的类。为什么使用内部类？

1.内部类可以对同一个包中的其他类隐藏(也就是说需要在内部完成一些工作而不想被外部访问)。

2.内部类方法可以访问定义在这个类的作用域中的类，包括原有的私有方法。

### 使用内部类访问对象状态

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

**外围类的引用由构造器设置**。编译器会修改内部类的构造器，添加当前外围类的参数。

TimerPrinter中没有构造器，所以编译器会默认生成一个无参的构造器(???书上的这段话让我很迷惑,既然无参那么clock算什么)。

```java
public TimerPrinter(TalkingClock clock){//auto generated code
    outer=clock;
}
```

outer并不是Java中的关键字，只是为了方便我们理解。

在start方法中构造TimerPrinter之后，由于之前生成了内部类的构造器，这时会将当前与语音时钟的this引用传入TimerPrinter的构造器中。

### 内部类的特殊语法规则

#### 1.OuterClass.this

这是正规语法，例如

```java
...
if(Talking.this.beep)...
```

#### 2.outerObject.new innerClass(construction parameters)

```java
ActionListener listener=this.new TimePrinter();
```

假设IimePrinter是公共内部类，对于任意的语音始终都可以构造TimePrinter

```java
TalkingClock jabber=new TalkingClock(1000,true);
TalkingClock.TimePrinter timer=jabber.new TimerPrinter();
```

#### 3.几个注意的点

##### 1.外围类的作用域之外调用内部类

outerClass.innerClass

##### 2.内部类中的静态字段都必须是final,并初始化为一个编译时常量

编译时常量指的是程序在**编译时就能确定常量的具体指与之对应的是运行时常量**，运行时常量是程序运行时才能确定的值，例如

```java
public final int a = 1;     //编译时常量
public final static int d = 10;   //编译时常量 
public final static int d = 10;   //编译时常量 
public final static String str4 = "static str";  //编译时常量 
public final double e = Math.random(); //运行时常量
```

##### 3.内部类中不允许有static方法

### 内部类是否有用、必要和安全

内部类是一个**编译器现象**,与虚拟机无关。编译器将会把内部类转换为常规的类文件，用$分隔外部类名与内部类名，而虚拟机则对此一无所知。

例如，TalkingClock类内部的TimePrinter类将被转换成类文件TalkingClock$TimePrinter.class。

![](https://gitee.com/aryangzhu/picture/raw/master/java/%E5%86%85%E9%83%A8%E7%B1%BB%E6%B5%8B%E8%AF%95.png)

可以看到，**编译器中生成了一个额外的实例字段this$0**,对应外围类的引用(名字this$0是编译器合成的，在你自己编写的代码中不能引用这个字段。)同时，构造器中也有TalkingClock参数。

能不能自己动手实现编译器的转换？假设我们将TimePrinter放在TalkingClock的外部，在构造TimePrinter对象的时候，传入它的对象的this指针。

肯定是有问题的，因为内部类可以访问外部类的私有字段，但是如果实在类的外面的话，那么就不能访问私有字段beep。

 那么问题来了，内部类是如何得到超越自身类的访问权限的呢？

![](https://gitee.com/aryangzhu/picture/raw/master/java/TalkingClock.png)

编译器在外围类添加了静态方法access$000，将返回作为参数传递的那个对象的beep字段。

if(beep)实际上会产生以下调用

if(TalkingClock.access$000(outer))

这里会有安全性问题，因为任何人都可以通过access$000方法来访问外部类的私有字段beep。黑客可以使用16进制编辑器创建一个类，再通过虚拟机指令调用那个方法。

下面，我们来描述一下编译器如何构造私有内部类。

假设将一个TimePrinter转换为一个私有内部类。在虚拟机中不存在私有类，因此编译器会生成一个具有包可见性(default默认)的类，其中有一个私有构造器‘

```java
private TalkingClock$TimePrinter(TalkingClock)
```

由于是私有的所以没有人可以调用这个构造器，因此，还有第二包可见的构造器:

```java
TalkingClock$TimePrinter(Talking Clock,TalkingClock$1)
```

这个构造器将会调用第一个构造器。合成TalkingClock$1类只是为了将这个构造器与其他构造器区分开。

我们在start方法中，有

```java
var listener=new TimePrinter();
```

将会转换为

```java
new TalkingClock$TimePrinter(this,null);
```

### 内部局部类

局部常见的就是在方法中定义，TimePrinter只在start()方法中出现了一次，所以遇到这种情况时，可以在一个方法中**局部地定义这个类**。

```java
public void start(){
	class TimerPrinter implements ActionListener{
    	public void acitonPerformed(Action event){
        System.out.println("At the thone the time is"+
        Instant.ofEpochMilli(event.getWhen()));
        if(beep){
            Toolkit.getDefaultToolkit().beep();
        	}
    	}
	}
    var listener=new TimePrinter();
    ...
}
```

声明局部类时不能有访问说明符(即public或者private)。局部类的作用域被限定在局部类所在的块中。

优势:完全对外隐藏，除了start方法之外没有任何方法知道TimePrinter类的存在。

### 由外部方法访问变量

与其他内部类相比，局部类不但能够访问外部类的字段，还可以访问局部变量！不过，那些局部变量必须是**事实最终变量**(effectively final),就是说一旦赋值就不能更改。个人理解和final是有区别的，final是不能指向别的引用，对象本身是可以进行修改。

下面的代码将原本在构造器中参数interval和beep放在start方法中。

```java
public vodi start(int interval,boolean beep){
    class TimerPrinter implements ActionListener{
    	public void acitonPerformed(Action event){
        System.out.println("At the thone the time is"+
        Instant.ofEpochMilli(event.getWhen()));
        if(beep){
            Toolkit.getDefaultToolkit().beep();
        	}
    	}
	}
    var listener=new TimePrinter();
    ...
}
```

TalkingClock类**不再需要存储实例变量beep**(这句话很重要)。局部类只是引用start中的参数。

可能有的人会说，这跟普通的代码没什么区别，为什么还要再写一遍呢？

注意:**这里实现的是定时语音，也就是说会隔一段时间触发一次，但是start方法执行完之后beep将会消失，这是问题的关键**。

为了更容易理解，我们来看一下整个控制流程:

1.调用start;

2.调用内部类TimePrinter的构造器，初始化listener变量；

3.将listener引用传递给Timer构造器，定时器开始计时，start方法退出，beep不复存在。

4.1s后，actionPerformed方法执行if(beep)...

为了能够让actionPerformed方法工作，TimePrinter类在beep参数消失之前复制为start方法的局部变量(可以看看对象和类那儿方法参数的笔记)。



下面这段话可能很拗口

请注意构造器的boolean参数和var$beep实例变量。当创建一个对象时，beep值就会传递给构造器，并存储在var$beep字段中(也就是说对象中会有形如var$beep的字段)。**编译器检测对局部变量的访问(就是说在编译时已经注意到了对于beep局部变量的访问)，为每一个变量建立相应的实例字段，并将局部变量复制到构造器(内部类的构造器)，从而能够初始化这些字段**(个人理解是编译器会生成一个构造器里面有这个参数，图上的结果中确实也有)。

### 匿名内部类

使用局部内部类时如果还想更进一步，**只想创建对象**，那么就不需要指定类的名字。这样的一个类被称为**匿名内部类**。

```java
public vodi start(int interval,boolean beep){
    var listener=new ActionListener(){
        System.out.println("At the tone the time is"+Instant.ofEpochMilli(event.getWhen());
       	if(beep){
            Toolkit.getDefaultToolkit().beep();
        }
    };
    var timer=new Timer(interval,listener); 
    timer.start();
}
```

含义是创建一个类的对象，这个类实现了ActionListener接口，并且需要实现的actionPerformed方法放在{}中实现。

一般语法

```java
new SuperType(construction parameters){
    inner class methods and data
}
```

**SuperType可以是接口，也可以是一个类，如果是类，内部类就要扩展这个类**。

由于匿名内部类没有类名，而构造器必须与类名相同，所以匿名内部类没有构造器。实际上，**构造参数要传递给超类(superclass)构造器**。

具体地，只要内部类实现了一个接口，就不能有任何构造参数。

```java
new InterfaceType(){
	...
}
```

一个类的新对象和构造一个扩展了这个类的匿名内部类的区别

```java
Person person=new Person("Jack");
Person count=new Person("Marry"){...};
```

从上面的代码中很容易能够看出来，匿名内部类后面有{}

注:匿名内部类不能有构造器，但可以提供一个对象初始化块

```java
var count=new Person("Marry"){
    {initialzation}
};
```

警告:建立一个与超类大体类似的匿名子类通常会很方便。不过，对于equals方法要特别当心。之前的equals方法使用了下面的测试

```java
if(getClass()! =other.class){
    return false;
}
```

对于匿名子类做这个测试会失败。

### 静态内部类

如果使用内部类只是为了把一个类隐藏在另外一个类的内部，并不需要内部类有外部类对象的一个引用。为此，**可以将内部类声明为static,这样就不会生成那个引用**(外围类对象)。

下面来看一个典型的例子。考虑这样一个任务:计算数组中的最小值和最大值。当然，可以编写两个方法，一个方法用于计算最小值，一个方法用于计算最大值。在调用这个方法的时候，数组被遍历两遍。如果只需要遍历数组一次，并能够同时计算出数组的最大值和最小值，则可以提高效率。

```java
double min=Double.POSITIVE_INFINITY;
double max=Double.NEGATIVE_iNFINITY;
for(double v:value){
    if(v<min)min=v;
    if(v>max)max=v;
}
```

由于需要同时返回最大值和最小值，所以可以定义一个包含两个值的类Pair。

```java
class Pair{
    private double first;
    private double second;
    
    public Pair(double f,double s){
        first=f;
        second=s;
    }
    
    public double getFirst(){
        return first;
    }
    
    public double getSecond(){
        return second;
    }
}
```

minmax方法可以反回一个Pair类型的对象。

```java
class ArrayAlg{
    public static Pair minmax(double[] values){
        ...
        return new Pair(min,max);
    }
}
```

这个方法的调用这可以使用getFirst和getSecond方法获得答案

```java
Pair p=ArrayAlg.minmax(d);
System.out.println("min="+p.getFirst());
System.out.println("max="+p.getSecond());
```

通常由于Pair这个类名的普遍性，所以容易造成类名的混淆(在一个大项目中，其他人也定义了这个类，可能里面定义了两个字符串字段)。为了解决这个冲突，将Pair定义为ArrayAlg的一个公共内部类(公共的意思就是用public来进行修饰)，就可以通过Arrayalg.Pair访问它了。

```java
ArrayAlg.Pair p=ArrayAlg.minmax(d);
```

不过，与之前的例子所使用的内部类不同，在Pair对象中不需要任何其他对象的引用，为此，可以将这个内部类声明为static，从而不生成那个引用:

```java
class ArrayAlg{
    public static class Pair{
        ...
    }
    ...
}
```

在上面的实例中，必须使用静态内部类，因为内部类对象实在静态方法中构造的。

如果没有将Pair声明为static,那么编译器将会报错，指出没有可用的隐式ArrayAlg类型对象来初始化内部类对象。

#### 注意

1.凡是不需要访问外围类对象的内部类就应该是静态内部类。

2.之前我们提到过内部类不允许有静态方法，静态内部类可以有静态字段和方法。

3.在接口中声明的内部类自动是public和static。

## 服务加载器
通常提供一个服务时，程序希望服务设计者能有一些自由，能够确定如何实现服务的特性。另外还希望有多个实现以供选择。**利用ServiceLoader类可以很容易地加载符合一个公共接口的服务**。  
假设有一个接口，其中包含服务的各个实例应当提供的方法。

```java
package serviceLoader;

public interface Cipher{
	byte[] encrypt(byte[] source,byte[] key);
    byte[] decrypt(byte[] source,byte[] key);
    int strlength();
}
``` 
服务提供者可以提供一个或者多个实现这个服务的类，例如，  
```java
package serviceLoader.impl;
public calss CaesarCipher implements Cipher{
    ...
}
```  
实现类可以放在任意的包中，而不一定是服务接口所在的包。**每个实现类必须有一个无参数构造器**(类会调用超类的无参构造器，而接口必须有无参构造器)。  
现在，将类名增加至META-INF/services目录下的一个UTF-8编码文本文件中，**文件名必须与完全限定类名一致**。  
程序可以如下初始化一个服务加载器:  
```java 
public static ServiceLoader<Cipher> cipher=ServiceLoader.load(Cipher.class);
```  
这个初始化只在程序中完成一次。  
服务加载器的iterator方法会返回一个迭代器来处理所提供服务的所有实现(实现类)。最容易的是通过一个增强的for循环来进行遍历，在循环中选择一个适当的。  
```java
public static Cipher getCipher(int minStrength){
    for(Cipher cipher:cipherLoader){ //implicitly calls cipherLoader.iterator()
        if(cipher.strength()>=minStrength){
            return cipher;
        }
    }
}
```
也可以使用stream流(数据集合)来查找所要的服务。**stream方法会生成ServiceLoader.Provider实例的一个流**。  
```java
public static Optional<Cipher> getCipher2(int minStrength){
    return cipherLoader.stream()
        .filter(descr->descr.type()==serviceLoader.impl.CaesarCipher.class)
        .findFirst()
        .map(ServiceLoader.Provider::get);
}
```

最后，如果想要得到任何服务实例，只是需要调用findFirst。  
Optional\<Cipher> cipher=cipherLoader.findFirst();  
#### 常用API  
##### java.util.ServiceLoader\<S>  
static \<S> ServiceLoader\<S> load(Class\<S> service)  
创建一个服务加载器来加载实现给定服务接口的类。  
Iterator\<S> iterator()  
生成一个以“懒”方式加载服务类的迭代器。也就是说，**迭代器推进时才会加载类**。  
Stream\<ServiceLoader.Provider\<S>> stream()  
返回提供者描述的一个流，从而可以采用懒方式加载所要的类的提供者。  
Optional\<S> findFirst()  
查找第一个可用的服务提供者(如果有)。  
##### java.util.ServiceLoader.Provider\<S>
Class\<? extends S> type()
获得这个提供者的类型。
S get()
获得这个提供者的实例。
## JDK动态代理
### 从静态代理开始
首先必须知道什么是静态代理,一般的场景是一个接口、一个接口的实现类,我们须手动编写这个实现类的代理类,示例代码如下
```java
public interface Product{
    void addPrice(String price);
}

public class Stone implements Product{
    public void addPrice(String price){
        //...
    }
}

public class Square implements Product{
    Stone stone=new Stone();

    public void addPrice(String price){
        stone.addPrice("1");
        //...
    }
}
```
静态代理的弊端???
1. 如果新增一个方法,那么代理类中也需要增加方法
2. 针对每个实现类都需要写一个代理类
利用动态代理(proxy)在运行时**创建了一组给定接口的新类**。只有在编译时无法确定需要实现哪个接口才需要使用代理。  
相比于静态代理来说，动态代理更加灵活。我们不需要针对每个目标类都**单独创建一个代理类**，并且也**不需要我们必须实现接口，我们可以直接代理实现类**( CGLIB 动态代理机制)
### 何时使用代理
假设我们需要创建一个类的对象，这个类可能实现了一个或者多个接口，但是在编译时不知道这些接口是什么。回想一下之前，如果是想要构造具体的类的话，那么我们可以里用newInstance或者反射来创建一个类的对象实例(通过找到构造器)。但是，**不能实例化接口**，需要在运行的程序中定义一个新类。  
为了解决这个问题，有些程序会生成代码，将这些代码放在一个文件中，调用编译器，然后再加载得到类文件。但是，这样做的速度很慢，而且需要**将编译器连同程序一起部署**。 
而代理机制是更好的解决方案，代理类可以在**运行**的时候由**JVM**创建全新的类。这样的代理类能够实现你指定的接口。具体的，代理类包含以下方法:  
1.指定接口所需要的全部方法     
2.Object类中的全部方法，例如，toString、equlas等。  
说实话，上面的这些话我看了半天还是不太理解，从网上的关于代理模式的博文中我有了更加直观的了解。  
https://xie.infoq.cn/article/9a9387805a496e1485dc8430f  
**代理类和委托类有相同的方法**，代理类为委托类做了**消息的预处理、过滤或者调用委托类的方法以及事后的处理**。  
![](https://gitee.com/aryangzhu/picture/raw/master/java/%E9%80%89%E5%8C%BA_025.png)
有几点我们需要注意:
1.**用户只在乎接口功能，而不关心是谁实现的**。
2.接口的真正实现者是**RealSubject，但是它不与用户直接接触**，而是通过代理。
3.代理就是上图中的Proxy，由于它实现了Subject,所以它可以和用户直接接触。
4.用户调用Proxy时，Proxy内部调用了RealSubject的方法,是对RealSubject的方法的增强。
### InvocationHandler接口
不能在运行时为这些方法提供新代码，必须提供一个**调用处理器**(invocation handler)。调用处理器是实现了**InvocationHandler**接口的类的对象。这个接口只有一个方法:  
```java
Object invoke(Object proxy,Method method,Object[] args)
```
分别有三个参数  
代理类 proxy  
调用方法 method  
方法参数 args  
无论何时调用代理对象的方法(proxy)，**调用处理器的invoke方法都会被调用，并向其传递Method对象和原调用的参数**。之后调用处理器必须确定如何处理这个调用。
### 创建代理对象
创建代理对象需要使用**Proxy**类的**newProxyInstance**(之前见过的类似的工厂方法)。**这个方法有三个参数**:
```java
public static Object newProxyInstance(ClassLoader loader,
Class<?>[] interfaces,
 InvocationHandler h)
throws IllegalArgumentException
    {
        ......
    }
```
这是Proxy类中newProxyInstance方法的注释
```java
//返回将方法调用分派到指定调用处理程序的指定接口的代理类的实例。
//Proxy.newProxyInstance抛出IllegalArgumentException的原因与Proxy.getProxyClass相同。
/**参数：
loader – 定义代理类的类加载器
interfaces – 代理类要实现的接口列表
h - 将方法调用分派到的调用处理程序
回报：
具有代理类的指定调用处理程序的代理实例，该代理类由指定的类加载器定义并实现指定的接口
**/
```
这是方法中的部分源码，可以看出，代理类是由类加载器定义并实现

```java
/*
* Look up or generate the designated proxy class.
*/
Class<?> cl = getProxyClass0(loader, intfs);
```
1.**一个类加载器**(class loader)。作为Java安全模型的一部分，可以对平台和应用类、从因特网上下载的类等使用的加载器。  
2.一个**Class对象数组，每个元素对应需要实现的各个接口**。  
3.一个**调用处理器(invocation handler)**。  
### JDK动态代理的使用步骤
1. **自定义接口和实现类,这一步在静态代理里面也得有**  
2. 定义InvocationHandler接口并重写invoke方法,**自己实现InvocaitonHandler接口的类中需要有是实现类**
3. 使用Proxy.newProxyInstance(...)方法调用  
#### 两个需要解决的问题:
##### 如何定义处理器？
##### 对于得到的代理对象能够做些什么？
这两个问题取决于我们想要通过代理机制解决什么问题，可能会有如下目的:  
1. 将方法调用路由到远程服务器(说实话不明白什么意思)。  
2. 为了调试，跟踪方法调用  
上面只是两种场景,还有一种场景,Spring整合Mybatis之后,只用编写xml文件,用屁股想也知道只有接口肯定是无法工作的,那么就一定是在运行过程中动态生成了Dao的实现类 
下面的例子中，我们使用代理和调用处理器跟踪方法调用。**我们定义了一个TraceHandler包装器类存储包装的对象**(将委托类包装在里面，查看委托类方法的调用情况)。其中的invoke方法会打印所调用方法的名称和参数，随后使用包装的对象作为隐式参数调用这个方法。  
```java
class TraceHandler implements InvocationHandler{
    private Object target;
    
    public TraceHandler(Object obj){
        target=t;
    }
    
    public void invoke(Object proxy,,Method m,Object[] args) throws Throwable{
        //print name and parameters
        ...
        //invoke actual method
        return m.invoke(target,args[]);
    }
}
```
接下来我们需要构造可以跟踪方法调用的代理对象

```java
Object value="...";

TraceHandler handler=new TraceHandler(value);

Class[] interfaces=new Class[]{Comparable.class};

Object proxy=Proxy.newProxyInstance(ClassLoader.getSystemClassLoader(),
                              new Class[]{Comparable.class},handler);
```
只要在Proxy上调用了某个接口的方法，就会打印这个方法的名字和参数，之后再用value调用这个方法。  
书上还有一个例子，使用代理对象跟踪一个二分查找。数组中填充整数1-1000的代理，调用Arrays类的binarySearch方法在数组张查找一个随机整数。最后打印匹配元素。  
```java
//填充1-1000的代理对象
for (int i = 0; i <elements.length; i++) {
     Integer value=i+1;
     TraceHandler handler=new TraceHandler(value);
     Object proxy=Proxy.newProxyInstance(ClassLoader.getSystemClassLoader(),new Class[]{Comparable.class},handler);
     elements[i]=proxy;
}

Integer key=new Random().nextInt(elements.length)+1;

int result= Arrays.binarySearch(elements,key);

if(result>0){
   	System.out.println(elements[result]);
}
```
Integer类实现了Comparable接口。代理对象属于运行时定义的一个类，**它也实现了Comparable接口**。不过，它的compareTo调用了代理对象处理器的invoke方法。  
### 代理类的特性
1.代理类总是在程序的运行过程中动态创建的。**一旦被创建之后，他们就变成了常规类，与虚拟机中的任何其他类没有区别**。  
2.所有代理类都扩展了Proxy类。**一个代理类只有一个实例字段-即调用处理器，它在Proxy超类中定义**。完成代理对象任务所需要的任何额外数据都必须存储在调用处理器中。例如，代理Comparable对象时，TraceHandler就包装了任务苏需要的实际对象(Object target)。  
3.如果没有定义代理类的名字，Oracle虚拟机中的Proxy类将生成一个以字符串$Proxy开头的类名。  
4.**对于特定的类加载器和预设的一组接口来说，只能有一个代理类**。也就是说，如果使用同一个类加载器和接口数组调用了两次newProxyInstance方法，将得到同一个类的两个对象。也可以利用Class ProxyClass=Proxy.getProxyClass(null,interfaces)来获取这个类。  
5.**代理类总是public和final**。因为如果代理类实现的所有接口都是public,这个代理类就不属于任何特定的包；否则，所有非公共的接口都必须属于同一个包，而代理类也必须属于这个包。  
6.可以通过调用Proxy类的isProxyClass方法检测一个特定的Class对象是否代表一个代理类。  
#### 常用API
##### java.lang.refelt.InvocationHandler
Object invoke(Object proxy,Method method,Object[] args)  
定以这个方法完成一个动作(增强过程)，你希望只要在代理对象上调用一个方法就完成这个动作。  
##### java.lang.refelt.Proxy
static Class\<?> getProxyClass(ClassLoader loader,Class<?>interfaces)  
返回实现指定接口的代理类  
static Object newProxyInstance(ClassLoader loader,Class\<?> interfaces,InvocationHandler handler)  
构造实现指定接口的代理类对象实例。所有方法都调用给定处理器的invoke方法。  
static boolean isProxyClass(Class<?> cl)  
如果cl是一个代理类则返回true。  
## CGLIB动态代理机制
### 介绍
JDK动态代理有一个最的问题就是**只能代理实现了接口的方法**
cglib动态代理中重要的是
**MethodInterceptor接口**和**Enhacer类**
其中MethodInterceptor接口中的**intercept**方法又是重中之重。
```Java
public interface MethodInterceptor
extends Callback{
    // 拦截被代理类中的方法
    public Object intercept(Object obj, java.lang.reflect.Method method, Object[] args, MethodProxy proxy) throws Throwable;
}
```
参数:
obj:要被代理的对象
method:被代理方法
args:方法参数
proxy:代理类
### 使用步骤
1. 导入依赖  
2. 定义一个类
3. **自定义MethodInterceptor**并重写intercept方法,intercept方法用于拦截被代理方法，与JDK动态代中的invoke方法类似
4. **通过Enhancer类的create()创建代理类**
### JavaGuide示例
1.定义发送短信的类
```java
public class MessageService{
  public Strng send(String message){
    //dosomething
    return message;
  }
}
```
2.定义MethodInterceptore
```java
public class DebugMethodInterceptor implements MethodInterceptor{
  public Object interceptor(Object o,Method m,Object[]  args,MethodProxy proxy){
    //前增强
    Object object=proxy.invokeSuper(o,args);
    //后增强
    return object;
  }
}
```
3.获取代理类
```java
public class CglibProxyFactory{
  public static Object getProxy(Class<?> clazz){
    
      Enhancer enhancer=new Enhancer();

      enhancer.setClassLoader(clazz.getClassLoader());

      enhancer.setSuperClass(clazz);

      enhancer.setInteceptor(new DebugInterceptor());

      return enhancer.create();
      
  }
}
```
4.使用
```java
MessageService service=(MessageService)new CglibProxyFactory().getProxy(MessageService.class);
service.send("hello");
```
### JDK动态代理和CGLIB动态代理的区别
1. JDK只能代理接口或者接口的实现类,cglib可以代理普通类