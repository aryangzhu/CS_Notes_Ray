写在前面，这个系列参考《java核心技术卷》，我之前学习的知识没有系统化，导致很多细节只知其然，而且很多技术对我来说难以上手，究其原因就是基础不牢(为什么之前不做笔记呢，我就是一铁憨憨)，所以这个也相当于补票。大多数内容其实就是书上的知识，不喜勿喷。

### 一个简单的Java应用程序

### 命令行运行

1.进入终端

2.进入java的.java的上级目录

3.shell脚本命令

```java
javac Welcome.java
java Welcome
```

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

#### char类型

char/16

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

### 检测字符串是否相等

### 空串与Null串

### 码点与代码单元

### String API

### 阅读文档

### 构建字符串

## 输入与输出

### 读取输入

构造标准输入流对象Scanner
Scanner in =new Scanner(System.in);
例如，读取一个整数

```java
System.out.println("How old are you?");
int age=in.nextInt();
```
**不可变的好处**
1.作为哈希值的存储容器非常便利(哈希值不变，只进行一次计算便可得到结果)
2.String Pool的需要
3.安全性得到保证，网络传输过程不会被修改
4.不可变性天生具备线程安全的特点

#### 常用API

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

```java
System.out.println("hello word");
System.out.printf("%d",12);
```

### 文件输入与输出

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

