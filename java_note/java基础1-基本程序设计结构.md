写在前面，这个系列参考《java核心技术卷》，我之前学习的知识没有系统化，导致很多细节只知其然，而且很多技术对我来说难以上手，究其原因就是基础不牢(为什么之前不做笔记呢，我就是一铁憨憨)，所以这个也相当于补票。大多数内容其实就是书上的知识，不喜勿喷。

### 一个简单的Java应用程序

### 命令行运行

1.打开终端

2.进入corejava/v1ch02/Welcome目录(也就是当前.java文件的根目录)

3.shell脚本命令

```shell
javac Welcome.java
java Welcome
```

javac程序是一个Java编译器,它将文件Welcome.java编译成Welcome.class。java程序启动Java虚拟机，虚拟机执行编译器编译到类文件中的字节码。

## 注释

## 数据类型

### 八个基本类型

#### 布尔类型

boolean/1

#### 整型

byte/8

short/16

int/32

long/64

int最常用,如果想要表示较大的数字时应当使用long。byte和short类型主要应用于特定的场合,例如,底层的文件处理或者存储空间很宝贵时的大数组。

在Java中,整形的范围与运行Java代码的机器无关。从而在各个平台之间能够完美移植。

#### char类型和Unicode

char/16

##### Unicode

想要弄清楚char类型,就必须了解Unicode编码机制。Unicode打破了传统字符编码机制的限制。早在Unicode出现之前,就已经有许多中不同的标准:美国的ASCII、中国的GB18030等。这时出现了两个问题:

1.对于任意给定的代码值,不同的编码方案对应不同的字母;

2.采用大字符集的语言其编码长度有可能不同。例如,有些常用的字符采用单字节编码,而另一些需要两个或者多个字节。

设计Unicode就是为了解决这些问题,刚开始进行统一工作时,人们认为两个字节的代码宽度足以应付所有字符。Java中设计时采用的16位字符集,那时其他大部分语言只有8位。

但是后来由于加入了大量的中文、日文和韩语中的表意文字。Unicode字符超过了65535的限制,16位的char类型已经不能满足所有Unicode字符的需要了。

java5中引入了**码点**。码点(code point)是指与一个编码表中的**某个字符对应的代码值**。在Unicode标准中,码点采用十六进制书写,并加上前缀U+,例如U+0041就是拉丁字母A的码点。Unicode的码点共分为17个**代码平面**,第一个称为**基本多语言平面**,从U+0000到U+FFFF的“经典”Unicode代码;其余的从U+1000到U+10FFFF,包括辅助字符。

UTF-16编码采用不同长度的编码表示所有的Unicode码点。在基本多语言平面中,每个字符使用16位表示,通常称为**code unit(代码单元)**,而辅助字符编码为一对连续的代码单元。采用这种编码对表示的各个值落入基本多语言平面中未用的2048个值范围内,通常称之为**替代区域(surrogate area)**(如果一个代码单元超过了基本多语言的范围,那么就说明它是辅助字符的第一个代码单元),很容易区分一个代码单元是一个字符的编码还是辅助字符的第一部分或第二部分。

书上的建议是不要在程序中使用char类型,我持怀疑态度。

#### 浮点类型

float/32

double/64

#### 缓存池

new Integer(123)新建一个对象。
Integer.valueOf(123)会使用**缓存池**中的对象,**多次调用会取得同一个对象的引用**。Java8中缓存池的大小为-128~127。编译器会在**缓冲池范围内的基本类型**自动装箱调用valueOf()方法，因此多个Integer实例使用自动装箱来创建并且值相同，那么就会引用相同的对象。

## 变量与常量



## 运算符



## 字符串

### 子串

### 拼接

### 不可变字符串

String被声明为final，意味着不能够再被继承。同时内部声明为final，数组初始化之后就就不能引用其他数组(基本数据类型被final修饰之后只能进行一次赋值操作)。

```
String string ="123";
string=new String("234") ;
```
对于这里我们需要清楚的是String的地址@533，其中char [] value的地址为@535，执行完第二行代码之后value的地址为@537。

**不可变的好处**
1.作为哈希值的存储容器非常便利(哈希值不变，只进行一次计算便可得到结果)
2.String Pool的需要
3.安全性得到保证，网络传输过程不会被修改
4.不可变性天生具备线程安全的特点

### 检测字符串是否相等

### 空串与Null串

### 码点与代码单元

### String API

### 阅读文档

### 构建字符串

## 输入与输出

### 读取输入

想要通过控制台输入,**首先需要构造与"标准输入流"System.in相关联的Scanner对象**

````java
Scanner in =new Scanner(System.in);
````

例如，读取一个整数

```java
System.out.println("How old are you?");
int age=in.nextInt();
```
#### 常用API

#### java.util.Scanner

##### Scanner(InputStream in) 

用给定的输入流创建一个对象

##### int nextInt(); 

读取输入一个整数

##### String nextLine(); 

读取输入的一行内容

##### String next();

读取下一个单词(以空格为分割符)

##### double nextDouble() 

整数或浮点数的字符序列

##### boolean hasNext() 

检验输入中是否还有其他单词

##### boolean hasNextDouble() 

检测是否还有下一个整数或者浮点数的字符序列

### 格式化与输出

可以使用System.out.print(x),这条命令**将以x的类型所允许的最大非0数位个数打印输出x**。

```java
System.out.println("hello word");
System.out.printf("%d",12);
```

Java5中沿用了C语言函数库中的printf方法。例如,调用

System.ou.printf("%8.2f",x);

会以一个**字段宽度**打印x:包括8个字符,另外精度为小数点后2个字符。

每一个以%字符开始的格式说明符都用相应的参数替换。

用于printf的转换符

d

x

o

f

e

g

a

s

c 

h

tx

b

%

n

另外,还可以指定控制格式化输出外观的各种标志。例如,逗号标志可以增加分组分隔符。例如,

````java
System.out.printf("%,.2f",10000.0/3.0);
````

用于printf的标志

+

空格

0

-

(

,

#(对于f格式)

#(对于x或o格式)

$

<

可以使用静态的String.format方法创建一个格式化的字符串,而不打印输出:

```java
String message=String.format("Hello,%s. Next year,you‘ll be %d",name,age);
```

我们来看一下printf中格式说明的语法图:

![](https://gitee.com/aryangzhu/picture/raw/master/java/%E6%A0%BC%E5%BC%8F%E8%AF%B4%E6%98%8E%E7%AC%A6%E8%AF%AD%E6%B3%95.jpg)

argument index-参数索引(如果有的话后面就得加$)

flag-标志

width-宽度

precession-精确度

conversion character-转换符(如果没有和.组成的话那么就和t组成另一中形式)

### 文件输入与输出

想要读取一个文件,需要构造一个Scanner对象,如下所示:

```java
Scanner in=new Scanner(Path.of("myfile.txt"),StandardCharsets.UTF_8);
```

想要写入文件,就需要构造一个PrintWriter对象。在构造器(constructor)中,需要提供文件名和字符编码:

```java
PrintWriter out=new PrintWriter("myfile.txt",StandardCharsets.UTF_8);
```

## 控制流程

### 块作用域

### 条件语句

### 循环

### 确定循环

### 多重选择:switch语句

### 中断流程控制语句

## 大数



## 数组

### 声明数组

### 访问数组元素

### for each循环

### 数组拷贝

### 命令行参数

### 数组排序

### 多维数组

### 不规则数组

