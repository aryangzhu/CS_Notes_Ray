**基本思想是可以基于已有的类创建新的类，因为这样做的话(继承)就可以复用已创建的类的方法，而且可以增加一些新的方法和字段，使新类能够适应新的情况**
**反射，反射是指在程序运行期间更多地了解类及其属性的能力，反射是一个功能强大的特性，也是成为高阶程序员的必经之路**
#类、超类和子类
Employee只需要领薪水，而Manager完成业绩之后还可以领取奖金，所以我们需要拓展程序功能，但是又要复用原来的类。
关键字**extends**相当于C++的**:**(冒号)

# 定义子类

超类和子类的概念来自于计算机科学与数学理论中集合语言的术语。

```java
public class Manager extends Employee{
	private double bonus;
    ...
    private void setBonus(double bonus){
   		this.bonus=bonus;
   }
}
```
可以看到Manager中有自己独有的方法setBonus()，这就是在基类上进行拓展，但是子类中也可使用getName和getHireDay等方法，类似的，从超类中还继承了name、salary和hireDay这几个字段，再加上的自己的bonus。
## 覆盖方法
超类中的方法不一定适用，需要自己修改
比如，Manager返回薪水和工资的总和

```
public double getSalary(){
	return salary+bonus; //won't work
}
```
这样编写代码是不行的，为什么，因为只有类Employee方法才能直接访问Employee类的私有字段。这意味着子类也不能访问salary字段，如果Manager类的方法想要访问那些私有字段，就要使用公共接口。
```java
public double getSalary(){
	double baseSalary=getSalary(); //still won't work
    return baseSalary+bonus;
}
```
这段代码也存在问题，getSalary()方法，我们想要调用超类中的方法，但是我们自己也有一个这样的方法(而且是正在实现的)，所以就会无限次的调用自己，直到整个程序崩溃
**super关键字**
```java
public double getSalary(){
	double baseSalary=super.getSalary();
    return baseSalary+bonus;
}
```
**重点**
有些人认为super与this引用是类似的概念，实际上，这样并不恰当。因为super不是一个对象的引用，例如，不能将值super赋给另一个对象变量，他只是一个**指示编译器调用超类方法**的特殊关键字。
Java中使用super,而C++中使用::
## 子类构造器
```
public Manager(String name,double salary,int year,int month,itn day){
	super(name,salary,year,month,day);
    bonus=0;
}
```
super构造器必须是子类构造器的第一条语句
如果子类的构造器没有显示地调用超类的构造器，将自动地调用超类的无参构造器，如果超类没有无参构造器，并且在子类构造器中又没有显示地调用超类的其他构造器，Java编译器就会报错
也就是说，子类在初始化时会自动调用超类的构造器。如果我没有记错的话，应该是静态块先初始化，然后才会执行超类的构造函数。
**this关键字的作用**
1.指示隐式参数的引用
2.调用该类的其他构造器
**super关键字的作用**
1.调用超类方法
2.调用超类的构造器
对象变量e可以引用Employee对象，也可以引用Manager对象
**虚拟机知道e实际引用的对象类型，因此能够正确地调用相应的方法**
一个对象变量(例如，变量e)可以指示多种实际类型的现象被称为多态，在**运行时能够自动地选择**适当的方法，称为**动态绑定**
## 继承层次
意思就是继承不只是单层的，类似与树一样，在每一层有多个节点，而在继承体系中，某个特定的的类到其祖先的距离称为继承链。
## 多态
多态是针对对象变量来说的，我们只能说java中某个对象变量是多态的。
我们可以使用"is-a"来设计类是否能够继承父类，例如每个经理其实都是一名员工，反之不成立；另一个方面，“is-a"也有替换的一位，什么意思呢，父类型变量引用可以被子类型变量替换掉。
**不能将超类的引用赋给子类变量。例如,Manager m=staff[i]**,原因很清楚：不是所有员工都是经理。如果赋值成功，m可能引用了一个不是经理的Employee对象，而在后面有可能会调用m.setBonus(...),这就会发生运行时的错误。
**注意：子类引用的数组可以转换成超类引用的数组，而不需要强制类型转换**

```
Manager[] managers=new Manager[10]; //这没什么问题
Employee[] staff=managers;   //超类数组指向子类数组引用,多态的体现
staff[0]=new Employee("Harry Hacker..."); //编译器居然也没有识别出问题，如果这里的引用对象进行了修改，那么原对象变量指向的引用也就发生了改变。
managers[0].setBonus(1000);将会试图调用一个不存在的实例字段，进而会扰乱相邻存储空间的内容，会引发ArraysStoreException异常。
```
## 理解方法调用
假设要调用x.f(args),隐式参数x声明为类C的一个对象。联想到了this关键字。
1.编译器查看对象的声明类型和方法名。有可能存在多个名字为f但参数类型不一样的方法。例如，f(int)和f(String),**编译器将会一一列举类中所有名为f的方法和超类中所有名为f的方法而且可访问的方法(超类的私有方法不可访问)**
如此一来，编译器就已经知道了所有可能被调用的方的候选方法。
2.接下来，编译器要确定方法调用中提供的参数类型。如果在所有名为f的方法中存在一个与所提供的参数类型完全匹配的方法，就选择这个，这个过程称之为**重载解析**。例如，对于调用x.f("Hello"),编译器将会挑选f(String),而不是f(int)。但是呢，f(x)中x的类型还可以进行转换，如果编译器没有找到或者经过类型转化之后方法太多，编译器则会报告一个错误。
tips:方法的名字和参数列表称之为方法的签名。

```java
public Employee getBuddy(){......}
public Manager getBuddy(){......}
返回类型在子类中发生了改变，是超类的子类型，这两个方法有**可协变**的返回类型。
```
3.如果是private方法、static方法和final方法或者构造器，那么编译器将可以准确地知道该调用哪个方法。这称之为**静态绑定**。与此对应的是，如果要调用的的方法依赖于隐式参数的实际类型，那么必须在运行时使用动态绑定。在我们的示例中，编译器会利用动态绑定生成一个调用f(String)的指令。
4.程序运行并且采用**动态绑定**调用方法时，虚拟机必须调用与x所引用对象的实际类型所对应的方法。什么意思呢，就是说，假设D是C的子类，如果调用d.f(x)那么虚拟机首先会在d的类中找f方法，如果找不到才回去c中找f这个方法，如果c中找不到，那么就回去c的超类中找方法。
	每次调用方法都要完成一遍这个搜索，时间开销是比较大的。因此，虚拟机预先为每个类计算了一个**方法表(method table)**,其中列出了所有方法的签名(返回类型+参数列表)和要调用的实际方法。等到真正调用的时候，虚拟机直接查这个表就行了。**如果使用的super.f(param)那么将直接在当前类的超类中去寻找。**

```java
public class Test{
    public static void main(String[] args){
        Employee employee=new Employee("Harry Hacker",5000,1989,10,1);
        employee.getSalary();
    }
}
```

现在我们来看employee.getSalary()方法，Employee中只有一个getSalary()方法，不用担心方法的重载解析

1.首先，**虚拟机获取employee的实际类型的方法表**，可能使用Employee也可能是Manager，也可能是其他子类的方法表(比如Manager类型，就会同时获取)。

2.查找一遍定义了getSalary()签名的类。

3.最后虚拟机进行调用。

动态绑定还有一个重要的特性是：无须对现有的代码进行修改就可以对程序进行扩展。假设增加一个新类Executive,并且变量e引用了这个新类，比如说是getSalary()方法，那么就无须重新编译e.getSalay()的代码，而是直接调用Excutive.getSalary()方法。

## 阻止继承:final类和方法
final修饰的类不能继承，方法不能覆盖。
例如，Calendar的getTime和setTime就不允许别的子类来实现，只有Calendar这个类的设计者来实现，大佬果然是大佬。
String没有子类我们都知道，String类型的变量也不允许指向别的引用。
早期Java程序员甚至会使用final修饰方法来减少开销，因为一个方法很短且不能被覆盖的话那么CPU就能对其进行优化处理，这个过程称为内联。e.getName->e.name,所幸，现在的虚拟机的即时编译器都很强，它能准确的知道类之间的继承关系，如果方法很短且没有被覆盖，俺么即时编译器就会将这个方法进行内联处理。如果有子类的话，那么虚拟机将取消内联。又是虚拟机，可见虚拟机在Java体系中的重要意义。

## 强制类型转换
例如
```
double x=3.405;
int nx=(int) x;
```
简单来说，就是将一个类的对象引用转换为另一个对象引用。要完成对象引用的转换，转换语法与数值表达式的强制类型转换类似，需要用()放在转换对象之前
Manager boss=(Manager)staff[0];
使用强制类型转换的原因是：要在暂时忽视对象的实际类型之后使用对象的全部功能。
代码改写
```java
Manager boss=new Manager(5000);

Employee[] staff=new Employee[3];
staff[0]=boss;
staff[1]=new Employee(500);
```
如果使用强制类型转换
```java
Employee[] staff=new Employee[3];

for(int i=0;i<staff.length;i++){
if(i为经理) staff[i]=new Employee(5000);
else staff[i]=new Employee(500);
}
staff[0]=(Manager)staff[0];
```
我们转换是为了使用Manager下的方法getSalary，不过上面的代码反而脱裤子放屁，更加麻烦。
将一个值存入变量时，编译器会检查你是否承诺过多。
ClassCastException异常
**注意**
1.只能在继承层次内进行强制类型转换。
2.将超类强制转换为子类时建议使用instance of进行检查

```
if(staff[1] instanceof Manager){
boss=(Manager)staff[1];
}
```
## 抽象类
自下而上的看，位于上层的类更具有一般性，可能更加抽象。从某种角度看，祖先类更加具有一般性，人们只将它作为派生其他类的基类，而不是来构造你想使用的实例。
tip:通用的方法应该放在超类中。
抽象方法充当着占位方法的角色，在子类中进行实现：
1.子类中保留抽象方法，但是子类也得为抽象的
2.子类实现抽象方法。
**即使没有抽象方法，类也可以是抽象的，也就是说，能有非抽象方法**
定义抽象类Peson和两个非抽象子类Employee和Student
```
Person person=new Person[2];
person[0]=new Employee(...);
person[1]=new Student(...);

for(...){
	sout(p.getName()+p.getDescription());
}
```
p为什么能调用getDescription呢，这是因为由于不能构造抽象类Person的对象，所以变量p永远不会引用person对象，而是引用诸如Employee或Student这样的具体子类的对象，而这些对象中都定义了getDescription方法。
## 受保护访问
# Object:所有类的超类

## Oject类型变量
在Java中，只有基本类型不是对象，例如数值、字符和布尔类型的值都不是对象。
所有的数组类型，不管是对象数组还是基本类型的数组都扩展了Object类。
## equals方法
Object中实现的equals方法用于确定两个对象引用是否相等。
覆盖equals方法的基本套路
```
public class Employee{
...
@Override
    public boolean equals(Object object){
        if(this==object){
            return true;
        }
        
        if(object==null){
            return false;
        }
        
        if(getClass()!=object.getClass()){
            return false;
        }
        
        Employee other=(Employee) object;
        
        
        return name.equals(other.name)
        &&salary==other.salary
        &&hireDay.equals(other.hireDay);
    }
```
如果有子类的话，那么子类中直接调用超类的方法，如果相等再进行属性比较。
## 相等测试与继承
if(!(otherObject instance of Employee))return false;
允许其他类为Object的一个子类
Java规范:
1.自反性:x.equals(x)-true;
2.对称性:x.equlas(y)--y.equals(x)
3.传递性:x、y和z;
4.一致性:反复调用得到相同结果
5.对于非空对象x,x.equals(null）为false;
注意：
1.如果子类可以有自己的相等性概念，则对称性需求将强制使用getClass进行检测。
2.如果由超类决定相等的概念，那么就可以使用instance of检测，这样可以再不同子类的对象中进行相等行比较
说人话就是如果Emeployee和Manager进行比较，那么只用比较对应字段；如果两个Manager进行比较，则需要各个字段都相等，比如说奖金不相等就false,因此，需要使用getClass检测????
## hashcode方法
由对象导出的hash值。
字符串相同的话有相同散列值。
String s="OK";
StringBuilder sb=new StringBuilder(s);
String t=new String("Ok");
StringBuilder tb=new StringBuilder(t);

s和t的hash值是一样的，而sb和tb的hash值却不相同，Object 类的默认hashCode方法会从对象的存储地址得出散列码。
**常用API**
java.lang.Object
int hashCode 返回对象散列码

java.util.Objects
static int hash(Object ...objects)返回一个散列码，有所有对象的hash值组合而成
static int hashCode(Object a) 如果a为null返回0,否则返回a.hashCode().

java.lang.(Integer|Long...包装类)
static int hashCode(xxxValue) 返回给定值的散列码

java.util.Arrays
static int hashCode(xxx[] a)
计算数组a的散列码。
## toString方法
随处可见toString()方法的主要原因是:只要对象与一个字符串通过字符串"+"连接起来。Java编译器就会自动地调用toString方法来获得这个对象的字符串描述。
tip:在代码编写的过程中我们常常需要打印数组，但是原始的数组toString()的话会打印许I@a46e30这种前缀(这是由于历史原因造成的)，所以我们可以使用Arrays.toString(int[] nums),toString()在我们的日常调试中经常会用都，熟练应用百利而无一害。
**常用API**
java.lang.Object
Class getClass() 返回包含对象信息的类对象。
boolean equals(Object otherObject) 比较两个对象是否相等，如果两个对象指向同一块存储区域，方法返回true;
String toString() 返回表示该对象值的字符串。
#泛型数组列表
C/C++中，运行之前就必须规定数组的大小，

# 泛型数组列表

ArrayList<Object>可以理解成一个**带有类型参数**的泛型，<>指定了列表保存的元素对象的类型泛。泛型是由编译器来检测我们的集合对象中存放的对象是否符合我们之前的承诺，也就是说，如果定义了List<某种类型对象>，那么编译器就会帮助我们检查我们集合中添加的对象是否符合规范，如果不符合那么就会报错。

## 声明数组列表

编译器会检查这个变量、参数或方法的泛型类型，然后将这个类型放在<>中。
**常用API**
java.util.ArrayList<E>
ArrayList<E>() 构造一个空数组列表
ArrayList<E>(int initialCapacity) 构造一个定长的数组
boolean add(E obj) 数组列表末尾添加一个元素，永远返回true
int size() 返回存储在数组列表中的元素个数
void ensureCapacity(int capacity) 不进行扩容的情况下确保给定的capacity个元素能够被分配在数组中
void trimToSize() 将数组列表的存储容量削减到当前大小

## 访问数组列表元素
自动扩容有利有弊，数组列表自动扩容的便利也增加了访问元素语法的复杂程度。其原因是ArrayList并不是Java程序设计语言的一部分；它只是由某个人编写并在标准库中提供的一个实用工具类。
[]不能使用，而要使用get和set方法
例如，staff.set(i,harry);
等价于对数组a的元素赋值(与数组一样，下标值从0开始)
警告:
1.只有当数组的size大于i时，才能使用set方法
2.没有泛型类时，就是说如果集合<>中没有规定对象类型，那么默认的就是返回object类型
Employee e=(Employee)staff.get(i);
ArrayList list=new ArrayList<>();
while(...){
...
}
list.toArray(a);
如果需要在数组列表的中间插入元素，为此可以使用方法add方法并提供一个索引参数。
int n=staff.size()/2;
staff.add(n,e);
**常用API**
java.util.ArrayList<E>
E set(int index,E obj) 将值obj放置在数组列表的指定索引位置，返回之前的索引
E get(int index)　得到指定索引为值的内容
void add(itn index,E obj)　后移元素从而将obj插入到指定索引位置
E remove(int index)　删除指定索引位置的元素，并将后面的所有元素前移

## 类型化与原始数组的兼容性
处于兼容性的考虑，编译器检查到**没有发现违反规则**的现象之后，就将所有的类型化数组列表转换为原始的ArrayList对象。在程序运行时，所有的数组列表都是一样的，即虚拟机中没有类型参数。
#对象包装器和自动装箱
基本类型对应包装器类型，包装器是不可改变的，即一旦构造了包装器，就不允许更改包装在其中的值。

```
ArrayList<Integer> list=new ArrayList<Integer>();
list.add(3);
将会自动变换成
list.add(Integer.ValueOf(3));
```
相反的，将一个Integer对象赋给int值时,将会自动地拆箱。也就是说,编译器将int n=list.get(i);转换为int n=list.get(i).intValue();
**注意**自动装箱规范要求boolean、byte、char<=127,介于-128和127之间我们也称作缓冲区，short和int相同的值被包装到固定的对象中。我们可以和String类型的常量池进行一个比较，其实他们的作用是一样的。
另外，如果在一个表达式中混合使用Integer和Double类型,Integer值就会拆箱，提升为double,在装箱为Double。同时,我们需要清楚装箱和拆箱是编译器要做的工作,而不是虚拟机。
**常用API**
java.lang.Integer
int intValue()
将这个Integer对象的值作为一个int值返回。(覆盖Number类中的intValue方法)
static String toString()
返回一个新的String对象，表示指定的数值i的十进制表示
static String toString(int i,int radix)
返回数值i基于radix参数指定进制的表示
static int parseInt(String s)
static int parseInt(String s,int radix)
返回字符串s表示的整数，指定字符串必须表示一个十进制整数(第一种方法),或者采用radix参数指定的进制(第二种方法)
static Integer valueOf(String s)
static Integer valueOf(String s,int radix)
返回一个新的Integer对象，用字符串s表示的整数初始化，默认十进制，或者采用radix参数指定的进制
#参数数量和可变的方法
#枚举类
public enum Size{SMALL,MEDIUM,LARGE,EXTA_LARGE}
声明的时候用我们的enum
实际上，这个声明定义的类型是一个类，它刚好有4个实例,不可能构造新的对象。枚举的构造器总是私有的，如果使用其他修饰符那么就会出现语法错误。
枚举类型下有一个静态的values方法，它将返回一个包含全部枚举值的数组。
Size[] values=Size.values();
**常用API**
java.lang.Enum<E>
static Enum valueOf(Class enumClass,String name)
返回给定类中有指定名字的枚举常量
String toString()
返回枚举常量名
int ordinal()
返回枚举常量在enum声明中的位置,位置从0开始计数。
int compareTo(E other)
如果枚举常量出现在other之前，返回一个负整数;如果this==other，则返回0;否则,返回一个正整数。枚举常量的出现次序在enum声明中给出。
#反射
**反射库(reflection library)提供了一个丰富且精巧的工具集，可以用来编写能够动态操作Java代码的程序。
能够分析类能力的程序称为反射**
在运行时分析类的能力
在运行时检查对象，例如，编写一个适用于所有类的toString方法
实现泛型数组操作代码
利用Method对象，这个对象很像C++中的函数指针
总结来说反射是一种功能强大且复杂的机制。
## class类
Java为每个对象维护一个运行时类标识，从这个信息我们可以得知对象所属的类，虚拟机利用这个信息调用正确的方法。
**1.Object下有一个getClass()方法，返回的是一个Class类型的对象。**
Employee e;
Class class=e.getClass();
**2.还可以使用静态方法forName获得类名对应的class对象。**
String className="java.util.Random";
Class cl=Class.forName(className);
如果类名保存在一个字符串中，这个字符串会在运行时变化，就可以使用这个方法。
**3.T.class**
例如
Class cl1=Random.class;
这种方法得到的其实是泛型类，例如Employee.class得到的其实是Class<Employee>
虚拟机为每个类只有一个Class，也就是说我们可以使用==来进行比较
如果有一个Class类型的对象，可以用它来构造类的实例。调用getConstructor方法即可。
Class cl=Class.forName(className);
Object obj=cl.getConstructor().newInstance();
**常用API**
java.lang.Class
static Class forName(String className)
返回一个Class对象，表示名为className的类
Constructor getConstructor(class..parameter Types)
生成一个对象,描述有指定参数类型的构造器。
java.lang.reflect.Constructor
Ojbect newInstance(Object..params)
将params传递到构造器，来构造这个构造器声明类的一个新实例
java.lang.Throwable
void printStackTrace()
将Throwable对象和堆栈轨迹打印到标准流程错误
## 声明异常入门
## 资源
Class类中提供了查找资源文件的，但是得遵循以下步骤:
1.获得拥有资源的类的Class对象，ResourceTest.Class
2.有些方法需要描述资源位置的URL。
URL url=cl.getResource("about.gif");
我们通常将程序中可能会使用的文件等放在同一个报下，那么虚拟机就会自动的为我们进行搜索。这就是文件的自动装载，是利用资源加载特性完成的。
**常用API**
java.lang.Class
URL getResource(String name)
InputStream getResourceAsStream(String name)
找到与类位于同一位置的资源，返回一个可以用来加载资源的URL或者输入流。如果没有找到，则返回null。
## 利用反射分析类的能力
在java.lang.relect包下有三个类Field、Method和Constrcutor,这里在Spring容器中运用的非常多，所以我们需要认真学。
这三个类都有getName()的方法，用来返回字段、方法或者构造器的名称。
Field类下比较重要的是getType方法，用来返回某个字段的类型对象，类肯定就是Class。
Constructor类和Method类有报告参数类型的方法，Method类还有一个报告返回类型的方法，这三个类都有一个名为getModifiers的方法，它将返回一个整数，用不同的0/1位来描述修饰符。
Class类中的getFileds、getMethods和getConstructors方法将分别返回这个类支持的公共字段、方法和构造器的数组。
**常用API**
java.lang.Class
Field[] getFilelds()
Field[] getDeclaredFields()
getFields方法将返回一个包含当前类和超类的公共字段。getDeclaredField方法也返回包含当前类的field的数组。
Method[] getMethods()
Method[] getDeclaredMethods(()
返回包含Method对象的数组:getMethods将返回所有的公共方法，包括继承的公共方法。
Constructor[] getConstructors()
Constructor[] geteclaredConstructors()
返回包含Constructor对象的数组
String getPackageName()
得到报名。

## 使用反射编写泛型数组代码
在运行时获得任意对象数据字段字段的名字和类型
1.获得对应的Class对象
2.在这个Class对象上调用getDeclaredFields。
如果知道想要查看的字段名称和类型，那么查看就很简单。而利用反射机制，就可以知道编译时不知道的对象字段。

```
var harry=new Employee("Harry Hacker",50000,10,1,1989);
Class cl=harry.getClass();
Field f=cl.getDeclaredField("name");
```
上面的代码中我们中我们不知道是否存在name这个字段，但是我们可以使用getDeclaredField()得到一个Field对象，再用f.get(harry)方法,返回的将是obj的当前字段值。
**注意**:Java安全机制允许查看一个对象有哪些字段，但是除非是访问权限，否则不允许进行读写。但是可以使用f.setAccessible(true)；覆盖Java的访问控制。
## 调用任意方法和构造器
Object invoke(Object obj,Object args)作用是用来调用包装在当前Method对象中的方法。
第一个参数是隐式参数，其余的则提供了显式参数
double salary=(Double)m2.getSalary();
注意我们在使用这个方法的时候必须进行强制的类型转换，最后则会进行自动拆箱。

# 继承设计的技巧

1.将公共操作放在超类中。

2.不要使用受保护的字段

> 1.子类是无限的，任何一个人都可以编写子类来访问protected实例字段
>
> 2.同一个包下也能够访问protected实例字段。

这也解释了为什么在那么多的代码中没有见过用protected修饰字段

3.思考继承有没有被滥用

书上的例子是钟点工有个时薪的属性，我们在设计时会考虑继承Employee,然后添加一个时薪字段。然而，如果我们需要打印工资和薪水的时候，那么就得对二者做以区分，如果再计算税率，那么就更加复杂，还不如直接用employee类，再单独处理时薪(我的理解是这样式儿的)。

4.除非继承的方法有意义，否则不要继承。

例子更加直观，如果想实现Holiday，正常思路去继承GregorianCalendar

在设计的时候GregorianCalendar中已经有个公共的add(用来将假日转换成非假日)的方法了，无法确保假日集合的封闭性，也就是说任何人能够访通过这个操作来进行修改，显然这是不太安全的。

5.在覆盖方法时，不要更改预期的行为。

6.使用多态，而不要使用类型信息。

```java
if(s is of type 1){
acition(x1);
}else if(x is of type 2){
    action(x2);
}
```

上面

