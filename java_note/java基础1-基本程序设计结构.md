 写在前面，这个系列参考《java核心技术卷》，我之前学习的知识没有系统化，导致很多细节只知其然，而且很多技术对我来说难以上手，究其原因就是基础不牢(为什么之前不做笔记呢，我就是一铁憨憨)，所以这个也相当于补票。大多数内容其实就是书上的知识，不喜勿喷。
## 一个简单的Java应用程序
## 命令行运行
1.打开终端  
2.进入corejava/v1ch02/Welcome目录(也就是当前.java文件的根目录)  
3.shell脚本命令  
```shell
javac Welcome.java
java Welcome
```
javac程序是一个Java编译器,它将文件Welcome.java编译成Welcome.class。java程序启动Java虚拟机，虚拟机执行编译器编译到类文件中的字节码。  

## 注释
就是用来说明我们这部分代码用来做什么，在复杂的系统中尤为重要
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
### 算术运算符
### 数学函数与常量
### 数值类型之间的转换
### 强制类型转换
### 结合赋值和运算符
### 自增与自减运算符
### 关系和boolean运算符
#### & | ^ ~
处理整型数据时,可以完成对组成整数的位的操作,这意味着可以使用掩码技术得到整数的各个位。
& and | or ^ xor ~ not都是按位模式处理。书上的例子是把整数中不想要的位给他掩掉:  
```java
int fourthBitFromRight=(n&0b1000)/0b1000;
```
这个是用来取得从右往左的第4位的1(很难想到会有这种场景),套路就是利用&并使用适当的2的幂。  
#### \>\>和\<<
\>>和\<<可以将**位模式(以某种选定的进制)**左移或右移(从最右边的位开始算起),需要建立位模式来**完成位掩码**时,这两个运算符很方便。  
```java
int fourthBitFromRight=(n&(1<<3))>>3;
``` 
#####　\>\>\>
\>>>运算符会用0填充高位,这与\>>不同,他会用符号位来填充高位(0001和1001)。  
### 括号和运算符级别
## 字符串
从改变上讲,Java字符串就是由**Unicode字符序列**构成。例如,字符串"Java\u2122"由5个Unicode字符J、a、v、a和tm构成。Java没有内置的**字符串类型**,而是在标准Java类库中提供了一个预定义类,很自然地叫做String。每个用双引号括起来的字符串都是String类的一个实例:  
```java
String e="";
String greeting="Hello";
```
### 子串
String类的subString方法可以从一个较大的字符串中提取出一个子串。例如:  

```java
String greeting="Hello";
String s=greeting.subString(0,3);
```
第二个参数是不想复制的第一个位置。
### 拼接
```java
String expletive="Expltive";
String PG13="deleted";
String message="expletive"+PG13;
```
### 不可变字符串
为什么叫做不可变字符串?
因为不能修改字符串中的单个字符,例如不能将greeting的后两位字符修改为'p'和'!'。
也就是说这个字符串永远包含这几个字符,所以你不能做修改,那么要怎么做修改呢？
```java
greeting=greetin.subString(0,3)+"p!";
```
很明显,子串是一个新串子再与"p!"进行拼接。
同时我们可以修改Sting类型变量的引用地址,将其指向新的地址。
String被声明为**final**，意味着不能够再被继承。同时内部声明为final，数组初始化之后就就不能引用其他数组(基本数据类型被final修饰之后只能进行一次赋值操作)。

```java
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
使用equals检测两个字符串是否相等。对于表达式:
```java
s.equals(t);
```
来看下面这个例子

```java
String greeting="Hello";
if(greeting=="Hello"){
    ...
    //probably true
}
if(greeting.subString(0,3)=="Hel"){
    //probably false；
}
```

如果虚拟机中始终将相同的字符串共享,就可以使用==运算符检测是否相等。但实际上只有**字符串字面量**是共享的,而**+或subString()**等操作得到的字符串并不共享(也就是说会使用新的地址)。所以,千万不要使用==运算符测试字符串的相等性。

### 空串与Null串

String类型的变量可以是"",也可以是null,最常用的就是检测一个字符串时候为空

```java
if(str!=null && str.length()!=0)...
```

### 码点与代码单元

码点就是编码表中对应的代码值,而码元就是代码单元也就是用UTF-16的编码表示一个Unicode的代码单元。

返回代码单元数量。

```java
String greeting="Hello";
int n=greeting.length();
```

我们知道Unicode编码中辅助字符是由两个代码单元组成的,所以当返回特殊字符时数量会多一个。

返回实际长度即码点数量

```java
int cpCount=greeting.codePoint(0,greeting.length());
```

返回码元

```java
char first=greeting.charAt(0);
```

返回码点

```java
int index=greeting.offsetByCodePoints(0,i);
int cp=greeting.codePointAt(index);
```

### String API

#### java.lang.String

##### char charAt(int index)

返回给定位置的代码单元。除非对底层代码单元感兴趣,否则不需要调用这个方法。

##### int codePointAt(int index)

返回从给定位置开始的码点。

##### int offset(int startIndex,int cpCount)

返回从startIndex码点开始,cpCount个码点后的码点索引。

##### boolean empty()

##### boolean equals(String other)

##### int length()

##### int codePointCount(int startIndex,int endIndex)

返回startIndex和endIndex-1之间的码点个数

##### String replace(CharSequence oldString,CharSequence newString)

返回一个新字符串。这个字符串用newString代替原始字符串中所有的oldString。

String subString(int beginIndex)

##### String subString(int beginIndex,int endIndex)

返回一个新字符串。

String toLowerCase()

##### String toUpperCase()

返回一个新字符串,将大小写进行转换。

String trim()

删除字符串开头和结尾的空格。

##### String join(CharSequence delimiter,CharSequence ...elements)

返回一个新的字符串,用给定的定界符连接所有元素。

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

(对于f格式)

(对于x或o格式)

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

