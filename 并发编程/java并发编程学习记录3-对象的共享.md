## 可见性
当读写操作串行时，那么变量的值是一定的。但如果当读写操作不在同一个线程中，而在不同的线程中，那么就无法保证读线程对于内存写入操作的可见性。

```
public class Novisibility{
	private static boolean ready;
    private static int number;
    
    private static class ReadThread extends Thread{
    	public void run(){
        	while(!ready){
            	Thread.yeild();
                System.out.println(number);
            }
        }
    }
    
    public static void main(String[] args){
    	ready=true;
        number=42;
    }
}、
```
由于没有使用同步机制，对于读线程来说，主线程中值的更新对于读线程来说不一定是可见的。
两种现象，一种是读线程可能永远也看不到ready的值；
另外一种是读线程虽然能够访问到ready的值，但是number的值却有可能为0，为什么，如果我们没有使用同步机制，操作系统中就会进行指令重排序，从而导致读线程读到的可能与写线程写入的顺序相反。
### 失效数据
a线程和b线程，当访问没有进行同步时，假设一个线程进行的是读操作，另一个操作进行的是写操作，如果无法进行同步的话，读线程可能访问的是更新之前的值，也可能访问的是更新之后的值。
### 非原子的64位操作
jvm中，允许将一个long类型的64位的数据以两个32位的数据进行保存，如果多个线程进行访问的话，可能的结果就是拿到了原来的比如说低32位的数据和更新后的高32位的操作，那么这个时候显然变量的安全性无法保障
### 加锁和可见性的保证
Java中的内置锁，当a线程和b线程访问同一个同步代码块，b线程在lock之前能够看到a线程的unlock之前额所有操作结果实现了同步的可见性保。
我的理解是：
jdk提供的内置锁，可以将一个对象中的某个代码同步起来，什么意思呢，多个线程访问的时候，某个线程对象的监视器锁进行绑定，只有当前线程释放锁之后，其他线程才可访问同步代码块，不然会处于阻塞（or其他形式？？？）
还有书上提到了要求在同一个锁中，前面的学习可知，Java中每个对象含有一个内置锁，也就是说多个线程访问的是同一个对象？？？？？？
## 发布与逸出
发布：“发布”一个对象的意思是指，是对象能够在当前的作用域之外的代码中去使用。例如，将一个指向该对象的引用保存到其他代码可以访问的地方，或者在某一个非私有的方法中返回引用，或者将引用传递到其他类的方法中。我们有可能不想对象被发布，也可能需要发布对象。
发布对象的最简单方法是将对象的引用保存到一个公有的静态变量中，以便任何类和线程都能看见该对象。

```
public static Set<Secret> knowSecrets; //定义一个static的变量

public void initiaize(){
	knowSecrets=new HashSet<Secret>();  //将HashSet对象的引用保存到knowSecret中
}
```
逸出：虽然书上没有提到，我的理解就是本不该被发布的内容由于代码的不规范而被发布了，可惜我平时的crud中似乎从来没考虑过这个问题。当发布一个对象时，在该对象的非私有域中中引用的所有对象同样会被发布。一般来说，如果一个已经发布的对象能够通过非私有的变量引用和方法调用到达其他的对象，那么这些对象那个也都会被发布。
还有一种发布对象或者其内部状态的机制就是发布一个内部的实例
```
public class ThisEscape{
	public ThisEscape(EvnetEscape source){xiancheng
    	source.registerListener(new EventListener(){
        	public void onEvent(Event e){
            	doSomething(e);
            }
        });
    }
}
```
### 安全的对象构造
通过上述内容，我们可知，当内部实例发布时，外部封装的实例(ThisEscape)实例也逸出了，“当且仅当对象的构造函数返回时，对象才处于可预测的和一致的状态。”也就是说，当从一个对象的构造函数中发布对象时，发布了一个尚未完成的对象。即使发布对象的语句位于构造函数的最后一行也是如此。如果this引用在构造过程中逸出，那么这种对象就被认为是不正确的构造，我们应该避免在构造过程中是this逸出。
常见的错误是，在构造函数中启动一个线程。当对象在其构造函数中创意建一个线程时，无论是显式创建(通过将它传给构造函数)还是隐式创建(由于Thread或Runnable是是该对象的一个内部类)，this引用都会被新创建的线程共享。在未完全构造前，新的线程就可以看见他，所以他就不符合安全构造对象的规范。
在构造函数中创建线程并没有错误，但不要立即启动他，而是通过一个start或者initialize方法来启动。
如果想在构造函数中注册一个事件监听器或启动线程，那么可以使用一个私有的构造函数和一个公共的工厂方法，从而避免不正确的构造过程。

```
public class SafeListener{
	private final EventListener listener;
    
    private SafeListener(){
    	listener=new EventListener(){
        	public void onEvent(Event e){
            	doSomething(e);
            }
        };
    }
    
    public static SafeListener newInstance(EventSource source){
    	SafeListener safe=new SafeListener();
        source.registerListener(safe.listener);
        return safe;
    }
}
```
## 线程封闭
当访问共享的可变数据时，通常需要同步。一种避免使用同步的方式就是不共享数据。如果仅在单线程内访问数据，就不需要同步。这种技术被称为线程封闭(Thread Confinement),它是实现线程安全性的最简单方式之一。当某个对象封闭在一个线程中时，这种用法将自动实现线程安全性，即使被封闭的对象本身不是线程安全的。
线程封闭技术的一种常见应用是JDBC(Java Database Connectivity)的Connection对象。JDBC规范并不要求Connection对象必须是线程安全的。在典型的服务器应用程序中，线程从连接池中获得一个Connection对象，并且用该对象来处理请求，使用完之后再将对象返还给连接池。由于大多数请求(例如Servlet请求或EJB调用等)都是单个线程采用同步的方式来处理，并且在Connection对象返回之前，连接池不会再将它分配给其他线程。
### Ad-hoc线程封闭
维护线程封闭的职责完全由程序实现来承担。
### 栈封闭
栈封闭是线程封闭的一种特例，在栈封闭中，只能通过局部变量才能访问对象。可以和封装进行一个类比，封装使得代码更容易维持代码的不变性，同步变量也能使对象更易于封闭在线程中。局部变量的固有属性之一就是封闭在执行线程中。他们位于执行线程的栈中，其他线程无法访问这个栈。

```
public int loadTheArk(Collection<Animal> candidates){
	SortedSet<Animal> animals;
    int numPairs=0;
    Animal candidate=null;
    
    animal=new TreeSet<Animal>(new SpeciesGenderComparator());
    animals.addAll(candidates);
    for(Animal a:animals){
    	if(candidate==null || !candidate.isPotentialMate(a)){
        	candidte=a;
        }else {
        	ark.load(new AnimalPair(candidate,a));
            ++numPairs;
            candidate=null;
        }
    }
    return numPairs;
}
```
我的理解：
猜想和深入理解JVM中提到的每个方法都有自己的栈有关
“局部变量的固有属性之一就是封闭在执行线程中。它们位于执行线程的栈中”，当前执行线程有栈，栈里面有每个方法的变量和返回值等，也就是说局部变量只能由当前线程进行访问，其实也就是线程封闭的一种。但是如果发布了对集合animals(或者该对象中的任何内部数据)的引用，那么封闭性将被破坏，并导致对象animals的逸出。
### ThreadLocal类
为每个线程保留了一个当前线程访问的副本，并将其与原对象进行关联，提供了get与set等接口或方法，这些方法。
ThreadLocal对象通常用于防止对可变的单实例变量(Singleton)或全局变量进行共享。例如，在单线程应用程序中可能会维持一个全局的数据库连接。并在程序启动时初始化这个连接对象，从而避免在调用每个方法时都要传递一个Connection对象。由于JDBC的连接对象不是线程安全的，因此，当多线程应用程序在没有协同的情况下使用全局变量时，就不是线程安全的。通过将JDBC的连接保存到ThreadLocal对象中，每个线程都会有一个属于自己的连接对象。

```
private static ThreadLocal<Connection> connectionHolder=new ThreadLocal<Connection>(){
	public Connection initialValue(){
    	return DriverManager.getConnection(DB_URL);
    }
};

public static Connection getConnection(){
	return connectionHolder.getConnection();
}
```
ThreadLocal<Connction>指定了我在ThreadLocal中保存的是Connection类型的对象，由Java核心技术卷可知，泛型的定义是从编译器角度出发的。写了自定义类之后，我们在其内部定义了initialValue方法来帮助我们获得连接。
当某个执行频繁的操作需要一个临时对象，例如，Java5.0之前，Integer.toString()方法使用ThredLocal对象来保存一个12字节大小的缓冲区，而不是使用共享的静态缓冲区(这需要使用锁机制)或者在每次调用时都分配一个新的缓冲区。
“从概念上看，你可以将ThreadLocal<T>视为包含了Map<Thread,T>对象，其中保存了特定位于该线程的值...这些特定于线程的值保存在Thread对象中，当线程终止后，这些值将会作为垃圾回收。”
在实现应用程序框架时大量使用了ThreadLocal。例如，在EJB调用期间，J2EE容器需要将一个事务上下文(Transaction Context)与某个执行中的线程关联起来。通过将事务上下文保存在静态的ThreadLocal对象中，可以很容易实现这个功能:当框架代码需要判断当前运行的是哪个事务时，只需要从这个ThreadLocal对象中读物事务上下文。

## 不变性
如果某个对象在被创建后其状态就不能被修改，那么这个对象被称为不可变对象。线程安全性是不可变对象的固有属性之一，它们的不变性条件是由构造函数创建的，只要他们不改变，那么这些不变性条件就会维持。

```
@Immutable
public final class ThreeStooges{
	private final Set<String> stooges=new HashSet<String>();
    
    public ThreeStooges(){
    	stooges.add("Moe");
        stooges.add("Larry");
        stooges.add("Curly");
    }
    
    public boolean isStooge(String name){
    	return stooges.contains(name);
    }
}
```
类ThreeStooges中定义了一个可变的Set对象，但是用的final类型来声明变量类型，所以Set对象构造完成之后无法进行修改。而ThreeStooges构造函数也很明确add了三个对象，也就是说后面不会再进行修改
### Fina域
Java核心技术卷中提到的final关键字，其实也就是final域，个人猜想也是Java的机制之一。
### 示例：使用volatile类型来发布不可变对象
“对于在访问和更新多个相关变量出现的竞争条件问题，可以通过将这些变量全部保存到一个不可变对象中来消除。”在第二章中，我们有遇到过创建对象getInstance()时判断Object是否存在的竞态条件，如果将这需要进行判断的对象设置为不可变的话，其实竞态也就不存在，因为不可变对象从一开始就设置好了状态，后面肯定不会修改。
“如果要更新这些变量，那么可以创建一个新的容器对象，但其他使用原有对象的线程依然会看到对象处于一致的状态”，VolatileCachedFactorizer使用了OneValueCache来保存缓存的数值及其因数。“当一个线程将volatile类型的cache设置为引用一个新的OneValueCache时”，其他线程就能立即看到新缓存的数据。

```
@ThreadSafe
public class VolatileCachedFactorizer implements Servlet{
	private volatile OneValueCache cache=new OneValueCache(null,null);
    
    public void service(ServletRequest req,ServletResponse resp){
    	BigInteger i=extracrFromRequest(req);
        BigInteger[] factors=cache.getFactors(i);
        if(factors==null){
        	factors=factor(i);
            cache=new OneValueCache(i,factors);
        }
        encodeIntoResponse(resp,factors);
    }
}
```
## 安全发布
最常见的发布方式就是将对象引用保存到公有域中
	

```
//不安全的发布
public Holder holder;

public void intialize(){
	holder=new Holder(42);
}
```
由于可见性问题，其他线程看到的Holder对象将会处于不一致的状态，线程之间不可见的话，那么a线程无法看到b线程是否保存了b线程的引用，即便在该对象的构造函数中已经正确地创建了不变性条件，这种不正确的发布导致其他线程看到尚未创建完成的对象。
### 不能正确的发布：正确的对象被破坏
由于没有使用同步来确保Holder对象对其他线程可见，因此将Holder称为“未被正确发布”。存在两个问题，首先，除了发布Holder对象的线程之外，其他线程看到的Holder域是一个失效值，因此将看到一个空引用或者之前的失效值；更糟糕的是线程看到的Holder引用的值是最新的，但Holder状态的值却是失效的。某个线程在第一次读取域时得到失效值，而再次读取这个域时会得到一个更新值。
### 不可变对象与初始化安全性
“任何线程都可以在不需要额外同步的情况下安全地访问不可变对象，即使在发布这些对象时没有使用同步。”
这种保证还将延伸到被正确创建的对象中所有final类型的域。
在没有额外同步的情况下，也可以安全地访问final类型的域。
然后，如果final类型的域所指向的是可变对象，那么在访问这些域所指向的对象的状态时任然需要同步。
### 安全发布的常用模式
可变对象必须通过安全的方式来发布，这通常意味着在发布和使用该对象的线程时都必须使用同步。
"要安全地发布一个对象，对象的引用以及对对象的状态必须同时对其他线程可见。一个正确构造的对象可以通过以下方式来安全地发布："

>在静态初始化函数中初始化一个对象引用
>将对象的引用保存到volatile类型的域或者AtomicReferance对象中。
>将对象的引用保存到某个正确构造对象的final域中。
>将对象的引用保存到一个由锁保护的线程中。
>在线程安全容器内部的同步意味着，在将独享放入到某个容器，例如Vector或synchronizedList时，将满足上述最后一条需求。如果线程A将对象X放入一个线程安全的容器，随后线程B读取这个对象，那么可以确保B看到A设置的X状态，即便在这段读/写X的应用程序代码中没有包含显式的同步
>通过将一个键或者值放入Hashtable、synchronizedMap或者ConcurrentMap中，可以安全地将它发布给任何从这些容器中访问它的线程(无论是直接访问还是通过迭代器访问)
>通过将某个元素放入Vector、CopyOnWriteArrayList、CopyOnWriteArraySet、synchronizedList或者synchronizedSet中，可以将该元素安全地发布到任何从这些容器中访问该元素的线程
>通过将某个元素放入BlockingQueue或者ConcurrentLinkedQueue中，可以将该元素安全地发布到任何从这些队列中访问该元素的线程
>###事实不可变对象
>如果对象从技术上来看是可变的，但其状态在发布后不会再改变没那么把这种对象称为“事实不可变对象”。
```
public Map<String,Date> lastLogin=Collections.synchronizedMap(new HashMap<String,Date>());
```
### 可变对象
要安全地共享可变对象，这些对象就必须被安全地发布
对象的发布需求取决于它的可变性

>不可变对象可以通过任意机制来发布
>事实不可变对象必须通过安全方式来发布
>可变对象必须通过安全方式来发布，并且必须是线程安全的或者由某个锁保护起来。	
>###安全地共享对象
>当发布一个对象时，必须明确地说明对象的访问方式
>线程封闭 前面提过
>只读共享 表面意思
>线程安全共享 内部实现同步
>保护对象 被保护的对象只能通过持有特定的锁来访问