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

![](https://gitee.com/aryangzhu/picture/raw/master/java/InputStream&OutputStream.jpg)

另一方面,对于Unicode文本,可以使用抽象类Reader和Writer的子类。Reader和Writer类的基本方法与InputStream和OutputStream中的方法类似。

![](https://gitee.com/aryangzhu/picture/raw/master/java/Reader&Writer.jpg)

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

InputStream、OutputStream、Reader和Writer都实现了Closeable接口。

注:java.io.Closeable接口实现了java.lang.AutoCloseable接口。因此,对于任何Closeable接口进行操作时,都可以使用try-with-resource语句(我们之前在捕获异常中有有学习过)。

OutputStream和Writer还实现了Flushable接口。

Readable接口只有一个方法

```java
int read(CharBuffer cb)
```

CharBuffer类拥有按顺序和随机读写访问的能力,它表示**内存中的缓冲区或者一个内存映像的文件**。

Appendable接口拥有添加单个字符和字符序列的方法:

```java
Appendable append(char c)
Appehdable append(CharSequence s)
```

CharSequence接口描述了一个**char值序列的基本属性**,String、CharBuffer、StringBuilder和StringBuffer都实现了它。

### 常用API

#### java.io.Closeable

##### void close()

关闭这个Closeable,这个方法可能会抛出IOException

#### java.io.Flushable

##### void flush()

冲刷这个Flushable。

#### java.lang.Readable

##### int read(CharBuffer cb)

尝试着向cb读入其可持有数量的char值。返回可读入的char值的数量,或者从这个Readable中无法再获得更多的值时返回-1。

#### java.lang.Appendable

Appendable append(char c)

#####　Appendable append(CharSequence cs)

向这个Appendable中追加给定的码元或者给定序列的码元,返回this。

#### java.lang.CharSequnece

##### char charAt(int index)

返回给定索引处的码元。

##### int length()

返回在这个序列中的码元的数量。

##### CharSequence subSequnce(int startIndex,int Index)

返回由存储在startIndex到endIndex-1处的所有码元构成的CharSequnce。

##### String toString()

返回这个序列中所有码元构成的字符串。

## 组合输入/输出流过滤器

FileInputStream和FileOutputStream可以提供一个附着在磁盘文件上的流,你可以通过构造器来指定文件位置。

```java
FileInputStream in=new FileInputStream("meployee.dat");
```

### 两个注意的点：

1.**所有在java.io中的类都将相对路径解释为用户工作目录开始**,你可以调用**System.getProperty("user.dir")**来获得这个信息。

2.java中的"\\"为转义字符,所以在Windows风格的路径中应当使用"\\\\"。例如D:\\\\Windows\\\\win.ini。但是,更好的方法是使用常量字符串**java.io.File.separator**获得它。

同字节流一样,FileInputStream和FileOutputStream也只能处理字节。

Java中有非常灵活的机制来确保各个流能够组合在一起,FileInputStream和URL类的openStream方法返回的输入流无法读入数值类型的方法,那我们可以将他与DataInputStream组合起来使用。

例如,为了从文件中读入数字,首先需要创建一个FileInputStream,然后将其**传递给DataInputStream的构造器**:

```java
FileInputStream fin=new FileInputStream("employee.dat");
DataInputStream din=new DataInputStream(fin);
double x=din.readDouble();
```

FilterInputStream和FilterOutputStream类,这些文件的子类用于向处理字节的输入/输出流添加额外的功能。

默认情况下,输入流每执行一次read方法就要请求操作系统分发一个字节。那么,如果申请一个数据块并将其置于缓冲区肯定更加高效。如果我们想使用缓冲机制和用于文件数据输入方法,那么需要使用下面这种相当复杂的构造器序列:

```java
var din=new DataInputStream(
    new BufferedInputStream(
        new FileInputStreama("employee.dat")))
```

注意:我们将DataInputStream置于构造链的最外部，这是因为我们希望使用DataInputStream的方法,并且希望**它们能够使用带缓冲机制的read方法**。

当我们使用输入流链的时候,需要跟踪各个中介输入流(intermediate input 
stream)。例如,当读入输入时,你需要检查下一个将要读入的字符是否是你需要的字符。Java中提供了这样的类PushbackInputStream,我们对上面的代码加以改造:

```java
var bin=new DataInputStream(
    pbin=new PushbackInputStream(
    	new BufferedInputStream(
        	new FileInputStream("employee.dat"))));
```

```java
int b=pbin.read();
if(b!='<')pbin.unread();
```

如果不是自己期望的字符,那么我们可以将其推回至流中。

其他语言的输入/输出流类库中,缓冲机制和预览都是自动处理的。Java更加麻烦,但也更加灵活。

### 常用API

#### java.io.FileInputStream

FileInputStream(String name)

##### FileInputStream(File file)

由name字符串或file对象指定路径名的文件创建一个新的输入流。

#### java.io.FileOutputStream

FileOutpuStream(String name)

FileOutputStream(String name,boolean append)

FileOutputStream(File file)

##### FileOutputStream(File file,boolean append)

由name字符串或file对象指定路径名穿啊关键一个新的文件输出流。如果append为true,那么已存在的文件不会被删除而会直接添加导师文件末尾;如果为false,那么则会删除素有同名文件。

#### java.io.BufferedInputStream

##### BufferedInputStream(InputStream in)

创建一个带缓冲区的输入流。带缓冲区的输入流在从流中读取字符时,不会每次都访问设备。当缓冲区为空时,会向缓冲区中读入一个新的数据块。

#### java.io.BufferedOutputStream

##### BufferedOutputStream(OutputStream out)

创建一个带缓冲区的输出流。带缓冲区的输出流在收集要写出的字符时,也不会每次都访问设备。当缓冲区填满或这被冲刷时,数据就被写出。

#### java.io.PushbackInputStream

PushbackInputStream(InputStream in)

##### PushbackInputStream(InputStream in,int size)

构建一个可以预览一个字节或者具有指定尺寸的回退缓冲区的输入流。

##### void unread()

回推一个字节,它可以在下次调用read时再次被获取。

## 文本输入与输出

在保存数据时,可以选择二进制形式或者文本形式。例如,1234在二进制数表示为000004D2构成的序列(十六进制表示法),而文本格式存储为**字符串**"1234"。虽然二进制格式的I/O高速且高效,但是不适合人类阅读。

在存储文本字符串时,我们必须考虑**字符编码**。Java内部使用的UTF-16编码方式,许多其他程序希望按照其他方式编码。

**OutputStreamWriter类将使用选定的字符编码方式**,把Unicode码元的输出流转换为字节流。

**InputStreamReader类**将包含字节的输入流转换为可以**产生Unicode码元的读入器**。

```java
var in=new InputStreamReader(new FileInputStream("data.txt"),StandardCharsets.UTF-8);
```

## 如何写出文本输出

