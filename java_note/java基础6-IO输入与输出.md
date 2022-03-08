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

完成输入/输出之后,**应当关闭流(通过close方法),避免占用有限的系统资源。同时输出流在关闭时会刷新缓冲区:**所有临时被置于缓冲区以便后面用更大的包来传递的字节会在关闭输出流时被送出**。特别是**不关闭文件的话,那么写出字节的最后一个包可能永远也得不到输出**。当然还可以用flush方法来人为地冲刷这些输出。

原生的read和write方法很少用,因为程序员对于数字、字符串和对象更加感兴趣(用的多)。

## 常用API

#### system.getProperty()

获得当前路径。

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

对于**文本输出**(以文本格式输出???),可以使用**PrintWriter**。这个类拥有以**文本格式打印字符串和数字的方法**,**需要用文件名和字符编码方式构建一个PrintStream对象**:

```java
var out=new PrintWriter("employee.txt",UTF_8);
```

我们来看如下代码:

```java
String name="Harry Hacker";
double salary=7500;
out.print(name);
out.print(' ');
out.println(salary);
```

它将

Harry Hacker 7500.0

输出到**写出器**out,之后这些字符将会被转换成字节并最终写入employee.txt中。

如果写出器设置为**自动冲刷模式**,那么只要println被调用,缓冲区中的所有字符都会发送到他们的目的地(打印写出器总是带缓冲区的)。                  	

### 常用API

#### java.io.PrintWriter

PrintWriter(Writer out)

##### PrintWriter(Writer writer)

创建一个向给定的写出器写出的新的PrintWriter。

PrintWriter(String fileName,String encoding)

##### Printer(File file,String encoding)

创建一个使用给定的编码方式向给定的文件写出的新的PrintWriter。

##### void print(Object obj)

通过打印从toString产生的字符串来打印一个对象。

##### void print(String s)

打印一个包含Unicode码元的字符串。

##### void println(String s)

打印一个字符串,带有终止符。如果处于自动冲刷模式,那么将冲刷这个流。

##### void print(char[] s)

打印在给定的字符串中的所有的Unicode码元。

##### void print(char c)

打印一个Unicode码元。

void print(int i)

void print(long l)

void print(float f)

void print(double b)

void print(boolean b)

以文本格式打印给定的值。

##### void printf(String format,Object...args)

按照格式字符串指定的方式打印给定的值。

##### boolean checkError()

如果产生格式化或输出错误,则返回true。一旦这个流碰到了错误,它就收到了污染,并且所有对checkError的调用都将返回true。

## 如何读入文本输入

我们应用最广泛的就是**Scanner**类。

如果想要将文件一行行读入,可以调用:

```java
List<String> lines=Files.readAllLines(path,charset);
```

如果文件太大,那么可以将行惰性处理为一个Stream\<String>对象:

```java
try(Stream<String> lines=Files.lines(path,charset)){
...
}
```

还可以使用**扫描器**来读入符号(token),即**由分隔符分隔的字符串**,默认的分隔符是空白字符,可将分隔符修改为任意的正则表达式。

```java
Scanner in=...;
in.useDelimiter("\\PL+");
```

调用next方法可以产生下一个符号:

```java
while(in.hasNext()){
	String word=in.next();
}
```

或者,读取包含所有文件的流:

```java
Stream<String> words=in.tokens();
```

在早期的Java版本中,处理文本输入的唯一方式就是通过BufferedReader类。它的readLine方法会产生一行文本,或者在无法获得更多的输入时返回null。

```java
InputStream in=...;
try(BufferedReader in=new BufferedReader(new InputStreamReader(inputStream,charset))){
    String line;
    while((line=in.readLine())!=null){
        dosomething;
    }
}
```

与Scanner不同的是,BufferedReader没有用于任何读入数字的方法。

## 以文本格式存储对象

由于要写出文本文件中,所以使用PrintWriter类。

split方法的参数是一个描述分隔符的正则表达式。

注:竖线在正则表达式中具有特殊的含义,因此需要用\字符来表示转义,而这个\又需要使用另一个\来转义,所有就有了"\\\\\|"表达式。

## 字符编码方式

输入和输出流都是用于字节序列的,但在许多情况下,**我们希望操作的是文本,即字符序列**,那么字符如何编码成字节就是问题。

Java针对**字符**采用的是Unicode标准。每个字符或"编码点"都具有一个21位的整数,也就是说将这些21位数字包装成字节的方法有多种。

最常见的编码方式是UTF-8,它会将每个Unicode编码点编码为1到4个字节的序列。

另一种常见的编码方式是UTF-16,它会将每个Unicode编码点编码为1个或2个16位值。Java**字符串**中使用的就是这种方式。

StandardCharsets类具有类型为Charset的静态变量,用于表示每中Java虚拟机都必须支持的字符编码方式,例如:

StandardCharsets.UTF_8;

# 读写二进制数据

## DataInput和DataOutput接口

DataOutput接口定义了用于以二进制格式读写数组、字符、boolean值和字符串的方法。

例如,writeInt总是将一个整数写出为4字节的二进制数量值,而不管它有多少位,writeDouble总是将一个double值写出为8字节的二进制数量值。优点是给定类型的每个值所使用的空间都是相同的,缺点是非人类阅读的。

注: 存储的方式分为最高位字节的方式存储和最低位字节方式存储。

DataInputStream类实现了DataInput接口,为了从文件中读入二进制数据,可以将DataInputStream与某个字节源组合,例如FileInputStream：

```java
DataInputStream in=new DataInputStream(new FileInputStream("employee.dat"));
```

而DataOutputStream实现了DataOutput接口。

### 常用API

#### java.io.DataInput

##### int readInt()

读取一个给定类型的值。

##### void readFully(byte[] b)

将字节读入数组b中,其间阻塞直至所有字节被读入。

##### void readFully(byte[] b,int off,int len)

len指定数量,放置在数组中从off开始的位置。

#### java.io.DataOutput

##### void writeInt(int i)

写出一个定类型的值。

void writeChars(String s)

写出字符串中的所有字符。

## 随机访问文件

**RandomAccessFile类可以在文件的任何位置查找或写入数据**。你可以打开一个随机访问文件,只用于读入或者同时用于读写,你可以通过使用字符串"r"(读)或"rw"(用于读写访问)作为构造器的第二个参数拉丝指定这个选项。

```java
RandomAccessFile in=new RandomAccessFile("employee.data","rw")
```

随机访问文件有一个表示下一个将被读入或这写出的字节所处位置的**文件指针**

,**seek方法可以用来将这个文件指针设置到文件中的任意字节位置**。seek的参数是一个long类型的整数,它的值位于0到文件按照字节来度量的长度之间。

getFilePointer方法将返回文件指针。

指定文件指针位置:

```java 
long n=3;
in.seek((n-1)*RECORD_SIZE);
Empolyee e=new Employee();
e.readData(in);
```

整数型和浮点型在二进制格式中都具有固定的尺寸,但是在处理字符串时就有些麻烦了,所有我们手写两个助手方法来读写具有固定尺寸的字符串。

下面这个方法写出从字符串开头开始的指定数量的码元(如果码元过少,则用0来补位)



```java
public static  void writeFixedString(String s,int size,DataOutput out){
    char ch=0;
    for(int i=0;i<size;i++){
        if(i<s.length()){
            ch=s.charAt(i);
        }
        out.writeChar(ch);
    }
}
```

readFixedString方法从输入流中读入字符,直至读入size个码元,或者直至遇到具有0值的字符值跳过剩余0值。

```java
public static String readFixedString(int size,DataInput in) throws IOException{
    StringBuilder b=new StringBuilder();
    int i=0;
    boolean done=false;
    while(!done&&i<size){
        ch=in.readChar();
        i++;
        if(ch==0) done=true;
        else b.append(ch);
    }
    in.sipBytes(2*(size-i));
    return b.toString();
}
```

 ## ZIP文档

每个ZIP文档中都有一个头,包含诸如每个文件名字和所使用的压缩方法等信息。你有可能需要浏览文档中的每一项,所以你可以使用getNextEntry方法来获取一个ZipEntry类型的对象。该方法会读入数据至这一项的末尾。然后调用closeEntry来读入下一项。在读入最后一项之前,不要关闭zin。下面是典型的**通读ZIP文件的序列代码**:

```java
ZipInputStream zin=new ZipInputStream(new FileInputStream(zipname));
ZipEntry entry;
while((entry=zin.getNextEntry())!=null){
    read the contents of zin;
    dosomething...;
    zin.closEntry();
}
zin.close();
```

下面是写出到ZIP文件的代码:

```java
ZipOutputStream zout=new ZipOutputStream(new FileInputStream("test.zip"));
for all files
{
    var ze=new ZipEntry(filename);//需要传递ZipEntry的构造器
    zout.putNextEntry(ze); //用这个方法来写出新文件，并将数据发送至ZIP输出流中。
    send data to zout;
    zout.closeEntry();
}
zout.close();
```

**JAR文件是一个特殊性的ZIP文件**。

### 常用API

#### java.io.ZipInputStream

#### java.io.ZipOutputStream

#### java.util.zip.ZipEntry

##### ZipEntry(String name)

用给定的名字构建一个ZIP项。

#### java.io.zip.ZipFile

# 对象输入/输出序列化

​	我们希望使用固定长度的格式保存记录,但是Java中的数据类型的长度又不一样。例如,一个Employee数组,有可能存储的是Manager类型的记录。

​	Java语言支持一种称之为**对象序列化**(object serialization)的非常通用的机制。可以将任何写出到输出流,并在之后将其读回。

​	我觉得Java编程思想中为什么使用序列化更为透彻:

​	当创建对象时，在程序运行期间可以获取，但是程序终止时，所有的对象都会被清除，我们是无法再获取的。当然，你可以通过将信息写入文件或者数据库来达到目的。但是为了更方便，Java 为我们提供了序列化机制，并且屏蔽了大部分细节。 ——Bruce Eckel

## 保存和加载序列化对象

为了保存对象数据,首先需要打开一个ObjectOutputStream对象:

```java
var out=new ObjectOutputStream(new FileOutputStream(""employee.dat));
```

现在,**为了保存对象可以直接使用ObjectOutputStream的writeObject方法**。

```
out.writeObject(harry);
```

为了将对象读回,创建一个ObjectInputStream对象

```java
var in=new OjbectInputStream(new FileInputStream("employee.dat"));
var harry=(Employee)in.readObject();
```

所有希望**在对象输出流中存储或从对象输入流中恢复**的类都应该实现Serializable接口。

Serializable接口中没有任何方法(联系之前的学习的Cloneable接口,但是为了使类可以克隆,必须要是覆盖Object类中的clone方法)

在幕后,是ObjectOutputStream在浏览对象的所有域(访问堆中的对象实例???),并存储它们的内容。

现在来假设另一种情况:当一个对象被多个对象共享,作为它们各自状态的一部分时,会发生什么？

书上的案例是一个经理陪一个秘书,当然两个经理可以公用一个秘书。

```java
Employee harry=new Employee("Harry Hacker");
Manager carl=new Manager("Carl Craker");
carl.setSecretary(harry);
Manager tony=new Manager("Tony Teeter");
tony.setSecretary(tony);
```

保存这样的对象网络对我们来说是一个挑战,我们不能去**保存和恢复秘书对象的内存地址**,因为对象被重新加载的时候内存地址会发生改变(在服务器之间传输的时候)。

![](https://gitee.com/aryangzhu/picture/raw/master/java/%E4%B8%80%E4%B8%AA%E5%AF%B9%E8%B1%A1%E5%BA%8F%E5%88%97%E5%8C%96%E7%9A%84%E5%AE%9E%E4%BE%8B.jpg)

每个对象都是用一个**序列号(serial number)**保存的,这就是这种机制之所以被称为对象序列化的原因,下面是算法:

1.对你遇到的每一个对象都关联一个序列号。

2.对于对象,当第一次遇到时,保存其对象数据到输出流中。

3.如果某个对象之前被保存过,那么只写出**"与之前保存过的序列号为x的对象相同"**。

在读回对象时,整个过程是反过来的。

1.对于对象输入流中的对象,在第一次遇到其序列号时构建它,并使用流中数据来初始化它,然后记录这个顺序号和新对象之间的关联。

2.遇到"与之前保存过的序列号为x的对象相同时"这一标记,则获取与这个序列号相关联的对象引用。

### 常用API

#### java.io.ObjectOutputStream

##### ObjectOutputStream(OutputStream out)

创建一个ObjectOutputStream使得你可以将对象写出到指定的OutputStream。

##### void writeObject(Object obj)

写出指定的对象到ObjectOutputStream,这个方法将存储指定对象的类、类的签名以及这个类及其超类中所有非静态和非瞬时的域的值。

#### java.io.ObjectInputStream

##### ObjectInputStream(InputStream in)

创建一个ObjectInputStream用于从指定的InputStream中读回对象信息。

##### Object readObject()

从ObjectInputStream中读入一个**对象**。特别是,这个方法会读回对象的类、类的签名和这个类以及超类中所有非静态和飞瞬时的域的值。它执行的反序列化允许恢复多个对象引用。

## 理解对象序列化的文件格式

这一章书上的内容很顶,建议多看几遍。

对象序列化是以特殊的文件格式存储对象数据,每个文件是以下面的两个字节的"魔幻数字"开始的

AC ED

后面紧跟着对象序列化格式的版本号,目前是

00 05

然后是它包含的**对象序列,其顺序即它们存储的顺序**。

字符串对象被存为

74 两字节表示的字符串长度 所有字符

例如,"Harry"被存为

74 00 05 

字符串中的Unicode字符被存储为修改过的UTF-8格式。

当存储一个对象时,这个对象所属的类也必须存储。这个**类的描述**包括(下面的类标识符更加详细)

1.类名。

2.序列化的**版本唯一**的ID,它是数据域类型和方法签名的指纹(指纹是通过对类、超类、接口、域类型和方法签名按照规范方式排序,然后使用安全算法SHA应用于这些数据而获得的,SHA会随着类的数据域或方法发生变化而变化)。

3.描述序列化方法的标志集。

4.对数据域的描述

​	在读入一个对象时,会拿其指纹与它所属的类的当前指纹进行对比。如果它们不匹配,那么就说明这个类的定义在该对象被写出之后发生过变化,因此会产生一个异常。当然,类是会演化的,所以读入一个旧版本的对象是必需的。

下面表示了**类标识符**是如何存储的:

1.72

2.2字节的类名长度

3.类名

4.8字节长的指纹

5.1字节长的标志

6.2字节长的数据域描述符的数量

7.数据域描述符

8.78(结束标记)
9.超类类型(如果没有就是70)

标志字节是由在java.io。ObjectStreamConstants中定义的3位掩码构成的:

static final byte SC_WRITE_METHOD=1;//class has a writeObject method that writes additional data

static final byte SC_SERIALIZABLE=2;//class implements the Serializable interface

static final byte SC_EXTENALIZABLE=4;//class implements the Externalizable interface

我们要写出的类实现了Serializable接口,并且其标志值为02,而可序列化的java.util.Date类定义了自己的readObject/writeObject方法,并且标志值为03。

每个数据域描述符的格式如下:

1.1字节长的类型编码。

2.2字节长的域名长度。

3.域名。

4.类名(如果域是对象)

大部分类型编码使用的类型的首字母,除了以下这几个:

1.J long

2.L 对象

3.Z boolean

4.[ 数组

**类名和域名字符串不是以字符串编码74开头的**,但是域类型是。

例如,Employee类薪水被编码为:

D 00 06 salary

下面是Employee类完整的**类描述符**:

72 00 08 Employee

   E6 D2 86 7D AE AC 18 1B 02 指纹和标志

   00 03                      实例的数量

   D 00 06 salary             实例域的类型和名字

   L 00 07 hireDay            同上

   74 00 10 Ljava/util/Date;  实例域的类名:Date

   ...

   78                          结束标记

   70                          无超类

这些描述相当的长,如果需要在文件中再次记录的话,可以使用一种缩写版:

71 4字节长的**序列号**

上面的序列号将会引用前面已经描述过的类描述符。

对象将会被存储为:

73 类描述符 对象数据

例如,下面就是Employee对象如何存储:

40 E8 6A 00 00 00 00 00 00      salary域的值:double

73                              hireDay域的值:新对象

​	71 00 7E 00 08              已有的类java.util.Date

​	77 08 00 00 00 91 1B 4E B1 80 78 外部存储

74  00 0C Harry Hacker          name域的值:String

然后最后是数组如何存储:

75 类描述符 4字节长的数组项的数量 数组项

### 几个注意的点:

1.对象流输出中包含素有对象的类型和数据域(像上面的形式???)。

2.每个对象都被赋予一个序列号。

3.相同对象的重复出现将被存储为对这个对象的序列号的引用。

## 修改默认的序列化机制

**某些数据域是不可以序列化的**,例如,只对本地方法有意义的存储文件句柄或者窗口句柄的整数值，这种信息在稍后重新加载对象或将其传送到其他机器上时都是没有用处的。甚至如果这种域的值处理不恰当,还会引起本地方法的崩溃。Java中拥有一种很简单的机制来防止这种域被序列化,那就是将它们标记成transient的。**如果这些域属于不可序列化的类,也需要将它们标记成transient的**。瞬时的域在对象被序列化时总是被跳过的。

​	序列化机制为单个的类提供了一种方式,去向默认的读写行为添加验证或者任何其他想要的行为。可序列化的类可以定义具有下列签名的方法:

```java
private void readObject(ObjectInputStream in)throws IOException,ClassNotFoundException;
private void writeObject(ObjectOutputStream out)throws 
IOException;
```

之后数据就再也不会被自动序列化,取而代之的是调用这些方法(也就是说之前我们只需要调用out.writeObject(harry)就能序列化对象,而现在我们需要确定数据域,然后在可序列化的类中手写数据域的序列化)。例如,下面的代码:

```java
private void writeObject(ObjectOutputStream out)throws 
IOException{
    out.defaultWirteObjct();
    out.writeDouble(point.getX());
    out.wirteDouble(point.getY());
}
```

上面的defaultWriteObject方法是可序列化的类的writeObject内部调用的。

java中的java.util.Date类,它提供了自己的readObject和writeObject方法,将日期写出为从纪元(UTC时间1970年1月1日0点)开始的毫秒数。Date类有一个复杂的内部表示,为了优化查询,它存储了一个Calendar对象和一个毫秒计数值。

Calendar对象是冗余的,因此不需要重复存储。

readObject和writeObject方法只需要加载和保存数据域,而不关心超类和其他类的信息。

### 类定义自己的机制来保存和加载对象

这个类必须实现Externalizable接口,这需要它定义两个方法:

```java
public void readExternal(ObjectInputStream in) throws IOException,ClassNotFoundException;
public void writeExternal(ObjectOutputStream out) throws IOException;
```

与序列化机制不同的是,这些方法对包括**超类数据**在内的整个对象的保存和恢复负责(序列化机制在输出流在中仅仅只是记录该对象所属的类,想想访问超类的公字段super.rariseMoney)。

下面的代码是Employee类实现上述的两个方法:

```java
public void readExternal(OjbectInput in) throws IOException,ClassNotFoundException{
    name=in.readUTF();
    salary=s.readDouble();
    hireDay=LocalDate.toEpochDay();
}
public void writeExternal(ObectOutput out) throws IOException{
    out.writeUTF(name);
    out.writeDouble(salary);
    out.writeLong(hireDay.toEpochDay());
}
```

readObject和writeObject方法是是私有的,并且只能被序列化机制调用。榆次不同的是,readExternal和writeExternal方法是公共的。特别的是,readExternal还潜在的允许修改现有对象的状态。

## 序列化单例和类型安全的枚举

在序列化和反序列化时,如果目标对象是**唯一**的,你必须加倍小心,这通常会在实现单例和类型安全的枚举时发生。

如果你使用Java语言的enum结构,那么你就不必担心序列化,它能够正常工作。

但是,有可能遗留的历史代码如下:

```java
poublic class Orientation{
	public static final Orientation HORIZONTAL=new Orientation(1);
    public static final Orientation VERTICAL=new Orientation(2);
        
    private int value;
    private Orientation(){
        
    }
}
```

注意:构造器是私有的,所以不可能创建出其他对象(符合单例条件),特别的是,你可以使用==操作符来测试对象的等同性:

```java
if(orientation == Orientation.HORIZONTAL);
```

当类型安全的枚举实现Serializable接口时,你必须牢记存在着一种重要的变化,此时,默认的序列化机制是不适用的。下面的是示例代码:

```java
Orientation original=Orientation.HORIZONTAL;
ObjectOutputStream out=...;
out.write(original);
out.close();
ObjectInputStream in=...;
var saved=(Orientation)in.read();
```

现在,下面的测试

```java
if(saved==Orientation.HORIZAONTAL)...
```

将失败。**即使构造器是私有的,序列化机制也可以创建一个全新的对象**！

所以你必须使用一个称为readResolve的特殊序列化方法。如果定义了readResovle方法,在对象被序列化之后就会调用它。它必须返回一个对象,这个对象将作为readObject方法的返回值。

```java
protected Object readResolve()throws ObjectStreamException{
    if(value==1) return Orientation.HORZONTAL;
    if(value==2) return Oritentation.VERTICAL;
    throw new ObjectStreamException();
}
```

## 版本管理

之前学习过,无论类发生了什么变化,它和SHA指纹也会发生对应的变化,而我们都知道对象输入流将拒绝读入具有不同指纹的对象。但是,**类可以表明它对之前的早期版本兼容**,要想这样做,就必须首先获得这个类的早期版本的指纹。

使用JDK中的单机程序serialver来获得类的指纹:

serialver Employee

将会打印出

Employee: static final long serialVersionUID=-18142398255173400645L;

这个类的较新版本都必须把serialVersionUID常量定义为与最新版本的指纹相同。

```java
class Employee implements Serialbizable{
	...
	public static final long serialVersionUID=-18142398255173400645L;

  		
}
```

有了这个静态常量以后,就不用人工计算指纹。

​	**一旦这个静态数据成员被置于某个类的内部,那么序列化系统就可以读入这个类的对象的不同版本**。

​	如果新版本的类的方法发生了变化,那么在读入时不会有任何影响,但是如果数据域发生了变化,那么可能会出现问题,对象输入流会尽可能将流对象转化为这个这个类的当前版本。

​	对象输入流会将这个类当前版本的数据域与被序列化的类的数据域进行比较(只考虑非瞬时和费静态的数据域)。如果这两部分数据域之间名字匹配而类型不匹配,对象输入流不会尝试将一种类型转换成另一种类型,因为两个对象不兼容;如果被序列化的对象具有在当前版本中所没有的数据域,那么**对象输入流会忽略这些额外的数据**;如果当前版本具有在被序列化的对象中所没有的数据域,那么这些新添加域将被设置成它们的默认值(如果对象则是null,数字为0,boolean值为false)。

​	这种处理安全吗？视情况而定。丢掉数据域看起来是无害的,因为接收者仍旧知道如何处理的所有数据,但是将数据域设置为null却有可能不是那么安全。

## 为克隆使用序列化

​	序列化机制提供了克隆的快捷方式,将对象序列化到输出流中,然后将其读回。这样产生的对象是"深拷贝"的对象。同时不必将对象序列化到文件中,而使用ByteArrayOutputStream将数据保存到字节数组中。	

# 操作文件

​	文件管理的内涵远比读写要广,Path和Files类封装了在用户机上处理文件系统所需的所有功能。例如,Files类可以用来移除或重命名文件,或者查询文件最后被修改的时间。

## Path

Path(路径)表示的是一个**目录名序列**,其后还可以跟着一个文件名。路径中的第一个部件可以是**根部件**,例如/或C:\,而允许访问的根部件取决于**文件系统**。以根部件开始的路径是**绝对路径**;否则,就是**相对路径**。

```java
Path absolute=Paths.get("/home","harry");
Path relative=Paths.get("myprog","conf","user.properties");
```

**静态的Paths.get方法接受一个或多个字符串**,并将它们用默认文件系统的路径分隔符连接起来。然后将其连接起来进行解析,如果表示的不是给定文件系统的合法路径,那么就抛出InvalidPathException异常。这个连接起来的结果就是一个Path对象。

get方法可以获取包含多个部件的单个字符串:

```java
String baseDir=props.getProperty("base.dir");//获取名为base.dir的属性的路径
Path basePath=Paths.get(baseDir);
```

**组合和解析路径**是司空见惯的操作,调用p.resolve(q)将按照下列规则返回一个路径:

1.如果q是绝对路径,则结果就是q。

2.否则,根据文件系统的规则,将"p后面跟着q"作为结果。

想象一个场景,假设你的应用系统需要查找相对于给定基目录的工作目录(在当前目录下查找一个工作目录),其中基目录是从配置文件中读取的。

```java
Path workRelative=Paths.get("work");
Path workPath=basePath.resolve(workRelative);
```

resolve方法有一种快捷方式,它接受一个字符串而不是路径:

```java
Path workPath=basePath.resolve("work");//字符串而不是对象
```

还有个很方便的方法resolveSibling,他通过解析指定路径的父路径产生其兄弟路径。

例如,如果workPath=workPath.resolveSibling("temp");

将创建/opt/myapp./temp

relativize方法会调用p.relativize(r)将产生路径q,而对q进行解析的结果正是r。例如,以"/home/harry"为目标对"/home/fred/input.txt"进行相对化操作,会产生"../fred/input.txt",其中我们假设..表示文件系统中的父目录(也就是说两个路径都是/home开头，所以可以使用../作为相对路径)。

### 常用API

#### java.nio.file.Paths

##### static Path ge(String first,String ..more)

通过连接给定的字符串创建一个路径。

#### java.nio.file.Pah

Path resolve(Path other)

##### Path resovle(String other)

如果other是绝对路径,那么就返回other;否则将通过链接this的父路径和other产生路径。

##### Path relativize(Path other)

返回用this进行解析,相对于other的相对路径。

##### Path normalize()

移除诸如.和..等冗余的路径元素。

##### Path toAbsolutePath()

返回与该路径等价的绝对路径。

##### Path getParent()

返回父路径,或者在该路径没有父路径时,返回null。

##### Path getFileName()

返回该路径的最后一个部件,或者在该路径上没有任何部件时,返回null。

##### Path getRoot()

返回该路径的根部件,或者在该路径没有任何部件时,返回null。

##### toFile()

从该路径中创建一个File对象。

#### java.io.File

##### Path toPath()

从该文件中创建一个Path对象(利用文件的路径属性来完成创建)。

## 读写文件

利用下面的代码很容易读取文件的所有内容:

```java
byte[] bytes=Files.readAllBytes(path);
```

还可以利用下面的方法来读取文件内容:

```
var content=Files.readString(path,charset);
```

将文件当做行序列读入:

```java
List<String> lines=Files.readAllBytes(path,charset);
```

相反,如果希望写出一个字符串到文件中,可以调用:

```java
Files.writeString(path,content.char);
```

向指定文件追加内容:

```java
Files.write(path,content.getBytes(charset),StandardOpenOption.APPEND);
```

将一个行的集合写出到文件中:

```java
Files.write(path,lines,charset);
```

虽然上面的代码很方便,还是推荐使用输入/输出流(因为更加专业)。

### 常用API

#### java.nio.file.Files

##### static List\<String> readAllLines(Path path,Charset charset)

读入文件的内容。

##### staic Path write(Path path,Iterable<? extends CharSequence> contents,OpenOption options)

将给定内容写出到文件中,并返回path。

##### static BufferedWriter newBufferedWriter(Path path,Charset charset,OpenOption... options)

打开一个文件,用于读入或这写出。

## 创建文件和目录

**创建新目录**可以调用

```java
Files.createDirectory(path);
```

其中的路径除了最后一部分,其他部分必须是已经存在的。

要创建中间目录可以调用

```
Files.createDirectories(path);
```

如果需要创建一个空文件:

```java
Fiels.creatFile(path);
```

如果文件已经存在,那么这个调用就会抛出异常。检查文件是否存在和创建文件是**原子性**的。如果文件不存在,那么该文件就会被创建,并且其**他程序在此过程中是无法执行文件创建操作的**。

有些便捷方法在给定位置湖泽系统指定的位置创建临时文件或者临时目录。

其中,dir是一个Path对象,prefix和suffix是可以为null的字符串。

在创建文件或者目录时,可以指定属性例如文件的拥有者和权限。

### 常用API

#### java.nio.file.Files

##### static Path createDirectories(Path path,FileAtrribute<?>...attrs)

创建一个文件或者目录,createDirectories方法还会创建路径中所有的中间

目录。

## 复制、移动和删除文件

将文件复制到另一个位置

Files.copy(fromPath,toPath);

移动文件(即复制并删除原文件)

Files.move(fromPath,toPath);

如果目标路径已经存在,那么复制或移动会失败。但是可以对目标路径进行覆盖,使用**REPLACE_EXISTING选项**。如果想要复制所有的文件属性,可以使用**COPY_ATTRIBUTES选项**。

```java
Files.copy(fromPath,toPath,StanddardCopyOption.REPLACE_EXISTING,StandardCopyOption.COPY_ATTRIBUTESE);
```

你可以将移动操作定义为原子性(要成功都成功)的,这样就**保证要么移动操作完成,要么源文件继续保持在原来位置**。

```java
Files.move(fromPath,toPath,StandardCopyOption.AIOMIC_MOVE);
```

还可以将一个输出流复制到Path中,这表示想要将输入流存储到硬盘上。

```java
Files.copy(inputStream,toPath);
```

删除文件:

```java
Files.delete(path);
```

用于文件操作的标准选项可以看书上的表格,非常的详细。

### 常用API

#### java.nio.file.Files

##### static Path move(Path from,Path to,CopyOption...options)

##### static long copy(Path from,OutputStream to,CopyOption ...options)

从文件复制到输入流中,返回复的字节数。

static void delete(Path path)

##### static boolean deleteIfExists(Path path)

删除给定文件或空目录。第一个方法在文件或目录不存在的情况下抛出异常,而第二种方法会在情况下返回false。

## 获取文件信息

下面的静态方法都将返回一个boolean值,表示**检查路径的某个属性**的结果:

1.exists

2.isHidden

3.isReadable,isWriteable,isExecutable

4.isRegularFile,isDirectory,isSymbolicLink

size方法将返回文件的字节数:

```java
long fileSize=Files.size(path);
```

getOwner方法将**文件的拥有者**作为java.nio.file.attribute.UserPrincipal的一个实例返回。

所有的文件系统都会报告一个基本属性集,它们被封装在**BasicFileAttributes**接口中,这些属性与上述信息有部分重叠。基本文件属性包括:

1.创建文件、最后一次访问以及是最后一次修改文件的**时间**,这些时间都表示成java.nio.file.attribute.FileTime。

2.文件是常规文件、目录还是符号链接,还是三者都不是。

3.文件尺寸。

4.文件主键,这是某种类的对象,具体所属类与文件系统相关,有可能是文件的唯一标识符,也可能不是。

要获取这些属性,可以调用

```java
BasicFileAtrributes attributes=Files.readAttributes(path,BasicFileAttributes.class);
```

### 常用API

#### java.nio.file.Files

##### static boolean isSymbolicLink(Path path)

检查由路径指定的文件的给定属性。

###### static long size(Path path)

获取文件按字节数度量的尺寸。

##### A readAttributes(Path path,Class\<A> type,LinkOption...options)

读取类型为A的文件属性。

#### java.nio.file.attribute.BasicFileAttributes

FileTime creationTime()

boolean isRegularFile()

boolean isDiretory()

boolean isSymbolicLink()

long size()

Object fileKey()

获取所请求的属性

## 访问目录中的项

**静态的Files.list**方法会返回一个可以读取目录中各个项的**Stream\<Path>对象**。目录是被惰性(使用时才会进行调用或某些操作)读取的,这使得处理具有大量项目的目录可以变得更加高效。

读取目录涉及需要关闭的系统资源,所以应该使用try块:

```java
try(Stream<Path> entries=Files.list(pathToDirectory)){
    ...
}
```

list方法不会进入子目录。为了处理目录中的所有子目录,需要使用File.walk方法。

```java
try(Stream<Path> entries=Files.walk(pathRoot)){
    //Contains all descendants,visited in depth-first order;
}
```

可以通过调用Files.walk(pathToRoot,depth)来限制想要访问的树的深度。

**两种walk方法都有可变长的参数FileVisitOption...,但是你只能提供一种选项-FOLLOW_LINKS,即跟踪符号链接**。

## 使用目录流

上面我们学习了使用Files.walk方法会产生一个可以遍历目录中所有子孙的Stream\<Path>对象。有时,需要对遍历过程进行更加细粒度的控制。在这种情况下,应该使用Files.newDirectoryStream对象,它会产生一个DirectoryStream。它不是java.util.stream.Stream的子接口,而是专门用于目录遍历的接口。它是Iterable的子接口,因此可以在增强的for循环中使用目录流。示例如下:

```java
try(DirectoryStream<Path> entries=Files.newDirectoryStream(dir)){
    for(Path entry:entries){
        Process entries;
    }
}
```

