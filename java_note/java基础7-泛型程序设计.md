在没有泛型之前,必须使用Object来编写适用多种类型的代码。泛型类和泛型方法有**类型参数**,使得它们可以准确描述**特定类型实例化**时会发生什么。  
# 为什么要使用泛型程序设计
**泛型程序设计(generic programming)**意味着编写的代码可以被多种不同的类型的对象重用。例如,收集File类型和String类型对象就可以设计为ArrayList\<T>。  
## 1.类型参数的好处
在没有泛型之前,实现ArrayList的时候需要在内部**维护**一个Object类型  
引用的数组,这种设计很明显是利用了任何类都继承了**Object**类。 
```java
public class ArrayList{
  private Object[] elementData;
  ...
  public Object get(int i){
      ...
  }
    
  public void add(Object o){
      ...
  }
}
```
这种方式存在两个问题:  
1.当获取一个值时必须进行强制类型转换:  
```java
String filename=(String)list.get(0);
```

2.没有错误检查,可以向数组列表中添加任意类型的对象:

```java
files.add(new File("..."));
```

但是,如果将get的结果进行强制类型转换为String类型,就会产生一个错误。

泛型提供了一个更好的解决方案:**类型参数(type paramater)**。ArrayList类有一个类型用来**指示元素**的类型:

```java
ArrayList<String> files=new ArrayList<String>();
```

java9中对于匿名子类可以使用菱形语法:

```java
ArrayList<String> passwords=new ArrayList<>(){
 	public String get(int n){
        return super.get(n).replaceAll(".","*");
    }   
}
```

编译器也会利用这个类型信息,调用get的时候,**不需要进行强制类型转换**。**编译器**知道返回的类型为String,而不是Object：

```java
String filename=files.get(0);
```

**编译器还会进行检查,防止你插入错误类型的对象**,例如,以下语句:

```java
files.add(new File("..."));//can only add Sting objects to an ArrayList<String>
```

上面的错误是无法通过编译的。不过,出现**编译错误要比运行时出现类的强制类型转换异常好得多**。

## 2.泛型程序员

使用泛型类很简单(比较经典的是像ArrayList这样的集合),但是混合使用不同的类型时或者要对类型参数一无所知的遗留代码交互时,你可能看到令人困惑的错误消息,这个时候你需要对Java泛型有足够的了解,才能系统地解决问题。

应用程序员很可能不会编写太多的泛型代码,因为JDK的编写人员已经尽了自己最大的努力。学习这些知识可以更好地帮助我们来排查错误。

# 定义简单泛型类

泛型类就是含多个**类型变量**的类。下面是示例代码

```java
public class Pair<T>{
    private T first;
    private T second;
    
    public Pair(){
        fisrt=null;
        second=null;
    }
    
    public Pair(T first,T second){
        this.first=first;
        this.second=second;
    }
    
    public T getFirst(){
        return first;
    }
    
    public T getSecond(){
        return second;
    }
}
```

引入了一个类型变量T,用<>括起来。泛型类可以有多个类型变量:

```java
public class Pair<T,U>{
	...
}
```

其中第一个字段和第二个字段使用不同的类型。

类型变量在整个类中用于**指定方法的返回类型以及字段和局部变量的类型**。例如:

```java
private T first;

private T getFirst(){...}
```

可以用具体的类型替换类型变量来**实例化(instance)**泛型类型。

Pair\<String>

上面所有的T都将被**编译器替换为String(有些工作是需要编译器来完成的,而有些工作在虚拟机中执行)**,换句话说泛型类相当于**普通类的工厂(用于生产普通类)**。

```java
String first=(String)pair.getFirst();
```



# 泛型方法

定义一个带有**类型参数**的方法:

```java
class ArrayAlg{
    public static <T> T getMoiddle(){
        return a[a.length/2];
    }
}
```

**这个方法是定义在普通类中的,而不是在泛型类中**。但是,也可以定义在泛型类中。

```java
String middle=ArrayAlg.<String>getMiddle("John","Q.","Public");
```

在这种情况下(实际情况下),方法调用可以省略\<String>类型参数。编译器有足够的信息推断出你想要的方法。它将参数的类型与泛型类T进行匹配,推断出T一定是String,也就是说上面的代码可以简化为下面的形式。

```java
String middle=ArrayAlg.getMiddle("John","Q.","Public");
```

几乎在所有情况下,泛型方法的类型都能正常的工作。但是偶尔也会出现错误:

```java
double middle=ArrayAlg.getMiddle(3.14,1729,0);
```

错误消息将以晦涩的方式指出:解释这个代码有两种方式,且都是合法的。简单的说,编译器将参数自动装箱为1个Double类型对象和2个Integer对象,然后寻找共同的超类型。有两个,一个是Number,一个是Comparable接口。然后,编译器不知道选取哪种类型。解决方法是将所有的参数都写为double值。

# 类型变量的限定

有时,类或方法需要对类型变量加以约束。下面的代码用来计算数组中的最小元素:

```java
class ArrayAlg{
    public static <T> T min(T[] a){
        if(a==null || a.length==0){
            return null;
        }
        
        T smallest=a[0];
        
        for(int i=1;i<a.length;i++){
            if(samllest.compareTo(a[i]>0)){
                smallest=a[i];
            }
        }
        
        return smallest;
    }
}
```

上面的代码中samllest的类似为T,它可以是任何一个类的对象。

解决这个问题的办法是限制T只能是实现了Comparable接口(包含一个方法compareTo的标准接口)的类。可以通过对类型变量设置一个**限定(bound)**来实现这一点。

```java
public static <T extends Comprable> T min(T[] a)...
```

现在,泛型方法min只能在实现Comprable接口的类的数组上调用。

使用extends是它更接近子类型的概念,限定类型可以是类,也可以是接口。

一个类型变量或通配符可有多个限定,例如:

T extends Comparable & Serializable

限定类型用"&"分隔,而逗号用来分隔类型变量。

Java继承体系中,可以根据需要拥有多个接口超类型,但**最多有一个限定可以是类**，而且必须是第一个。

# 泛型代码和虚拟机

**虚拟机中没有泛型类型对象-所有对象都属于普通类**。在泛型实现的早期版本中,甚至能够将使用泛型的程序编译为在1.0虚拟机上运行的类文件。

## 类型擦除

​	无论何时定义一个泛型类型,都会自动提供一个相应的**原始类型(raw type)**。这个原始类型的名字就是去掉类型参数后的泛型类型名。类型变量会被**擦除(erased)**,并替换为其**限定类型**(或者,对于无限定的变量则替换为Object)。

```java
public class Pair{
    private Object first;
    private Object second;
    
    public Pair(Object first,Object second){
        this.first=first;
        this.second=second;
    }
    
    public Object getFirst(){
        return first;
    }
    
    public Object getSecond(){
        return second;
    }
    
    public void setFirst(Object newValue){
        first=newValue;
    }
    
    public void setSecond(Object newValue){
        second=newValue;
    }
}
```

​	**因为T是一个无限定的变量**,所以直接用Object替换。结果是一个普通的类,就和引入泛型之前实现的类一样。

​	在程序中可以包含不同类型的Pair,例如,Pair\<String>或Pair\<LocalDate>。**类型擦除后,它们都会变成原始的类型**。

​	原始类型用**第一个限定**来替换类型变量,或者,如果没有给定限定,就替换为Object。例如,类Pair\<T>中的类型变量没有显式的限定,因此,原始类型用Object替换T。

```java
public class Interval<T extends CXomparable & Serializable> implements Serializable{
    private T lower;
    private T upper;
    
    public Interval(T first,T second){
        if(first.compareTo(second)<=0){
            lower=first;
            upper=second;
        }else{
            lower=second;
            upper=first;
        }
    }
}
```

原始类型Interval如下所示:

```java
public static Interval implements Serializable{
    private Comparable lower;
    private Comparable upper;
    ...
   	public Interval(Comparable first,Comparable second){
        ...
    }
}
```

如果限定时将Serializable接口放在前面,那么原始类型就会用Serializable接口替换T,而编译器在必要时要向Comparable插入强制类型转换。为了提高效率,应该将**标签接口(即没有方法的接口)**放在限定列表的末尾。

## 转换泛型表达式

编写一个泛型方法调用时,如果擦除了返回类型,编译器会插入强制类型转换。例如:

```java
Pair<Employee> buddies=...;
Employee first=buddies.getFirst();
```

**getFirst擦除之后的返回类型是Object。编译器自动插入转换到Employee的强制类型转换**。也就是说,编译器把这个方法调用转换为两条虚拟机指令:

1.对原始方法Pair.getFirst的调用;

2.将返回的Object类型强制转换为Employee类型。

当访问一个泛型字段时也要插入强制类型转换。例如

```java
Employee buddy=buddies.getFirst();
```

也会在结果字节码中插入强制类型转换。

## 转换泛型方法

类型擦除也会出现在泛型方法中。

```java
public static <T extends Comparable> T min(T[] a){
    ...
}
```

是整个一组方法,而擦除类型之后,只剩下一个方法:

```java
public static Comparable min(Comparable[] a)
```

方法的擦除带来了两个复杂的问题,先来看下面的代码:

```java
class DateInterval extends Pair<LocalDate>{
    public void setSecond(LocalDate second){
        if(second.compareTo(getFirst())>=0){
            super.setSecond;
        }
        ...
    }
}
```

1.日期区间是一对LocalDate对象,而且我们需要覆盖这个方法来确保第二个值永远不小于第一个值。

类型擦除之后应该如下:

```java
class DataInterval extends Pair{
	public void setSecond(LocalDate second){
        ...
    }
}
```

但是还有另一个从Pair继承的setSecond方法,即

```java
public void setSecond((Object second)
```

2.很明显,不是同一个方法,因为它有一个类型不同的参数-Object,而不是LocalDate。不过,不应该不一样:

````java
var interval=new DateInterval(...);
Pair<LocalDate> pair=interval;
pair.setSecond(aDate);
````

上面的代码中,由于pair引用了一个DateInterval对象,我们希望setSecond调用具有多态性去调用最合适的方法,所以应该调用的是DateInterval.setSecond方法。

为了解决这个问题,**编译器在DateInterval类中**生成了一个**桥方法**(bridge method)：

```java
public void setSecond(Object second){
    setSecond(setSecond(LocalDate) second);
}
```

对pair.setSecond(aDate)进行跟踪:

变量pair已经声明为类型Pair\<LocalDate>,类中只有一个setSecond方法即setSecond(Object object)。虚拟机子在pair引用的对象上调用这个方法。这个对象是**DateInterval类型**,因而将会调用DateInterval.setSecond(Object)方法。这个方法是合成的桥方法,它会调用DateInterval。setSecond(LocalDate),正是我们需要的。

假设DateInterval类也覆盖了getSecond方法:

```java
class DateInterval extends Pair<LocalDate>{
    public LocalDate getSecond(){
        return (LocalDate)super.getSecond();
    }
}
```

那么在DateInterval类中,有两个getSecond方法:

```java
LocalDate getSecond(); //defined in DateInterval
Object getSecond(); //overrides the method defined in Piar to call the first method
```

java中不允许两个方法有相同的类型参数。但是,**会由参数类型和返回类型共同指定一个方法**。因此,编译器会为两个仅返回类型不同的方法生成字节码,虚拟机能够正确地处理这种情况。

对于Java泛型的转换,需要记住以下几点:

1.虚拟机中没有泛型,只有普通的类和方法;

2.所有的类型参数都会替换为它们的限定类型;

3.会合成桥方法来保持多态;

4.为保持类型安全性,必要时会插入强制类型转换。

## 调用遗留代码

可以使用**注解**(annotation)来让警告消失,如下:

```java
@SuppressWarnings("uncheked")
Dictionary<Integer,Components> labelTable=slider.getLabelTable();
```

# 限制与局限性

大多数限制都是由类型擦除引起的。

## 1.不能用基本类型

例如,没有Pair\<double>,因为擦除之后类型变量变为Oject类型,而Object不能存储double值。

## 2.运行时类型查询只适用于原始类型

**虚拟机中的对象总有一个特定的非泛型类型**。因此,所有的类型查询只产生原始类型。例如,

if(a instanceof Pair\<String>) //ERROR

if(a instanceof Pair\<T>) //ERROR

或者强制类型转换:

Pair\<String> p=(Pair\<String>) a;//warning-can only test that a is a Pair;

同样的道理,getClass方法总是返回原始类型。例如:

```java
Pair<String> stringPair=...;
Pair<Employee> employeePair=...;
if(stringPair.getClass()==employeePair.getClass())
```

返回的结果是true,两次调用的结果都返回Pair.clss。

## 3.不能创建参数类型的数组

不能实例化参数化类型的数组,例如:

```java
var table=new Pair<Sting>[10]; //ERROR
```

这有什么问题呢?擦除之后，table的类型是Pair[],可以将其转换为Object[]：

```
Object[] objarray=table;
```

数组会记住元素类型,如果视图存储其他类型的元素,就会抛出一个ArrayStoreException异常。

objarray[0]="Hello"; //ERROR

## 4.Varargs警告

场景:向参数可变的方法传递一个泛型类型的实例。

```java
public static <T> void addAll(Collection<T> coll,T...ts){
    for(T t:ts){
        coll.add(t);
    }
}
```

**实际上参数ts是一个数组,包含提供的所有实参**。

考虑以下调用:

```java
Collection<Pair<String>> table=...;
Pair<String> pair1=...;
Pair<String> pair2=...;
addAll(table,pair1,pair2);
```

为了调用这个方法,Java虚拟机必须建立一个Pair\<String>数组,这就违反了前面的规则。

## 5.不能实例化类型变量

不能使用类似new T(...)的表达式。

类型擦除变为Object,我们不需要Object。

Java8之后,最好的解决办法是让调用者提供一个构造器表达式。例如:

```java
Pair<String> p=Pair.makePair(String::new);
```

makePair方法接收一个**Supplier\<T>，这是一个函数式接口**,表示一个无参数而且返回类型为T的函数:

```java
public static <T> Pair<T> makePair(Supplier<T> constr){
    return new Pair<>(constr.get(),constr.get());
}
```

传统的方式通过反射调用Constructor.newInstance方法来构造泛型对象:

first=T.class.getConstructor().newInstance(); //ERROR

表达式T.class是不合法的,擦除之后会变为Object.class。

我们自己来编写API

```java
public static <T> Pair<T> makePair(Class<T> cl){
    try{
        return new Pair<>(cl.getConstructor().newInstance(),
        cl.getConstructor().newInstance());
    }catch(Exception e){
        return null;
    }
}
```

如下调用:

```java
Pair<String> p=Pair.makePair(String.class);
```

String.class是一个Class\<String>的实例(事实上,它是唯一的实例)。因此,makePair方法能够推断出所建立的对组(pair)的实例。

## 6.不能构造泛型数组

擦除之后可能在类型转换出现ClassCastException。

## 7.泛型类的静态上下文中类型变量无效

不能在静态字段或方法中引用类型变量。

## 8.不能抛出或捕获泛型类的实例

既不能拼抛出也不能捕获泛型类的对象,泛型类扩展Throwable甚至都是不合法的。

## 9.可以取消对检查型异常的检查



## 10.注意擦除后的冲突

这里需要注意与Object类的方法发生冲突。

# 泛型类型的继承规则

下面的代码将不能编译:

```java
Manager[] topHonchos=...;
Pair<Employee> result=ArrayAlg.minmax(topHonchos);
```

minmax方法返回Pair\<Manager>而不是Pair\<Employee>,并且这样的赋值是不合法的。

通过下面这个图可以直观的看出继承规则:

![](https://gitee.com/aryangzhu/picture/raw/master/java/%E6%B3%9B%E5%9E%8B%E7%B1%BB%E7%9A%84%E7%BB%A7%E6%89%BF%E8%A7%84%E5%88%99.jpg)

注:上面的就是泛型类型与Java数组之间的一个重要区别。**可以将一个Manager[]数组赋给一个类型为Employee[]的变量**:

```java
Manager[] managerBuddies={ceo,cfo};
Employee[] employeeBuddies=managerBuddies;
```

如果试图将一个低级别的员工存储到employeeBuddies[0],虚拟机将会抛出ArrayStoreException异常。

总是可以将**参数类型(泛型类型)转换为一个原始类型**。例如,Pair\<Employee>是原始类型Pair的子类型。但是**转换成原始类型会产生错误**。

```java
Pair<Manager> managerBuddies=new Pair<Manager>(ceo,cfo);
Pair rawBuddies=managerBuddies;
rawBuddies.setFirst(new File("...")); //only a complie-time warning
```

![](https://gitee.com/aryangzhu/picture/raw/master/java/%E6%B3%9B%E5%9E%8B%E5%88%97%E8%A1%A8%E7%B1%BB%E5%9E%8B%E4%B8%AD%E5%AD%90%E7%B1%BB%E5%9E%8B%E7%9A%84%E5%85%B3%E7%B3%BB.jpg)

**泛型类可以扩展或者实现其他的泛型类**。例如,ArrayList\<T>类实现了List接口。这意味着,一个ArrayList\<Manager>可以转换为一个List\<Manager>。

# 通配符

## 通配符概念

通配符类型中,允许参数发生变化。例如,通配符类型

```java
Pair<? extends Employee>
```

表示**任何(这里的任何指的是一个集合)泛型Pair类型**。它的类型参数是Employee的子类,如Pair\<Manager>。

来看下面的代码:

```java
public static void printBuddies(Pair<Employee> p){
    Employee first=p.getFirst();
    Employee second=p.getSecond();
    System.out.println(first.getName()+"and "+second.getName()+"are buddies");
}
```

我们知道不能将Pair\<Manager>传递给这个方法,但是我们可以解决这个问题:

```java
public static void printBuddies(Pair<? extends Empooyee> p)
```

类型Pair\<Manager>是Pair\<? extends Employee> p)的子类型。

来看下面的代码:

```java
Pair<Manager> managerBuddies=new Pair<Manager>(ceo,cfo);
Pair<? extends Employee> wildcardBuddies=managerBuddies;
wildcardBuddies.setFirst(lowEmployee); //complie-time error
```

我们担心的是破坏掉Pair\<Manager>的引用,但是显然不会,原因是:

```java
? extends Employee getFirst()
void setFirst(? extends Employee)
```

编译器知道？是Employee的子类型,但是不知道具体是什么类型,它就**拒绝传递任何特定的类型**。？不能匹配。

使用getFirst就不存在这个问题:将getFirst的返回值赋给一个Employee引用完全是合法的。

**这就是引入有限定的通配符的关键之处**。到这里,已经有了安全的访问器方法和不安全的更改器方法。

## 通配符的超类限定

通配符限定与类型变量限定十分类似,但是,还有一个附加的能力,即可以指定一个**类型限定**(supertype bound),如下:

```java
? super Manager
```

这个通配符限制为Manager的所有超类型。

**直观来讲,带有超类型限定的通配符允许你写入一个泛型对象,而带有子类型限定的通配符允许你读取一个泛型对象**。

第一个经典示例:我们来求一个经理数组中的最大奖金和最小奖金,很明显,Manager可以,Employee也可以:

```java
public static void minmax(Manager[] a,Pair<? super Manager> result){//第二个参数代表Manager或者Manager的超类Employee
    if(a.length==0){
        return;
    }
    Manager min=a[0];
    Manafer max=a[0];
    ...
}
```



来看一个应用:Comparable接口本身就是一个泛型类型。声明如下:

```java
public interface Comparable<T>{
    public int compareTo(T other);
}
```

String实现了Comparable\<String>,它的compareTo方法声明为:

```java
public int compareTo(String other)
```

由于Comparable是一个泛型类型,也许可以把ArrayAlg类的min方法做得更好:

```java
public static <T extends Comparable<T>> T min(T[] a)
```

使用minmax方法处理一个LocalDate对象的数组时,我们会遇到一个问题。LocalDate实现了ChronoLocalDate,而ChronoLocalDate扩展了Comparble\<ChronoLocalDate>,因此LocalDate实现的是Comparable\<ChronoLocalDate>而不是Comparable\<LocalDate>.

在这种情况下,可以利用超类型来解决:

```java
public static <T extends Comparable<? super T>> T min(T[] a)...
```

compraeTo方法写成

```java
int compareTo(? super T)
```

他可以声明使用类型T的对象,或者也可以是使用T的一个超类型的对象(当T是LocalDate时)。

作为库程序员,你有必要慢下来仔细理解其中的逻辑,但是工作中只需要了解即可。

## 无限定通配符

形如Pair<?>

? getFirst()

void setFirst(?)

getFirst的返回值只能赋给一个**Object**。setFirst方法不能被调用,**甚至不能用Object**调用。Pair<?>和Pair本质的不同在于:**可以用任意Object对象调用原始Piar类的setFirst方法**。

## 通配符捕获

我们来看一个例子,用一个方法来交换对组的元素:

```java
public static void swap(Pair<T> p)
```

**通配符不是类型变量**,因此,不能在编写代码中使用"?"作为一种类型。也就是说,下面的代码是非法的:

```java
? t=p.getFirst(); //ERROR
p.setFirst(p.getSecond);
p.setSecond(t);
```

为了解决这个问题,我们可以编写一个辅助方法swapHelper,如下所示:

```java
public static <T> void swap(Piar<T> p){
    T t=p.getFirst();
    p.setFirst(p.getSecond());
    p.setSecond(t);
}
```

注意:swapHelper是一个泛型方法,而swap不是,它有一个固定的Pair<?>类型的参数。

swapHelper方法的参数T**捕获通配符**。它不知道通配符指示哪种类型,但是,这是**一个明确的类型**,并且从\<T>swapHelper的定义可以清楚地看到T指示这个类型。

通配符捕获只有非常限定的情况下才是合法的。编译器必须能够保证通配符表示单个确定的类型。例如,ArrayList\<Pair\<T>>中的T永远不能捕获ArrayList\<Pair\<?>>中的通配符。数组列表可以保存两个Pair\<?>,其中的？分别具有不同的类型。

#　反射和泛型

反射允许你在运行时分析任意对象。

## 泛型Class类

现在,Class类是泛型的。例如,**String.class实际上是一个Class\<String>类的对象**(事实上,这是唯一的对象)。

类型参数十分有用,这是因为它**允许Class\<T>方法的返回类型更加具有特定性**。

以下方法就使用了类型参数:

```java
T new Instance()
T cast(Object obj)
T[] getEnumConstants()
Class <? super T> getSuperClass()
Constructor<T> getConstructor(Class...parameterTypes)
Constructor<T> getDeclareConstructor(Class...paramaterTypes)
```

newInstance方法返回这个类的一个实例,由无参数构造器获得。**它的返回类型现在被声明为T,其类型与Class\<T>描述的类相同**,这样就避免了类型转换。

如果给定对象的类型实际上是T的一个子类型,cast方法就会返回这个给定对象(现在声明为类型T),否则,会抛出一个BadCastException异常。

getConstructor和getdeclaredConstructor方法返回一个Constructor\<T>对象。Constructor类也已经变成泛型,从而使newInstance方法有一个正确的返回类型。

## 常用API

### java.lang.Class\<T>

#### T newInstance

返回无参数构造器构造的一个新实例。

#### T cast(Object obj)

如果obj为null或有可能转换为类型T,则返回obj;否则抛出一个BadCastException异常。

#### T[] getEnumConstants()

如果T是枚举类型,则返回所有数组值组成的数组,范泽返回null。

#### Class\<? super T> getSuperClass()

返回这个类的超类。如果T是一个类或Object类,则返回null。

#### Constructor\<T> getConstructor(Class paramaterTypes)

#### Constructor\<T> getDeclaredConstructor(Class paramaterTypes)

获得公共构造器,或者有给定参数类型的构造器。

### java.lang.reflect.Constructor\<T>

#### T newInstance(Object...paramaters)

返回用指定参数构造的新实例。

## 使用Class\<T>参数进行类型匹配

匹配泛型方法中Class\<T>参数的类型变量有时会很有用。

```java
public static <T> Pair<T> makePair(Class<T> c) throws InstantiationException,IllegalAccessException{
    return new Pair<>(c.newInstance(),c.newInstance());
}
```

如果调用

```java
makePair(Employee.class)
```

Employee.class将是一个Class\<Employee>类型的对象。makePair方法的类型参数T同Employee匹配,编译器可以推断出这个方法返回一个Pair\<Employee>。

## 虚拟机中的泛型类型参数

Java泛型的突出特性之一就是在**虚拟机中擦除泛型类型**。但是,擦除的类好像仍然会保存原先泛型的微弱记忆。例如,原始的Pair类知道它源于泛型类Pair\<T>,尽管一个Pair类型的对象无法区分它是构造为Pair\<String>还是Pair\<Employee>.

类似的,考虑以下方法:

```java
public static Comparable min(Comparable[] a)
```

这是擦除以下方法得到的:

```java
public static <T extends Comparable<? super T>> T min(T[] a)
```

可以使用反射API来确定与类型声明有关的参数,**java.lang.reflect**包中的接口**Type**。这个接口包含了以下子类型:

1.Class类,描述**具体类型**。

2.TypeVariable接口,描述**变量类型**(如T extends Comparable<? super T>)。

3.WildcardType接口,描述**通配符**(如? super T)。

4.ParameterType接口,描述**泛型类或接口类型**(如Comparable <? super T>)。

5.GenericArrayType接口,描述**泛型数组**(如T[])。

```java
public class GenericReflectionTest{
    public static void main(String[] args){
        String name;
        try(Scanner in=new Scanner(System.in)){
            ...
            name=in.next();
            ...
        }
        
        try{
            Class<?> cl=Class.forName(name);
            printClass(cl);
            for(Method m:cl.getDeclareMethods()){
                printMethod(m);
            }
        }catch(ClassNotFoundException e){
            e.printStackTrace();
        }
    }
    
    public static void printClass(Class<?> cl){
        ...
       	Type sc=cl.getGenericSuperClass();
        if(sc!=null){
            ...
        }
        ...
    }
    
    public static void printMethod(Method m){
        String name=m.getName();
        ...
        
    }
    
    public static void printTypes(Type[] types,String pre,String sep,String suf,boolean isDefinition){
     	if(pre.equals("extends")&&Arrays.equals(types,new Type[]{Object.class})){
            return;
        }
        
      	if(types.length>0){
            ...
        }
        for(int i=0;i<types.length;i++){
            if(i>0){
                ...
            }
            printTypes(types[i],isDefinition);
        }
        if(types.length>0){
           ...
        }
       	
    }
    
    public static void printType(Type type,boolean isDefinition){
        
    }
}
```

## 类型字面量

先来看例子,在一种持久存储机制中,你可能希望用户指定一种方法来保存某个特定类的对象。但是,既然ArrayList\<Integer>和ArrayList\<Double>都会被擦除为同一个原始类类型ArrayList,如何让它们具有不同的动作呢?

我们可以将泛型类与Type接口进行结合。如下:

```java
var type=new TypeLiteral<ArrayList<Integer>>(){};
```

TypeLiteral构造器会捕获泛型超类型:

```java
class TypeLiteral{
    public TypeLiteral(){
        Type parentType=getClass().getGenericSuperClass();
        if(parentType instancof ParameterizedType){
            type=(ParameterizedType)parentType.getActualTypeArguments()[0];
        }
        else{
            throw new UnsupportedOperationException("Construct as new TypeLiteral<...>(){}");
        }
    }
    ...
}
```

如果运行时有一个泛型类型,就可以将它与TypeLiteral匹配。我们无法从一个对象得到泛型类型(已经被擦除)。但是,**字段和方法参数的泛型类型还留在虚拟机中**。

**CDI和Guice等注入框架(Injection framework)**就是使用类型字面量来控制泛型类型的注入。

### 常用API

#### java.lang.Class\<T>

##### TypeVarialbe[] getTypeParameters()

如果这个类型被声明为泛型类型,则获得泛型类型变量,否则获得一个长度为0的数组。

##### Type getGenericSuperClass()

获得这个类型所声明超类的泛型类型;如果这个类型是Object或者不是类类型(class type),则返回null。

##### Type[] getGenericInterfaces()

获得这个类型所声明接口的泛型类型(按照声明的次序),否则,如果这个类型没有实现接口,则返回长度为0的数组。

#### java.lang.reflect.Method

##### TypeVariable[] getTypeParameters

如果这个方法被声明为一个泛型方法,则获得泛型类型变量,否则返回长度为0数组。

##### Type getGenericReturnType()

获得这个方法声明的泛型返回类型。

##### Type[] getGenericParameterTypes()

获得这个方法声明的泛型参数类型。如果这个方法没有参数,返回长度为0的数组。

#### java.lang.reflect.TypeVariable

##### String getName()

获得这个类型变量的名字。

##### Type[] getBounds()

获得这个类型变量的子类限定,否则,如果该变量无限定,则返回长度为0的数组。

#### java.lang.reflect.WildcardType

##### Type[] getUpperBounds()

获得这个变量的子类(extends)限定,否则,如果没有子类限定,则返回长度为0的数组。

##### Type[] getLowerBounds()

获得这个类的超类限定(super),如果没有则返回长度为0的数组。

#### java.lang.reflect.ParameterizedType

##### Type getRawType()

获得这个参数话类型的原始类型。

##### Type[] getActualTypeArguments()

获得这个参数话类型声明的类型参数。

##### Type getOwnerType()

如果是内部类型,则返回其外部类类型;如果是一个顶级类型,则返回null。

Type getOwnerType()

如果是内部类型,则返回其外部类型;如果是一个顶级类型,则返回null。

#### java.lang.reflect.GenericArrayType

##### Type getGenericComponent()

获得这个数组类型声明的泛型元素类型。
