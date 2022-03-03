对于异常情况，例如，可能造成程序崩溃的错误输入，Java使用了一种称为**异常处理**(exception handing)。

# 处理错误

假设程序运行期间出现了一个用户并不想见到的错误。用户希望程序能够合理地处理这个错误。如果某些操作没有完成，程序应该:

1.返回到一种安全状态，并能够让用户执行其他的命令;或者

2.允许用户保存已经所有工作的结果，并以妥善的方式终止程序

要实现上面的任务很难，因为检测引发错误的代码和能让工作保存的代码相距很远。处理异常的任务就是将控制权由错误代码转交到保存工作的处理器上。	

为了能够处理异常情况，必须考虑到程序中可能会出现的错误和问题。

1.用户输入错误。例如，输入URL语法有误。

2.设备错误。例如，浏览器网页打不开，打印机设备在打印过程中没有打印纸。

3.物理限制。例如，物理磁盘空间已满。

4.代码错误。**程序或者方法可能没有正确的完成工作**。例如，方法返回一个错误答案，或者错误地调用了其他方法。计算一个无效的数组索引，在散列表中查找一个不存在的记录，或者让空栈执行一个弹出操作。

传统的错误处理方法是在方法执行错误之后返回一个值，通过这个值来匹配一种错误。例如，-1代表返回的是错误结果。但是，不是任何时候都能返回一个值。

在Java中，如果方法不能正常地完成任务那么也可以通过另一个路径退出程序。在这种情况下，程序会抛出(throw)一个封装异常信息的对象。这个时候，方法会立刻退出，并不返回任何值。此外，也不会调用这个方法的代码继续执行，取而代之的是，异常处理机制开始搜索能够处理这种异常状况的**异常处理器**(exception handler)。

## 异常分类

在Java程序设计语言中，异常对象都是派生于Throwablele类的实例对象。

![](https://gitee.com/aryangzhu/picture/raw/master/java/%E5%BC%82%E5%B8%B8%E4%BD%93%E7%B3%BB.png)

Error类层次结构描述了java运行时系统的内部错误或资源耗尽等情况。你不应该抛出这种错误。出现这种错误，除了通知用户并尽力妥善地终止程序几乎无能为力。所幸的是，这种情况很少见。

Exception层次结构是我们关注的重点。它又分为了两个分支:一个分支派生于RuntimeException;另一个分支包含其他异常。一般规则是:由编程导致的异常属于RuntimeException;程序本身没有问题，但是由于像I/O错误引起的异常属于其他异常。

派生于RuntimeException的异常:

1.错误的强制类型转换;

2.数组访问越界；

3.访问null指针。

派生于其他异常的情况:

1.试图超越文件末尾继续读取数据;

2.试图打开一个不存在的文件;

3.试图根据给定的字符串查找Class对象，而这个字符串表示的类并不存在。

“RuntimeException一定是你的自己的问题”，我们在编写代码时就应该避免出现这种异常。

1.检测数组下标来避免ArrayIndexOutOfBoundsException异常

2.访问对象检测是否为null来杜绝NullPointException异常

**java中将派生于Error和RuntimeException类的所有异常称为-非检查型异常**。其他异常称为检查型异常。

## 声明检查型异常

**方法不仅仅需要告诉编译器将要返回什么值，还要告诉编译器有可能出现什么错误**。例如，一段读取文件内容的代码知道**有可能读取的文件不存在，或者文件内容为空**，因此，试图处理文件信息的代码需要通知编译器可能会抛出IOException类的异常。

如果要指定一个方法有可能抛出一个异常，就要修改方法的首部。例如，标准类库中的FileInputStream类的一个构造器的声明。

```java
public FileInputStream(String name) throws FileNotFoundException
```

这个构造器会根据String类型的参数会产生一个FileInputStream对象，但是也有可能会出现异常。这个时候，会抛出一个FileNotFoundExceptin对象，如果这个方法真的抛出异常对象，**运行时系统就会开始搜索知道如何处理FileNotFoundException对象的异常处理器**。

在自己编写代码时，不必声明这个方法可能抛出的所有异常。

**什么时候在方法中使用throws声明异常**？throws子句声明哪些异常？需要记住遇到一下4种情况时会抛出异常(假设我们自己编写了一个**方法**遇到了下面的情况):

1.调用了一个抛出检查型异常的方法，例如，FileInputStream构造器。

2.检测到一个错误，并且利用throw语句抛出一个检查型异常(就是说这个方法中已经出现了这种情况，我们使用throw来抛出这个检查型异常)。

3.程序出现错误，例如，a[-1]=0;会抛出一个非检查型的错误

4.Java虚拟机或运行行时库出现问题。

如果出现前两种情况，则必须要告诉调用这个方法的程序员有可能会抛出异常。为什么，因为任何一个抛出异常的方法都有可能是死亡陷阱。**如果没有处理器捕获这个异常，那么执行的线程就会终止**。

有些Java方法包含在对外提供的类中，我们应该使用方法首部的**异常规范**(exception specification)声明这个方法可能抛出异常。

```java
class MyAnimation{
    ...
    public Image loadImage(String s) throws IOException{
        ...
    }
}
```

如果有一个方法有可能抛出多个检查型异常类型，那么就必须在方法的首部列出所有的异常类。

```java
class MyAnimation{
    ...
    public Image loadImage(String s)throws FileNotFoundException,EOFException{
        ...
    }
}
```

总之，一个方法必须声明所有可能抛出的检查型异常。

### 需要注意的点

1.如果超类中声明了检查型异常，子类覆盖超类的方法时不能声明超过父类的声明异常类型(子类可以抛出更加特定的异常或者不抛出异常)；如果超类没有声明，子类也不能声明。

2.如果一个方法抛出一个异常，而这个异常是某个特定类的实例，那么这个异常可能属于这个类，也可能属于这个类的任意一个子类。

## 如何抛出异常

如果已有的异常类能够满足你的要求，可以查阅JavaAPI文档，在这种情况下:

1.找到一个合适的异常类;

2.创建这个类的一个对象;

3.将对象抛出。

```java
throw new EOFException();
```

或者是

```java
var e=new Exception();
throw e;
```

书上的例子是读取文件的承诺长度与实际长度不符

```java
String readData(Scanner in)throws EOFException{
    while(...){
        if(!in.hasNext()){
            if(n<len){
                throw new Exception();
            }
        }
    }
    return s;
}
```



## 创建异常类

如果我们遇到任何标准异常类都无法描述的问题，则需要自己创建异常类。

### 如何自定义异常类?

1.定义的类需要派生于Exception类或者Exception类的子类(IOException等);

2.通常做法是自定义两个构造器，一个是无参构造器；另一个则是包含详细信息的构造器(超类Throwable的toString方法会返回一个字符串，其中包含这个信息，这在调试中非常有用)。

```java
class FileFormatException extends IOException{
    public FileFormatException(){}
    public FileFormatException(String gripe){
        super(gripe);
    }
}
```



### 常用API

#### java.lang.Throwable

##### Throwable()

构造一个新的Throwable对象，**但没有详细的描述信息**

##### Throwable(String message)

**同上，但是带有指定的详细信息描述**。

##### String getMessage()

获得Throwable对象的详细描述信息。

# 捕获异常

## 捕获异常

如果发生了某个异常，但没有在任何地方捕获这个异常，程序就会终止，并在控制台上打印一个消息，包括这个异常的类型和一个堆栈轨迹。使用try/catch语句块来捕获异常。

读取文件的例子:

```java
public void read(String fileName){
    try{
        FileInputStream in=new FileInputStream(fileName);
        int b;
        while((b=in.read())!=-1){
            process input
        }
    }catch(IOException e){
        e.printStackTrace();
    }
}
```

1.如果try语句块中的任何代码跑出了cathch语句块中的一个异常类，将跳过try语句块中的其余代码。

2.程序将执行catch子句中的处理器代码。

上面的代码很容易理解，我们除了自己处理异常之外还有没有别的方式?

通常,最好的选择就是什么也不做，而是将异常传递给调用者。

```java
public void read(String fileName) throws IOException{
    var in=new FileInputStream(fileName);
    int b;
    while((b=in.read())!=-1){
        process input
    }
}
```

**编译器严格执行throws说明符**。如果调用了一个抛出检查型异常的方法，就必须处理这个异常。

哪种方法更好呢？

**一般经验，要捕获那些你知道如何处理的异常，而继续传播那些你不知道怎样处理的异常**。

如果超类方法没有抛出异常，而子类进行方法覆盖，就需要捕获方法代码中的每一个检查型异常。

## 捕获多个异常

如下

```java
try{
    ...
}catch(FileNotFoundException e){
    emergency action for missing files
}catch(UnknownHostException e){
    ...
}catch(IOException e){
    ...
}
```

注意:**捕获多个异常时,异常变量隐含为final变量**。例如,在以下子句体中不能为e赋不同的值:

catch(FileNotFoundException | UnknownHostException e){

...

}

## 再次抛出异常与异常链

通常catch语句块用来处理异常，但是也可以在catch中再次抛出一个异常。书上的场景是:有个子系统供其他程序员来调用，**可以使用一个指示子系统故障的异常类型**。

将原始异常设置为新异常的“原因”:

```java
try{
    access the database
}catch(SQLException original){
    var e=new ServletException("database error");
   	e.initCause(original);
    throw e; 
}
```

捕获到异常时，可以使用下面这条语句获取原始异常:

```java
Throwable original=caughtException.getCause();
```

强烈建议使用这种包装技术。这样可以在子系统中抛出高层异常，而不会丢失原始异常的细节。

## finally子句

不管异常是否被捕获,finally中的代码都会被执行。

```java
try{
    //1
    code that might throw exception
    //2
}catch(IOException e){
    //3
    show error message
    //4
}finally{
    //5
    in.close();
    
}
//6
```

1.如果没有问题，那么将会是1256;

2.如果try语句块中抛出异常，那么将会是13456;

3.如果catch语句块中抛出异常,**那么异常将会抛回到这个方法的调用者**(假设，e.printStackTrace()方法出错，那么将会由方法中所设置的异常处理机制(throws| throw new Exception()))。执行顺序将会是135(也就是说try/catch语句块之外的代码不会执行);

4.如果代码部分抛出异常，但是没有catch块能够捕获异常。程序将跳过try语句块中的剩余代码，最后执行finally中的代码，并将异常抛回给这个方法的调用者。


## try-with-Resource子句

假设资源属于一个实现了AutoCloseable接口的类，Java7为这种代码模式提供了一个很有用的快捷方式。AutoCloseable接口有个方法:

void close() throws Exception

try-with-Resource(带资源的try语句)的最简形为:

```java
try(Resource res=...){
    work with res
}
```

try块退出时会自动调用res.close()方法。下面是一个简单的例子:

```java
try(Scanner in=new Scanner(new FileInputStream("/usr/share/dict/words"),StandardCharsets.UTF_8);
PrintWriter out=new PrintWriter("out.txt",StandardCharsets.UTF_8)){
    while(in.hasNext()){
        System.out.println(in.next());
    }
}
```

无论这个块如何退出,in和out都会关闭。 

## 分析堆栈轨迹元素

**堆栈轨迹**是程序执行过程中某个特定点上所有挂起的方法调用的一个列表。

当Java程序因为一个未捕获的异常而终止时，就会显示堆栈轨迹。

1.可以调用Throwable类的printStackTrace方法访问堆栈轨迹的文本信息。

```java
var t=new Throwable();
var out=new StringWriter();
e.printStackTrace(new PrintWriter(out));
String description=out.toString();
```

2.我们还有另一种方法就是使用StackWalker类,他会生成一个StackWalker.StackFrame实例流,其中每个实例分别描述一个栈帧(stack frame)。可以利用以下调用迭代处理这些栈帧:

```java
StackWalker walker=StackWalker.getInstance();
walker.forEach(frame->analyze frame);
```

也可以通过懒方式处理Stream\<StackWalker.StackFrame>,可以调用:

```java
walker.walk(stream->process stream);
```

利用StackWalker.StackFrame类的一些方法可以得到所执行代码行的文件名和行号，以及类对象和方法名。

### 常用API

#### java.lang.Throwable

Throwable(Throwable cause)

##### Throwable(String message,Throwable cause)

用给定的cause(原因)构造一个Throwable对象。

##### Throwable initCase(Throwable cause)

为这个对象设置原因，如果这个对象已经有原因，则抛出一个异常。返回this。

##### Throwable getCause()

获得设置的原因对象。

##### StackTraceElement[] getStackTrace()

获得构造这个对象时调用堆栈的轨迹。

##### void addSuppressed(Throwable t)

为这个异常增加一个“抑制的”异常。这出现在try-with-Resource语句中,其中t是close方法中抛出的一个异常。

##### Throwable[] getSuppressed()

得到被抑制的异常。

#### java.lang.Exception

Exception(Throwable cause)

##### Exception(String message,Throwable cause)

用给定的cause构造一个Exception对象。

#### java.lang.RuntimeException

RuntimeException(Throwable cause)

RuntimeException(String message,Throwable cause)

#### java.lang.StackWalker

static StackWalker getInstance()

static StackWalker getInstance(StackWalker.Option option)

##### static StackWalker getInstance(Set\<StackWalker.Option> options)

得到一个StackWalker实例。

##### forEach(Consumer<? super StackWalker.StackFrame> action)

在每个栈帧上完成给定的动作，从最近调用的方法开始。

##### walk(Function<? super Stream\<StackWalker.StackFrame>,? extends T> function)

对一个栈帧流应用给定的函数，返回这个函数的结果。

#### java.lang.StackWalker.StackFrame

##### String getFileName()

得到包括该元素执行点的源文件的文件名,如果这个信息不可用则返回null。

##### int getLineNumber()

得到包含该元素执行点的源文件的行号,如果这个信息不可用则返回-1。

##### String getClassName()

得到方法包含该元素执行点的类的完全限定名。

##### String getDeclaringClass()

得到方法包含该元素执行点的类的Class对象。

##### String getMethodName()

得到包含该元素执行点的方法的方法名。构造器方法名为\<init>。静态初始化器的方法名为\<clinit>。无法区分同名的重载方法。

##### boolean isNativeMethod()

如果这个元素的执行点在一个原声方法中,则返回true。

##### String toString()

返回一个格式化字符串,包含类和方法名、文件名以及行号。

#### java.lang.StackTraceElement

String getFileName()

同上

int getLineNumber()

String getClassName()

String getMethodName()

boolean isNativeMethod()

String toString()

# 使用异常的技巧

## 1.使用异常不能代替简单的测试

理由很简单，捕获异常所用的时间比简单测试所花费的时间多得多。

## 2.不要过分细化异常



## 3.充分利用异常的层次结构

不要只抛出RuntimeException异常。应该寻找一个适合的子类或创建自己的异常类。

不要只捕获Throwable异常，否则会使你的代码更难度、更难维护。

如果能够将一种异常转换成另一种更加适合的异常,那么不要犹豫。

## 4. 不要压制异常

如果编写的方法调用了另一个方法，那个方法很久才会抛出一个异常，这时不使用throws列表中声明异常的话，编译器就会报错。

关闭异常

```java
public Image loadImage(String s){
    try{
        code that threatens to throw checked exceptions
    }catch(Exception e){
    }//so there
}
```

我们可以看到catch语句块中什么也没干,一旦出现异常，这个异常就会被悄无声息地忽略。

## 5.在检测异常时，“苛刻”更好

例如，使用无效的参数调用一个方法时，返回一个虚拟值是不是比抛出一个异常好?

我们认为:最好在出错的地方抛出一个EmptyStackException异常，这要好于以后抛出一个NullPointerException异常。

## 6.不要羞于传递异常

如果调用了一个抛出异常的方法,例如,FileInputStream构造器或readLine方法,它们就会本能地捕获这些可能产生的异常。其实,最好继续传递这个异常,而不是自己捕获

**5和6可以归纳为"早抛出,晚捕获"**。

# 使用断言

## 断言的概念

断言机制是允许在代码测试期间加入一些检查,而在生产代码中会自动删除这些检查。

Java中引入了关键字assert。这个关键字有两种形式:

1.assert condition;

2.assert condition:expression;

在第二个语句中,表达式将传入AssertionError对象的构造器,并转换成一个消息字符串。

"表达式"(expression)的唯一目的就是产生消息字符串。

```java
assert x>=0;
assert x>=0:x;
```

## 启用和禁用断言

需要注意的是，**不必重新编译程序来启用或禁用断言**。启用或禁用断言是**类加载器**的功能。

**可以运行程序时用-enableassertions或-ea选项启用断言**:

java -enableassertions MyApp

也可以在某个类或整个包中启用断言,例如:

java -ea:MyClass -ea:com.mycompany.mylib MyApp

这条命令将为MyClas类以及com.mycompany.mylib包和它的子包的所有类打开断言。x选项ea是打开所有无名包中所有类的断言。

使用选项-disableassertions或-da在某个特定类和包中禁用断言:

java -ea:... -da:MyClass MyApp

有些类不是由类加载器加载,**而是直接由虚拟机加载的**。可以使用这些开关有选择地启用或禁用那些类中的断言。

## 使用断言完成参数检查

### 什么时候使用断言

1.断言失败是不可恢复的;

2.断言只是在开发和测试阶段打开

因此，不应该使用断言向程序的其他部分通知发生了可恢复性的错误,不应该利用断言程序与程序用户沟通问题。

## 使用断言提供假设文档

### 常用API

#### java.lang.ClassLoader

void setDefaultAssertionStatus(boolean b)

为通过类加载器加载的类(没有显示的类或包断言状态)启用或禁用断言。

void setClassAssertionStatus(String className,boolean b)

为给定的类和它的内部类启用或禁用断言状态。

void setPackageAssertionStatus(String packageName,boolean b)

为给定包以及子包中的所有类启用或禁用断言。

void clearAssertionStatus()

删除所有显式的类和包断言状态设置，并禁用通过这个类加载器加载的所有类的断言。

# 日志

**日志是一种在程序整个生命周期都可以使用的战略性工具**。

可以不用使用System.out.print()插入在代码中去打印某些值。

日志API的优点:

1.**可以轻易地取消全部日志记录,或者仅取消某个级别一下的日志**,而且可以很容易地再次打开开关;

2.可以很简单地禁止日志记录,因此,将这些日志代码留在程序中的开销很小;

3.**日志记录可以被定向到不同的处理器**,如在控制台、写至文件等;

4.日志记录器和处理器可以对日志进行过滤。可以根据**过滤器实现器指定的标准**丢弃那些无用的记录项;

5.**日志记录可以采用不同的方式格式化**,例如,纯文本或XML;

6.应用程序可以使用多个日志记录器,他们使用与包名类似的有层次结构的名字,例如,com.mycompany.myapp;

7.日志系统的配置由配置文件控制。

## 基本日志

生成简单的日志记录,可以使用全局日志记录器(global logger)并调用其info方法:
Logger.getGlobal().info("File->Open menu item selected");

如果在适当的地方(如main方法)调用

Logger.getGlobal().setLevel(Level.OFF);

## 高级日志

在一个专业的应用程序中肯定不能将所有的日志都记录到一个全局日志记录器中。 

可以调用getLogger方法创建或获取日志记录器:

```java
private static final Logger myLogger=Logger.getLogger("com.mycompany.myapp");
```



注:未被任何变量引用的日志记录器可能会被垃圾回收。为了防止这种情况发生,要像上面的例子一样,用静态变量存储日志记录器的引用。

与包名类似,日志记录器也具有层次结构,而且日志具有更强的层次性。对于包来说,包与父包之间没有语义关系,但是日志记录器的父与子之间将共享某些属性。

通常,日志有

### 7个级别:

SEVERE(严重)

WARNING

INFO

CONFIG(配置)

FINE

FINER

FINEST

**从下往上级别越来越高,默认情况下,实际上只记录前3个级别**。也可以设置一个不同的级别,例如,

logger.setLevel(Level.FINE);

现在,FINE以及所有更高级别的日志都会记录。

另外,**还可以使用Level.ALL开启所有级别的日志记录,或者使用Level.OFF关闭所有级别的日志记录**。

所有级别都有日志记录的方法

logger.warning(message);

logger.fine(message);

或者,还可以使用log方法并指定级别,例如:

logger.log(Level.FINE,message);

**默认的日志记录将显示根据调用堆栈得出的包含日志调用的类名和方法名**。但是**如果虚拟机对执行过程进行了优化,就得不到准确的调用信息**。此时,可以使用logp方法获得调用类和方法的确切位置,这个方法的签名为:

void logp(Level l,String className,String methodName,String messge);

### 有一些用来跟踪执行流的方法:

void entering(String className,String methodName)
void entering(String className,String methodName,Object param)

void entering(String className,String methodName,Object[] params)

void exiting(String className,String methodName)
void exiting(String className,String methodName,Object result)

例如:

```java
int read(String file,String pattern){
	logger.entering("com.mycompany.mylib.Reader","read",new Ojbect[]{file,parttern});
    ...
    logger.exiting("com.mycompany.mylib.Reader","read",count);
}
```



这些调用将生成FINER级别而且以字符串ENTRY和RETURN开头的日志记录。

记录日志的常见用途是记录预料之外的异常。**可以使用下面两个便利方法在日志记录集中包含异常的描述**:

#### void throwing(String className,String methodName,Throwable t)

#### void log(Level l,String message,Throwable t)

## 修改日志配置

可以通过编辑配置文件来修改日志系统的各个属性。在默认情况下,配置文件位于:

conf/logging.properties(在Java9之前,位于jdkxxx/jre/lib/logging.properties)

![](https://gitee.com/aryangzhu/picture/raw/master/%E6%97%A5%E5%BF%97%E9%85%8D%E7%BD%AE.png)

要使用另外一个配置文件,就要将**java.util.logging.config.file属性**设置为那个文件的位置，**为此要用以下命令启动应用程序**:

```shell
java -Djava.util.logging.config.file=configFile MainClass
```



想要修改默认的日志级别,就需要**编辑配置文件**,并修改以下命令行

.level=INFO

可以通过添加下面这一行来指定自定义日志记录器的日志级别

com.mycompany.myapp.level=FINE(就是说在日志记录器的后面追加后缀.level)

日志记录器并不将消息发送到控制台,那是处理器的任务。如果想要在控制台看到FINE级别的消息,就需要单独进行设置。

java.util.logging.ConsoleHandler.level=FINE

注:**日志管理器(LogManager)配置中的属性设置不是系统属性**,因此,用-Dcom.mycompany.myapp.level=FINE启动程序时不会对日志记录器(Logger)产生任何影响。

**日志管理器在启动虚拟机时初始化,也就是在main方法执行前**。如果想要定制日志属性,但是没有使用java -Djava.util.logging.config.file命令行选项启动应用,也可以在程序中调用**System.setProperty(“java.util.logging.config.file”,file)**,不过这样一来,你就必须使用**LogManager.getLogManager().readConfiguration()重新初始化日志配置**。

在Java9中,可以通过调用以下方法更新日志配置:

```java
LogManager.getLogManager().updateCOnfiguration(mapper);
```

这样就会从java.util.logging.config.file系统属性指定的位置读取一个新配置。然后**应用到这个映射器来解析新配置或者老配置的所有键的值**。映射器是一个**Function\<String,BiFunction\<String,String,String>>**。它**将现有配置的键映射到替换函数**。**每个替换函数接收到与键关联的老值和新值(如果没有关联的值则得到null),生成一个替换**(万物皆对象),或者如果要在更新中删除这个键则返回null。

例如，常见的就是合并新配置和老配置,如果一个键在老配置和新配置中都出现,则优先选择新值。那么，这样一个**映射器**:

key->((oldValue,newValue)->newValue==null?oldValue:newValue)

上面使用了lambda表达式，key是Function<>函数式接口中前一个参数,而后面的一个参数又是一个函数式接口BiFunciton<>使用lambda表达式实现了方法。

或者说只想以com.company开头的键,其他的键保持不变:

```java
key->key.startWith("com.company")
    ?((oldValue:newValue)->newValue)
    :((oldValue:newVaule)->oldValue)
```

也可以使用jconsole程序改变一个正在运行的程序级别。

## 本地化

本地化的应用程序包含**资源包**中的本地特定信息。

一个程序可以包含多个资源包,例如一个用于菜单,另一个用于日志消息。每个资源包都有一个名字(如,"com.mycompany.logmessages")。要想为资源包增加映射,**需要对应每个本地化环境提供一个文件**(如果没有这个映射文件的话,那么应当先创建一个映射文件;如果映射文件存在,那么应当在映射文件里增加这个映射消息)。英文消息映射位于com/mycompany/logmessages_en.properties文件中;德文消息映射位于com/company/logmessages_de.properties文件中。可以将这些文件与应用程序的类文件放在一起,以便ResourceBundle类自动找到它们。

下面的都是映射文件(文本文件)的具体条目:

```java
readingFile=Achtung!Datei wird eingelesen
renamingFile=Datei wird umbenannt
```

创建日志记录器的时候可以指定资源包

```java
Logger logger=Logger.getLogger(loggerName,"com.mycompany.logmessage");
```

然后,**为日志消息指定资源包的键**,而不是实际的日志消息字符串。

```
logger.info("readingFile");
```



## 处理器

在默认情况下,**日志记录器将记录发送到ConsoleHandler(控制台处理器),并由它输出到System.err流(输出流)**。具体地,**日志记录器会把记录发送到父处理器,而最终的祖先处理器(名为"")有一个ConsoleHandler**。

与日志记录器一样,处理器也有日志级别。对于一个要记录的日志记录,它的日志级别必须高于日志记录器和处理器二者的阀值。日志管理器配置文件将默认的控制台处理器的日志级别设置为

java.util.logging.ConsoleHandler.level=INFO

我们可以自己修改配置文件中的默认日志记录器级别和处理器级别,也可以绕过配置创建自己的处理器。

```java
Logger logger=Logger.getLogger("com.mycompany.myapp");
logger.setLevel(Level.FINE);
logger.setUseParentHandlers(false);
ConsoleHandler handler=new ConsoleHandler();
handler.setLevel(Level.FINE);
logger.addHandler(handler);
```

在默认情况下,日志记录器将记录发送的自己的处理器和父日志记录器的处理器。我们的日志记录器是祖先记录器(名为"")的子类,而这个祖先日志记录器会把所有等于或高于INFO级别的记录发送到控制台。但是，我们并不想两次看到相同的日志记录,所以将useParentHandlers属性设置为false。

要将日志记录发送到其他地方,就要添加其他的处理器。日志API为此提供了两个非常有用的处理器,一个是FileHandler,另一个是SocketHandler。**SocketHandler将记录发送到指定的主机和端口**。

例如

```java
FileHandler handler=new FileHandler();
logger.add(handler);
```

这些记录被发送到用户主目录的javan.log文件中,n是保证文件唯一的一个编号。如果没有主目录的概念,就会保存到一个默认位置。默认情况下,记录格式会转化成为XML。一个典型的日志记录如下:

```java
<record>
    <date>...</date>
    <mills>...</mills>
    <sequence>...</sequence>
</record>
```

日志管理器的配置文件中有许多不同的参数,下面我们来看几个比较重要而且常用的:

java.util.logging.FileHander.formatter->记录格式化器(描述)->java.util.logging.XMLFormatter(默认值)。

java.util.logging.FileHandler.pattern->日志文件名的模式(描述)->%h/java%u.log(默认值)。

也有可能不想使用默认的日志文件名,因此,应该使用另一种模式,例如,%h/myapp.log。

由于接触的比较少,所以我们在这里描述一下

### 日志记录文件模式变量

%h->系统属性user.home的值

%t->系统临时目录

%u->用于解决冲突的唯一编号

%g->循环日志的生成号

%%->%字符

如果多个应用程序使用同一个日志文件,就应该使用append标志。并且要使用%u来方便每个应用程序来创建唯一的副本。‘

日志文件也可以以循环序列的形式保存(如myapp.log.0,myapp.log.2等)。**只要文件超出了大小限制,最老的文件就会被删除,其他的文件将重新命名,同时创建一个新的文件,其生成号为0**。

还可以通过扩展Handler类或者StreamHandler类来自定义处理器。例如,

```java
class WindowHandler extends StreamHandler{
    public WindowHandler(){
      ...
      JTextArea output=new JTextArea();
        
      setOutputStream(new OutputStream(){
          public void write(int b){}
          public void write(byte[] b,int off,int len){
              output.append(new String(b,off,len));
          }
      });
    }
}
```

但是上面的方式有一个缺陷就是处理器会缓存日志记录,所以我们需要刷新缓冲区将其写入流中。

```java
class WindowHandler extends StreamHandler{
	...
    public void publish(LogRecord record){
        super.publish(record);
        flush();
    }
}
```

## 过滤器

默认情况下,会根据日志记录级别进行过滤。每个日志记录器和处理器都有一个可选的过滤器来完成附加的过滤。要定义一个过滤器,需要实现Filter接口并实现以下方法:

```java
boolean isLoggable(LogRecord record)
```

例如,某个过滤器可能只对entering方法和exiting方法生成的消息感兴趣,这个过滤器就可以调用record.getMessage()方法,并检查消息是否以ENTYR和RETURN开头。要想将一个过滤器安装到一个日志记录器或处理器中,**只需调用setFilter方法**就可以了。                                              

## 格式化器

ConsoleHandler类和FileHandler类可以生成文本和XML格式的日志记录。你也可以扩展Formatter类并覆盖下面的这个方法:

```java
String format(LogRecord record)
```

可以根据自己的需要以任何方式对记录中的信息进行格式化,并返回字符串。在format方法中,可能会调用下面这个方法:

```java
String formatMessage(LogRecord record)
```

这个方法对记录中的消息部分进行格式化,将替换参数并应用本地化处理。

很多文件格式需要在记录的前后添加一个头部和尾部。

```java
String getHead(Handler h)
String getTail(Handler h)
```

## 日志技巧

1.对于一个简单的应用,选择一个日志记录器。可以把日志记录器命名为与主应用包一样的名字,例如,com.mycompany.myprog。

```java
Logger logger=Logger.getMyLogger("com.mycompany.myprog");
```

为了方便起见,**你可能希望为有大量日志活动的类增加静态字段**:

```java
private static final Logger logger=Logger.getLogger("com.mycompany.myprog");
```

2.默认的日志设置会把级别等于或高于INFO的所有消息记录到控制台。当然，用户可以更改这个设置,更改配置有些过于麻烦。所以,你应当在你的应用中安装更合适的配置。

下面代码实现了将所有的日志消息放到一个特定的文件中。

```java
if(System.getProperty("java.util.loggging.config.class")==null&&System.getProperty("java.util.logging.config.file")==null){
    try{
    logger.getLogger("").setLevel(Level.All);
    final int 	LOG_ROTATION_COUNT=10;
    FileHandler handler=new FileHandler("%h/myapp.log",0,LOG_ROTATION_COUNT);
    Logger.getHandler("").addHandler(handler);
    }catch(IOException e){
        logger.log(LEVEL.SEVERE,"Can't create log file handler",e);
    }
}
```

3.需要牢记:所有级别为INFO、WARNING和SEVERE的消息都将显示在控制台上。因此,最好只对程序用户有意义的消息设置为这几个级别。

想要调用System.out.println时,可以替换为发出以下的日志消息:

```java
logger.fine("File open dialog canceled");
```

也可以用来记录意料之外的异常:

```java
try{
    ...
}catch(SomeException e){
    logger.log(Level.FINE,"explanation",e);
}
```

### 常用API

#### java.util.logging.Logger

##### Logger getLogger(String loggerName)

##### Logger getLogger(String loggerName,String bundleName)

获得给定名字的日志记录器。如果这个日志记录器不存在,则创建一个日志记录器。本地化消息位于名为bundleName的资源包中。

##### void exiting(String className,String methodName,Object result)

记录一个描述进入/退出方法(有给定的参数和返回值)的日志记录。

##### void throwing(String className,String methodName,Throwable t)

记录一个描述抛出给定异常对象的日志记录。

##### void log(Level level,String message,Throwable t)

记录一个有给定级别和消息的日志记录,其中可以包括对象或者可抛出对象。要包括对象,消息中必须包含格式化占位符{0}、{1}等。

void logp(Level level,String className,String methodName,String message,Object[] objs)

##### void logp(Level level,String className,String methodName,String message,Throwable t)

记录一个有给定级别、准确的调用者信息和消息的日志记录,其中可以包括对象或可抛出对象。

void logrb(Level level,String className,String methodName,ResourceBundle bundle,String message,Object params)

##### void logrb(Level level,String className,String methodName,ResourceBundle bundle,String message,Throwable thrown)

记录一个有给定级别、准确的调用者信息、资源包和消息的日志记录,其中可以包括对象或可抛出对象。

##### void setLevel(Level l)

获得和设置这个日志记录器的级别。

##### void setParent(Logger l)

获得和设置这个日志记录器的父日志记录器。

##### Handler[] getHandlers()

获得这个日志记录器的所有处理器。

void addHandler(Handler h)

##### void removeHandler(Handler h)

增加/删除这个日志记录其中的一个处理器。

boolean getUseParentHandlers()

##### void setUseParentHandlers(boolean b)

获得和设置“使用父处理器”属性。如果这个属性是true，日志记录器会将全部的日志记录转发给他的父处理器。

void getFilter()

##### void setFilter(Filter f)

#### java.util.logging.Handler

##### abstract void publish(LogRecord record)

将日志记录发送到希望的目的地。

##### abstract void flush()

刷新输出所有已缓冲的数据。

##### abstract void close()

刷新输出所有的已缓冲数据,并释放所有相关的资源。

Filter getFilter()

##### void setFilter(Filter f)

获得和设置这个处理器的过滤器。

Formatter getFormatter()

##### void setFormatter(Formatter f)

获得和设置这个处理器的格式化器。

void getLevel()

##### void setLevel(Level l)

获得和设置处理器级别。

#### java.logging.ConsoleHandler

##### ConsoleHandler()

构造一个新的控制台处理器。

#### java.util.logging.FileHandler

##### FileHandler(String pattern,long limit,int count,boolean append)

构造一个文件处理器。limit是日志文件可以包含的最大字节数,count是循环文件的数量。

#### Java.util.logging.LogRecord

##### Level getLevel()

获得这个日志记录的日志级别。

##### String getResourceName()

获得正在记录这个日志记录的日志记录器的名字。

ResourceBundle getResourceBundle()

##### String getResourceBundleName()

获得用于本地化消息的资源或资源包名。

##### String getMessage()

获得本地化或格式化之前的"原始"消息。

##### Object[] getParameters()

获得对象参数。

##### Throwable getThrown()

获得所抛出的对象。

String getResourceClassName()

##### String getResourceMethodName()

获得记录这个日志记录的代码区域。**这个信息有可能是由日志记录代码提供的,也有可能是自动从运行时堆栈推测出来的**。

##### long getMillis()

获得创建时间。

##### Instant getInstant()

获得创建时间,作为java.time.Instant返回。

##### long getSequenceNumber()

获得这个日志记录的唯一序号。

##### int getThreadID()

获得创建这个日志记录的线程的唯一ID。这些ID是由LogRecord类分配的,与其他线程的ID无关。

#### java.util.logging.LogManager

##### static LogManager getLogManager()

获得全局LogManager实例。

void readConfiguration()

##### void readConfiguration(InputStream in)

从系统属性java.util.logging.config.file指定的文件或者给定的输入流读取日志配置。

void updateConfiguration(InputStream in,Function\<String,BiFunction\<String,String,String>> mapper)

##### void updateConfiguration(Function\<String,BiFuntion\<String,String,String>> mapper)

将日志配置与系统属性java.util.logging.configl.file指定的文件或给定的输入流合并。

#### java.util.logging.Filter

##### boolean isLoggable(LogRecord record)

如果给定日志记录需要记录,则返回true。

#### java.util.logging.Formatter

##### abstract String format(LogRecord record)

返回格式化给定日志记录后得到的字符串。

## 调试技巧

1.用下面的方法打印或记录任意变量的值。

System.out.println("x="+x);

或

Logger.getGlobal().info("x="+x);

想要获得对象的状态可以打印this

Logger.getGlobal().info("this="+this);

2.可以在每个类中放置一个单独的main方法。

对各个文件启用Java虚拟机来运行测试。在运行applet时,这些main方法不会被调用,而运行应用程序时,Java虚拟机只调用启动类的main方法。

3.使用Junit框架,常见的是就是@Test。

4.**日志代理**(logging proxy)是一个子类的对象,他可以截获方法调用,记录日志,然后调用超类中的方法。

例如,使用匿名内部类

```java
var generater=new Random(){
    public double nextDouble(){
        double result=super.nextDouble();
        Logger.getGlobal().info("nextDouble"+result);
        return result;
    }
}
```

5.利用Throwable类的printStack方法,可以从任意的异常对象获得堆栈轨迹。

下面的代码将捕获任意异常,打印这个异常对象的堆栈轨迹(非常常用的手段)

```java
try{
	...
}catch(Throwable t){
	t.printStackTrace();
	throw t;
}
```

并不是非得捕获异常才能生成堆栈轨迹。**只要在代码的某个位置插入下面这条语句就可以获得堆栈轨迹**。

6.一般来说,堆栈轨迹显示在System.err上。如果想要记录或显示堆栈轨迹,可以如下将它捕获到一个字符串中:

```java
var out=new StringWriter();
new Throwable().printStackTrace(new PrintWriter(out));
String description=out.toString();
```

7.通常,将程序错误记入一个文件会很有用。不过,错误会发送到System.err，而不是System.out。因此,不能通过运行下面的命令来获取:

```java
java MyProgram > errors.txt
```

而应当如下捕获错误流:

```
java MyProgram 2> errors.txt
```

要想在同一个文件中同时捕获System.err和System.out,需要使用一下命令

```
java MyProgram 1>errors.txt 2>&1
```

8.在System.err中显示未捕获的异常的堆栈轨迹并不是一个理想的方法。更好的方法是将这些消息记录到一个文件中。可以用静态方法Thread.setDefaultUncaughtExceptionHandler改变未捕获的异常。

```java
Thread.setDefaultUncaughtExceptionHandler(
    new Thread.UncaughtExceptionHandler(){
      public void uncaughtException(Thread t,Throwable t){
          save information in log file;
      }
    }
);
```

9.想要观察类的加载过程,启动Java虚拟机时可以使用-verbose标志。

10.java -Xlint sourceFiles

11.Java虚拟机增加了对Java应用程序的**监控**(monitoring)和**管理**(management)支持,允许在虚拟机中安装代理来跟踪内存消耗、线程使用、类加载等情况。

12.Java任务控制器(Java Mission Control)也很专业。

