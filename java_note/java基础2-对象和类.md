**面向对象程序设计概述OOP(object-oriented programming,OOP)**

传统程序设计：算法+数据结构
面向对象：将数据放在第一位，然后再考虑操作数据的算法。

## 面向对象程序设计

### 类

可以理解为一个模板
### 对象
要想使用对象，首先必须构造对象，并指定其初始状态，然后对对象应用方法。
在Java程序语言中，要使用构造器(constructor,或称构造函数)构造新实例。**构造器是一种特殊的方法，用来构造并初始化对象**。

```java
Date birthday=new Date();
```

birthday就是对象变量，而new Date()则是一个对象实例

```java
Date　deadline;//deadline　doesn't refer to any object;
```

定义了一个对象变量deadline,它可以引用Date类型的对象。
要认识到最重要的一点：对象变量并没有实际包含一个对象，**它只是引用一个对象。**
Date deadline=new Date();
可以把Java中的对象变量看做类似于Ｃ++的指针。例如，
Date birthday; //Java
实际上，等同于
Date * birthday; //C++
Date*指针只有使用new调用才会初始化。就这一点而言，Ｃ++和Java的语法几乎是一样的。
**Java类库中的localDate类**
不要使用构造器来构造LocalDate类的对象，实际上，应当使用静态工厂方法(factory method)，他会代表你调用构造器。

```java
localDate newYearsEve=LocalDate.of(1999,12,31);
int year=newYearEve.getYear(); //1999
int month=newYearEve.getMonthValue(); //12
int day=newYearEve.getDayOfMonth(); //31
```
#### LocalDate常用API

static LocalDate __now()__ 构造一个表示当前日期的对象
static LocalDate **of(int year,int month,int day)** 构造一个表示给定日期的对象
int **getYear()** 
int **getMonthValue()**
int **getDayOfMonth()** 得到当前日期的年、月和日
DayofWeek **getDayOfWeek()** 得到当前日期是星期几，作为DayOfWeek类的一个实例返回。调用**getValue**来得到１-7之间的一个数，表示这是星期几，１表示星期一，７表示星期日
LocalDate **plusDays(int n)** 
LocalDate **minusDays(int n)** 生成当前日期之后或之前n天的日期
#### 更改器方法和访问器方法
```java
LocalDate aThousandDaysLater=new YearEve.plusDays(1000);
```

plusDays方法会生成一个新的LocalDate对象，然后把这个新对象赋给aThousandDaysLater变量，原来的对象不做任何改动。
在字符串上调用toUpperCase时，这个字符串仍然保持不变，会返回一个大写的字符串。

```java
GregorianCalender someday=new GregorianCalendar(1999,11,31);
//odd feature of that class:month numbers go from 0 to 11
```

与LocalDate.plusDays方法不同，GregorianCalendar.add方法是一个__更改器方法(mutator method)__。调用这个方法之后，他不再是新年前夜
相反，只访问对象而不修改对象的方法有时称为__访问器方法__

### 识别类

Java语言中，没有所谓的”顶部“，从代码角度来说，我们不用先去写main()方法，而是从类开始，再为各个类添加方法。

### 类之间的关系
依赖
聚合
继承

## 使用预定义类
Java语言中已经定义好的类，可以直接使用，方便我们快速开发。
## 用户自定义类
**Employee类**
**多个源文件的使用**
**剖析Employee类**
**从构造器开始**

```java
public Empoyee(String n,double s,int year,int month,int day){
	name=n;
    salary=s;
    hireDay=LocalDate.of(year,month,year);
}
```
构造器与类同名
每个类可以有一个以上的构造器。
构造器可以有０个、１个或者多个参数。
构造器没有返回值。
**构造器总是伴随着new操作符一起使用**。
Java构造器的工作方式与C++一样。但是，要记住**所有的Java对象都是在堆中构造的，构造器总是结合new操作符一起使用**。C++程序员最易犯的错误就是忘记new操作符:
Employee number007("James Bond",100000,1,1); //C++,not　Java
这条语句在Ｃ++中能够正常运行，但在Java中却不行。
警告：不要在构造器中定义与实例字段同名的局部变量。例如，下面的构造器将不会设置salary。

```java
public Employee(String n,double s){
	String name=n; //ERROR
    double salary=s; //ERROR
}
```

Java10中，如果可以从变量的初始值推到出他们的类型，那么可以用var关键字声明局部变量，而无须指定类型。例如，可以不这样声明：
Employee harry=new Employee("Harry Hacker",50000,1989,10,1);
var harry =new Employee("Harry hacker",50000,1989,10,1);
**用var声明局部变量**
var harry=new Employee("Harry Hacker",5000,1989,10,1);
**使用null引用**
"宽容型"

```java
public Employee(String n,double s,int year,int month,int day){
	name=Objects.requiredNonNullElse("unkown");
}
```
也就是说，在构造方法中会对name进行检查，会进行提示
"严格型" 干脆拒绝null参数

```java
public Employee(String n,double s,int year,int month,int day){
	Objects.requireNonNull(n,"The name cannot be null");
    name=n;
}
```
**隐式参数和显示参数**

```java
public void raiseMoney(double byParent){
	double raise=salary*byParent/100;
    salary+=raise;
}
```
__number007__.raiseMoney(5);
raiseMoney方法有两个参数。第一个参数称为**隐式(implicit)参数，是出现在方法名之前的Employee类型的对象**实例。第二个参数是位于方法名后面括号中的数值，这是一个显式(explicit)参数。(有人把隐式参数称为方法调用的目标或者接收者。)
上面的方法可以改写成如下方式

```java
public void raiseMoney(double byParent){
	double raise=this.salary*byParent/100;
    this.salary++=raise;
}
```
**封装的优点**
想要获得或者设置实例字段的值，需要提供下面三项内容:

>一个私有的数据字段
>一个公共的字段访问器方法
>一个公共的字段更改器方法

**注意**

不要编写返回可变引用的的访问器方法。

```java
class Employee{
	private Date hireDay;
    
    public Date getHireDay(){
   		return hireDay; //Bad
   }
}
```
很明显，我们在Employee中**定义的私有属性hireDay,不希望被外部在位置情况下修改，但是我们getHireDay()方法中返回了hireDay,意味着其他变量可以保存这个引用**，从而导致不安全。
经验来说，如果需要返回一个可变数据字段的副本，就应该使用clone。

```java
class Employee{
...
	public Date getHireDay(){
    	return (Date)hireDay.clone(); //Ok
    }
}
```
### 基于类的权限访问
1.对于public修饰符，它具有最大的访问权限，**可以访问任何一个在CLASSPATH下的类，接口，异常等**。它往往对外也就是对象或者接口的形式。
2.对于protected修饰符，它主要的作用就是用来保护子类的。**子类可以访问,它相当于传递给子类一种继承的东西。**
3.对于default来说，有的时候也称为friendly，它是针对本包访问而设计的，**任何处于本包下的类，接口和异常等**，都可以互相访问，即使是父类没有用protected修饰的成员也可以。
4.对于private来说，**它的访问权限仅限于类的内部，是一种封装的体现**，例如，大多数的成员变量都是修饰为private,他们不希望被其他任何外部的类访问。

### final实例字段
可以将**实例字段定义为final,这样的字段必须在构造对象时初始化**。也就是说，必须确保每一个构造器执行之后，这个字段的值已经设置，并且以后不能再修改这个字段。

```java
class Employee{
	private final String name;
    ...
}
```
例如，可以将Employee类中的name字段声明为final，因为在对象构造之后，这个值不会再改变，即没有setName方法。
private final StringBuilder evaluations;
它在Employee类的构造初始化为
evaluations=new StringBuilder();
**final关键字只是表示存储在evaluations变量中的对象引用不会再指向另一个不同的StringBuilder对象**。不过这个对象可以更改:

```java
public void giveGoldStar(){
	evaluations.append(LocalDate.now()+":Gold star!\n");
}
```



## 静态字段和静态方法
### 静态字段
如果将一个字段定义为static,每个类只有一个这样的字段。什么意思呢，就是说，**对于非静态的实例字段，每个对象在堆中都有自己的一个副本**。但是对于静态字段来说，这个类的所有实例将共享一个nextId字段。即使没有Employee对象，静态字段nextId也存在。它属于类而不属于任何单个的对象。

```java
class Employee{
	private static int nextId=1;
    private int id;
    ....
}
```
### 静态常量

使用的比较多，例如System.out

```java
public class System{
	...
    public static final PrintStream out=....;
    ...
}
```
上面使用了final关键字，不允许对out指向新的引用

### 静态方法

静态方法是不在对象上执行的方法。例如,Math类的pow的方法既是一个

```java
public static int getNextId(){
	return nextId; //return static field 返回的是静态字段
}
```
可以提供类名来调用这个方法
int n =Employee.getNextId();
下面两种情况可以使用静态方法
1.方法不需要访问对象状态，因为它需要的所有参数都通过显式参数提供(例如：Math.pow())
2.方法只需要访问类的静态字段(例如，Employee.getNextId())

### 工厂方法
静态方法还有另一种常见的用途，类似LocalDate和NumberFormat的类使用静态工厂方法(factory method)来构造对象

```java
NumberFormat currencyFormat=NumberFormat.getCurrencyInstance();
NumberFormat percentFormat=NumberFormat.getPercentInstance();
double x=0.1;
System.out.println(currencyFormat.format(x)); //prints　$0.10
System.out.println(percentFormat.format(x));  //prints 10%
```
getCurrencyInstance()就是一个静态方法，生成了一个新的对象
```java
public final static NumberFormat getCurrencyInstance(){
	return getInstance(Locale.getDefault(Locale.Category.FORMAT),CURRENCYSTYLE);
}
```
上面是getInstance的源码，可以看到定义的是final static 
为什么不使用构造器？？？

​	无法命名构造器，**这里我们希望返回两个实例，一个是货币实例，一个是百分比实例，所以我们需要不同的对象定义**，显然和构造器只能和类同步是相违背的
使用构造器时，无法改变所构造的对象的实例，而工厂方法实际上将返回DecimalForamt类的对象，这是NumberFormat的一个子类

### main方法

这里有两个小技巧

```java
class Employee{
	public static void main(String[] args){
    ...
    }
}
```
1.如果只想对Employee进行测试
	java Employee
2.如果Empoyee只是程序的一部分，那么
	java Application

## 方法参数
Java中的参数传递都是按值调用，也就是说，方法得到的是所有参数值的一个副本。

```java
public static void tripleValue(double x){
	x=3*x;
}
```
执行过程

1.x初始化为percent值的一个副本

2.执行方法中的操作，但是percent仍然是10

3.这个方法结束后，参数变量x不再使用
![选区_153.png](https://i.loli.net/2021/04/14/dyTMNPl8F5ImuGc.png)gouzaoqi
**两种类型的方法参数**
1.基本数据类型(数字、布尔值)

2.对象引用

一个方法不能修改基本类型的参数，而对象引用作为参数就不同了，我们可以将对象的状态进行修改

```java
public static void tripleSalary(Employee x){ //works
	x.raiseSalary(200);
}
```
当调用

```java
harry=new Employee(...);
tripleSalary(harry);
```



![选区_154.png](https://i.loli.net/2021/04/14/PAK3rEXW4Yutw1R.png)
执行过程：
1.x初始化为harry的值的一个副本，这里就是一个对象引用,由图上可以看出指向同一个对象;

2.raiseSalary方法应用于这个对象引用。x和harry同时会引用的那个Employee对象的工资提高了200%;

3.方法结束后，参数变量x不再使用。当然，对象变量harry继续引用那个工资增至3倍的员工对象。

**交换对象方法，交换的只是对象的引用副本**
![选区_155.png](https://i.loli.net/2021/04/14/m4fDR9PJ1YBKEcp.png)

```java
var a=new Employee("Alice");
var b=new Employee("Bob");
swap(a,b);

public static void swap(Employee x,Employee y){ //doesn't work
	//x refers to Alice,y to Bob
	Employee temp=x;
    x=y;
    y=temp;
    //now x refers to Bob,y to Alice
}
```
**这个方法并没有改变存储在变量a和b中的对象引用**。

方法的参数被初始化为两个对象引用的副本，这个方法调用的是这两个副本，在方法执行完之后，x和y消失。

### 对象方法能做什么和不能做什么
1.方法不能修改参数的类型
2.方法可以修改对象的状态
3.方法不能让一个对象参数引用一个新的对象，在上面的案例中，swap方法交换的也只是对象的副本，不难理解。

## 对象构造
### 1.重载

重载解析：编译器挑选出调用哪个方法，它用各个方法首部中的参数类型与特定方法调用中所使用的值类型进行匹配来挑选出正确的方法，如果编译器找不到匹配的参数，就会产生编译时的错误，因为根本不存在匹配，或者没有一个比其他的更好。
java允许重载任何方法，而不只是构造器方法。因此，要完整地描述一个方法，需要指定方法名以及参数类型。这叫做方法的签名(signature)

### 2.默认字段初始化

如果没有在构造器中显示地为字赋初始值，那么就会被自动地赋为默认值：数值为0、布尔值为false、对象引用为null。
**注意：**
这是局部变量和字段的一个重要区别

### 3.无参数的构造器

如果类中至少提供了一个构造器，但是没有提供无参数的构造器，那么构造对象时如果不提供参数就是不合法的
当没有其他构造函数的时候，你才会得到一个默认的无参构造函数

### 4.显式字段初始化

通过重载类的构造方法，可以采用多种形式设置类的实例字段的初始状态

```java
class Employee{
	private String name="";
}
```
上面的name就进行了显式初始化，经验告诉我们，显示初始化总是好的
初始值不一定是常量值，下面的例子中，就是利用方法调用初始化一个字段

```java
class Employee{
	private static int nextId;
    private int id=assignId();
    ...
    private static int assignId(){
    	int r=nextId;
        nextId++;
        return r;
    }
}
```
### 5.参数名

### 6.调用另一个构造器

this指示一个方法的隐式参数，还有另一个含义
如果构造器的第一个语句形如this(...),这个构造器将调用同一个类的另一个构造器

```java
    private  String name;
    private double money;

    private  int nextId=0;

    public Employee(String s,double b){
        name=s;
        money=b;
    }

    public Employee(double s) {
        this("Employee",s);
        nextId++;
    }


    public static void main(String[] args){
        Employee employee=new Employee(6000);
    }
```
先进入到this调用另一个构造函数中去，最后返回初始化对象
上面的代码执行之后，首先会进行字段默认初始化，如下图
![选区_156.png](https://i.loli.net/2021/04/14/NYCUGRpur3IDjvV.png)

### 7.初始化块

初始化数据字段的方法(也就是说给字段赋值)

1.在构造器中设置值
2.显式地声明值
3.初始化块

```java
class Employee{
	private static int nextId;
    
    private int id;
    private String name;
    private double salary;
    
    
    //object initialization block  初始化块
    {
    	id=nextId;
        nextId++;
    }
    
   	public Employee(String n,double s){
    	name=n;
        salary=s;
    }
    
    public Employee(){
    	name="";
        salary=0;
    }
}
```
首先运行初始化块，然后才运行构造器的主体部分。

#### 调用构造器的具体处理步骤：

1.如果调用了另一个构造器，则会基于提供的参数执行第二个构造函数。
2.如果1不成立，

​	a.所有的数据字段初始化为其默认值;
​    b.按照在类中声明的顺序，执行**字段初始化方法**和**初始化块**
3.执行构造函数主体代码

可以通过提供一个初始值，或者使用一个静态的初始化块来给静态字段赋值。

```java
private static int nextId=1;
```

如果静态字段需要复杂的初始化代码，那么可以使用静态的初始化块。

将代码放在一个块中，并标记为static。

```java
//static initialzation block
static{
    Random generator=new Random();
    nextId=generator.nextInt(1000);
}
```

在类第一次加载的时候，将会进行静态字段的初始化。与实例字段一样，除非将静态字段显式地赋值，否则会默认赋值0，false或者null。**所有的静态字段初始方法以及静态初始化块都将依照类声明中出现的顺序执行**。

##### 常用API
**java.util.Random**
	Random() 构造一个新的随机数
	int nextInt(int n) 返回一个0～n-1之间的随机数

## 包

### 包名

使用包名是为了类的唯一性。为了保证包名的唯一性，要用一个因特网域名(唯一)的逆序形式来作为包名。

#### 完全限定名

先考虑一个域名horstman.com,再追加工程名corejava,再将Employee类放在这个包里，这个类的“完全限定名”就是com.horstman.corejava.Employee。

### 类的导入

在编译器的角度上java.lang和java.lang.jar是相互独立的(也就是说嵌套的包是独立的)。

一个类可以使用所属包中的所有类，以及其他包中的公共类。

### 静态导入

**可以允许导入静态方法和静态字段，而不只是类**
import static java.lang.System.*;
就可以使用System类的静态方法和静态字段，而不必加类名前缀
out.println("Goobye,world!!");
exit(0);

### 在包中增加类

**如果没有在源文件中放置package语句，这个源文件中类就属于无名包**。
将源文件放到完整包名匹配的子目录中，因为java中的类是强依赖包。例如,com.horstmann.corejava包中所有源文件应该也放置在子目录com/horstman/corejava中(.java文件)。编译器将类文件也放在相同的目录结构中。(.class文件)
java命令 src包下的类路径

### 类路径

类存储在文件系统的子目录中。**类的路径必须与包名匹配**。

为了使类能够被多个程序共享，需要做到下面几点：

1.把类文件放到一个目录中，例如/home/user/classdir。需要注意，这个目录是包树状结构的基目录。如果希望增加com.horstman.corejava.Employee类，那么Employee类文件就必须位于子目录/home/user/classdir/com/horstman/corejava中
2.将JAR文件放在一个目录中，例如：/home/user/archives。
3.设置类路径(class path)。类路径是所有包含类文件的路径的集合。
在unix环境中，类路径中的各项之间用冒号(:)分隔：
/home/user/classdir:.:/home/user/archives/archive.jar
而在windows环境中，则以分号(;)分隔
C:|classdir;,;C:\archives\archive.jar

### 设置类路径

```shell
java -classpath /home/user/classdir:.:/home/user/archives/archive.jar 
```

总结：在我们设置了类路径之后，java编译器总是在当前的目录中查找对象，但是jvm虚拟机仅在类路劲中包含"."目录的时候才查看当前目录。如果没有设置类路径的话，那么默认会在当前目录下进行查找，但是如果设置了类路径但是类路径中没有"."的话那么类可以编译但无法运行。
虚拟机搜寻com.horstmann.corejava.Employee类文件。它首先会查找Java API类。那里找不到相应的类文件，所以查看类路径。

```shell
/home/user/classdir/com/horstmann/corejava/Employee.class(对应上面第一步)
com/horstmann/corejava/Employee.class(从当前目录开始)
com/horstmann/corejava/Employee.class(/home/user/archives/archive.jar中)
```

## JAR文件
**java归档文件**，可以含有类文件和子目录，也可以包含诸如图像和声音等其他类型的文件。此外，JAR文件是压缩的，它使用了我们熟悉的zip压缩格式。

![选区_157.png](https://i.loli.net/2021/04/15/yAS6qtJMFbRlDNL.png)
**清单文件**
每个jar包下有一个清单文件，清单文件被命名为MANIFEST.MF，描述归档文件的特殊性，如上图，在META-INF下

### jar命令
#### 创建jar文件

jar cvf jarFileName file1 file2...

```shell
jar cvf CalculatorClasses.jar *.class icon.gif
```

#### 可执行jar文件

可以使用jar命令中的e选项指定程序的入口点，即**通常需要在调用Java程序启动器时启动器是定的类**:

```shell
jar cvfe MyProgram.jar com.mycompany.mypkg.MainAppClass files to add
```

或者，可以在清单文件中指定程序的主类，包括以下形式的语句:

Main-Class:com.mycompany.mypkg.MainAppClass

不论使用哪一种方法，用户可以简单地通过下面的命令来启动程序:

```shell
java -jar MyProgram.jar
```



### 多版本JAR文件

## 文档注释

使用” // “进行单行注释

使用“#“进行多行注释

## 类设计技巧
### 1.一定要数据进行私有化
### 2.一定要对数据进行初始化
切记一定要初始化局部变量
### 3.不要在类中使用过多的基本类型
可以用其他的类来替换多个基本类型
### 4.不是所有的字段都需要单独的字段访问器和字段更改器
例如类中的日期
### 5.分解有过多职责的类
### 6.类名和方法名要体现它的职责
### 7.优先使用不可变的类
LocalDate以及Java.time包中其他的类是不可变的