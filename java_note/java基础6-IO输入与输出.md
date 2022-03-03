# 输入/输出流

可以从**其中**(输入/输出流)读入一个字节序列的对象称为输入流,而可以向其中写入一个字节序列的称为输出流。这些字节序列的来源和目的地可以是文件、网络连接甚至是内存块。**抽象类InputStream和OutputStream**流构成了输入/输出的基础。

因为面向字节的流不好处理以Unicode(每个字符使用了多个字节来表示)为单位的存储形式，所以Java体系中专门有来处理Unicode字符的类层次结构,即**从Reader和Writer抽象类继承而来**。这些类的读入和写出操作都是基于**两字节的Char值**(即Unicode码元)。

## 读写字节

InputStream类中有一个抽象方法

```java
abstract int read()
```

这个方法将读入一个字节,并返回读入的字节,遇到**输入源**结尾的话直接返回-1。但是具体的输入流类在设计时,需要覆盖掉这个方法。例如,在FileInputStream中这个方法将从文件中读取一个字节,而System.in中这个方法是从"标准输入"(控制台或者重定向的文件)中读取一个字节。

InputStream中还有其他的若干个非抽象方法,他们都调用了read方法,所以子类只需要覆盖掉read方法即可。

与之对象的是OutputStream中有一个write方法,是向**输出源**中写入一个字节。

read方法和write方法在执行时都将被**阻塞**,直至字节确实被读入或写出。也就是说,当这两个方法被阻塞(由于网络链接忙不能及时访问)时,当前线程被阻塞。**这使得方法等待指定的流变为可用的这段时间内**,其他线程有机会去执行有用的工作。

available方法可以去检查当前可读入的字节数量,下面的代码无论如何不会被阻塞。

```java
int readAvailable=in.available();
if(readAvailable>0){
    var data=new byte[readAvailable];
    in.read(data);
}
```

完成输入/输出之后,应当关闭流(通过close方法),避免占用有限的系统资源。同时输出流在关闭时会刷新缓冲区:**所有临时被置于缓冲区以便后面用更大的包来传递的字节会在关闭输出流时被送出**。特别是**不关闭文件的话,那么写出字节的最后一个包可能永远也得不到输出**。当然还可以用flush方法来人为地冲刷这些输出。

原生的read和write方法很少用,因为程序员对于数字、字符串和对象更加感兴趣(用的多)。

## 常用API

### java.io.InputStream

#### abstract int read()

上面解释过

#### int read(byte[] b)

读入一个字节数组,并返回实际读入的字节数,或者在碰到输入流的结尾时返回-1。

int read(byte[] b,int off,int len)

#### int readNBytes(byte[] b,int off,int len)

如果未阻塞(read)的话,则读入由len指定数量的字节,或者阻塞至所有的值都被读入。读入的值将于b中off开始的位置,返回实际读入的字节数,结尾返回-1。

#### byte[] readAllBytes()

产生一个数组,包含可以从当前流中读入的所有字节。

#### long transferTo(OutputStream out)

将当前输入流中的所有字节传送到给定的输出流,返回传递的字节数。这两个流都不应处于关闭状态。

#### long skip(long n)

在输入流中跳过n个字节,返回实际跳过的字节数(遇到文件末尾可能小于n)

#### int avialable()

返回不阻塞情况下可获取的字节数。

#### void close()

关闭流

#### void mark(int readlimit)

在输入流的**当前位置**打一个标记(并非所有的流都支持打标记)。如果从输入流中已经读入的字节多于readlimit个,则允许忽略这个标记。

#### void reset()

返回到最后一个标记,随后重新调用read读入这些字节。如果没有标记则不重置。

#### boolean markSupported()

如果这个流支持标记,则返回true。

### java.io.OutputStream

#### abstract void write(int n)

写出一个字节的数据

void write(byte[] b)

#### void write(byte[] b,int off,int len)

写出所有字节或者某个范围的字节到数组b中

#### void close()

冲刷并关闭输出流

 #### void flush()

冲刷输出流,也就是将所有缓冲的数据发送到目的地

## 完整的流家族

C语言中只有单一类型File*包打天下,Java拥有一个家族。

按照处理字节和字符分为两个单独的层次结构。一方面,InputStream和OutputStream类可以读写单个字节或者数组,这些类构成了字节的层次结构的基础。要想读写字符串和数字,就需要功能更加强大的子类。例如,DataInputStream和DataOutputStream可以以**二进制格式**读写所有的**基本Java类型**。最后,还包含了多个有用的输入/输出流,例如,ZipInputStream和ZipOutputStream可以以我们常见的**ZIP压缩格式**读写文件。

另一方面,对于Unicode文本,可以使用抽象类Reader和Writer的子类。Reader和Writer类的基本方法与InputStream和OutputStream中的方法类似。

```java
abstract int read()
abstract void write(int c)
```

read方法将返回一个Unicode码元(一个在0-65535之间的整数),或者碰到文件结尾时返回-1。write方法被调用时将传递一个Unicode码元。

还有4个附加的接口:Closeable、Flushable、Readable和Appendable接口,前两个接口分别拥有

```java
void close() throws IOException
```

和

````java
void flush()
````

![](https://gitee.com/aryangzhu/picture/raw/master/java/Closeable%E6%8E%A5%E5%8F%A3.jpg)

