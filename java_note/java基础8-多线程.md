### day01

这里是按照how2j的知识点来的，先建立起一个框架-即比较重点的知识点

#### 启动一个线程

##### 继承线程类
很简单就是extends关键字，同时我们还需要重写run()方法,最后使用start()方法
下面的示例非常有用

```java
public class SearchFileThread extends Thread{
	private File file;
    private String search;
    
    public SearchFileThread(File file,String search){
    	this.file=file;
        this.search=search;
    }
    
    public void run(){
    	String fileContent=readFileContent(file);
    }
    
    public void readFileContent(File file){
    	//因为这里会出异常所以我们需要进行规避,同时下面的代码中我们可以使用try之后的括号来完成一系列的前置操作
        try(FileReader fr=new FileReader(file)){ //在这里创建了一个字符输入流
        	char[] all=new char[(int)file.length()];
            fr.read(all);
            return new String(all);
        }catch(FileNotFoundException e){
        	e.printStackTrace();
            return null;
        }catch(IOException e){
        	e.printStackTrace();
            return null;
        }
    }
}
```
测试类
```java
public static void search(File){
	if(file.isFile()){
    	if(file.getName().toLowerCase().endswith(".java")){
        	new SearchFileThread(file,search);
        }
    }
    if(file.isDrectory()){
    	File[] fs=file.listFiles();
        for(File f:fs){
        	search(f,search);
        }
    }
}

public static void main(String[] args){
	File folder=new File("");
    search(folder,"Magic");
}
```
上面代码中常用的几个方法，首先就是file.isFile()和file.isDerectory()方法，用于判断是否为文件夹。其次就是file.getName().endswith()方法，这里让我想起了stream管道流处理数据。
##### 实现Runnable接口
启动的时候，首先创建一个对象，然后再根据对象创建一个线程，并进行启动

```java
Battle battle1=new Battle(gareen,teemo);
new Thread(battle).start();
```
##### 匿名类
继承Thread,重写run方法，直接在run方法中写业务代码
匿名类的一个好处是可以很方便访问外部的局部变量

```java
//匿名类
Thread t1=new Thread(){
	public void run(){
    	while(!teemo.isDead()){
        	gareen.attackHero(teemo);
        }
    }
};

t1.start();
Thread t2=new Thread(){
	public void run(){
    	while(!lessin.isDead()){
        	bh.attackHero(lessin);
        }
    }
}
t2.start();
```

### day02

**常用API**
sleep-当前线程暂停
join-等待线程终止
setPriority-设置线程优先级
yield-临时暂停
setDaemon-守护线程

##### 当前线程暂停

```java
有一点需要注意的是当前线程sleep的时候有可能被停止，这时就会抛出InterrunptedException
public static main(String[] args){
	Thread t1=new Thread(){
    	public void run(){
        	int seconds=0;
            while(true){
            try{
            	Thread.sleep(1000);
            }catch(){
            	e.printStackTrace();
            }
            	System.out.printf("已经玩了LOL %d 秒%n",seconds++);
            }
        }
    };
    t1.start();
}
```
##### 加入到当前线程中
这一块儿how2j的示例部分我个人认为是存在问题的。

```java
public void main(String[] args){
	final Hero gareen=new Hero();
    gareen.name="盖伦";
    gareen.hp=616;
    gareen.damage=50;
    
    final Hero teemo=new Hero();
    teemo.name="提莫";
    teemo.hp=300;
    teemo.damage=30;
    
    final Hero bh=new Hero();
    bh.name="赏金猎人";
    bh.hp=500;
    bh.damage=65;
    
    final Hero lessin=new Hero();
    lessin.name="盲僧";
    lessin.hp=455;
    lesssn.damage=80;
    
    Thread t1=new Thread(){
    	public void run(){
        	while(!teemo.isDead()){
            	gareen.attackHero(teemo);
            }
        }
    };
    t1.start();
   
  //这里示例代码说之前一直是main线程在运行,只有t1线程运行结束，才会继续往下走，其实也就是书上说的等待线程终止
  try{
  	t1.join();
  }catch(InterruptedException e){
  	e.printStackTrace();
  }
  
  Thread t2=new Thread(){
  	public void run(){
    	while(!lessin.isDead()){
        	bh.attackHero(lessin);
        }
    }
  };
  //会观察到盖伦把提莫杀掉之后,才会运行t2线程
  t2.start();
}
```
上面的代码有问题，join()阻塞，从书上可以看到是等待终止指定的线程。

##### 设置线程优先级

```java
	Thread t1= new Thread(){
            public void run(){
 
                while(!teemo.isDead()){
                    gareen.attackHero(teemo);
                }              
            }
        };
          
   	Thread t2= new Thread(){
            public void run(){
                while(!leesin.isDead()){
                    bh.attackHero(leesin);
                }              
            }
        };
   t1.setPriority(Thread.MAX_PROIORITY);
   t2.setPriority(Thread.MIN_PROIORITY);	
   t1.start();
   t2.start();
```
虽然这里只是简单的进行应用，如果想成为高级Java工程师的话，那么这里就是我们必经之路。

##### 临时暂停

```java
public static void main(String[] args){
	Thread t1=new Thread(){
    	public void run(){
        	while(!teemo.isDead()){
            	gareen.attackHero(teemo);
            }
        }
    }
    
    Thread t2=new Thread(){
    	public void run(){
        	while(!lessin.isDead()){
            	Thread.yield();
                bh.attackHero(lessin);
            }
        }
    }
}
```
**守护线程相当于给我们执行任务的线程提供支持的线程**
**密码破解和守护线程**
首先我进行了破解线程的编写
其中比较难考虑的点是如何去破解密码，核心代码如下

```java
if(c==password.charAt(i)){
                    //将每次的密码都放进passwords集合中
                    guessPassword[i]=c;
                    String guess=new String(guessPassword);
                    result+=c;
                    passwords.add(guess);
                    try {
                        Thread.sleep(1000);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
}
```
如果当前字符和password中的i索引处的密码相等的话，那么我们就可以将这个字符串通过守护线程给它打印出来

```java
while(true){
            while(passwords.isEmpty()){
                try {
                    Thread.sleep(1);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
            String password=passwords.remove(0);
            System.out.println("穷举法产生的密码是:"+password);
        }
```
守护线程会进行sleep，为的是能够打印破解线程输出的密码
这里有两个需要注意的api，一个是**Charatcter.isLetterOrDigit()**用来判断是否为字母或者数字
还有就是我们设置守护线程**Thread.setDemon()**;

### day03

#### 同步问题

使用n个线程来增加英雄的血量，再使用n个线程来减少英雄的血量

```java
Thread[] addThreads=new Thread[n];
Thread[] redueceThreads=new Thread[n];
for(int i=0;i<n;i++){
Thead t=new Thread(){
	public void run(){
    	gareen.recover();
        try{
        	Thread.sleep(100);
        }catch(){
        	e.printStackTrace();
        }
    };
    t.start();
    addThreads[i]=t;
}
}
//减少血量的多个线程
for(int i=0;i<n;i++){
Thead t=new Thread(){
	public void run(){
    	gareen.damage();
        try{
        	Thread.sleep(100);
        }catch(){
        	e.printStackTrace();
        }
    };
    t.start();
    addThreads[i]=t;
}
}
//等待增加和减少血量的线程执行完成
for(Thread t:addThreads){
	try{
    	t.join();
    }catch(InterruptionException e){
    	e.printStack();
    }
}
for(Thread t:addThreads){
	try{
    	t.join();
    }catch(InterruptionException e){
    	e.printStack();
    }
}
```
#### 原因

上面的代码很明显容易出问题，Hero的对象在这种情况被并发访问的话肯定是要出大问题的

1. 假设增加线程先进入，得到的hp是10000
2. 进行增加运算
3. 正在做增加运算的时候，还没有来得及修改hp的值，减少线程来了
4. 减少线程得到的hp的值也是10000
5. 减少线程进行减少运算
6. 增加线程运算结束，得到值10001，并把这个值赋予hp
7. 减少线程也运算结束，得到值9999，并把这个值赋予hp
hp，最后的值就是9999
**解决思路**
保证访问对象的安全性
在增加线程进行增加时不允许其他线程进行访问和修改操作
1. 增加线程获取到hp的值，并进行运算
2. 在运算期间，减少线程试图来获取hp的值，但是不被允许
3. 增加线程运算结束，并成功修改hp的值为10001
4. 减少线程，在增加线程做完后，才能访问hp的值，即10001
5. 减少线程运算，并得到新的值10000
![选区_255.png](https://i.loli.net/2021/06/28/WrFVQbyC9ldTMJE.png)
**synchronized监视器锁**
线程和对象进行绑定，如果其他线程试图占有则会等待
```java
public static void main(String[] args){
	final Object someObject=new Object();
    
    Thread t1=new Thread(){
    	public void run(){
        	try{
            	synchronized(someObject){
                	System.out.println(now()+this.getName()+"占有对象");
                    Thread.sleep(5000);
                    System.out.println(now()+this.getName()+"t1 线程结束");
                    
                }
            }catch(InterruptedException e){
            	e.printStackTrace();
            }
        }
    };
    t1.setName("t1");
    t1.start();
    
    Thread t2=new Thread(){
    	public void run(){
        	try{
            	synchronized(someObject){
                	System.out.println(now()+this.getName()+"占有对象");
                    Thread.sleep(5000);
                    System.out.println(now()+this.getName()+"t2 线程结束");
                    
                }
            }catch(InterruptedException e){
            	e.printStackTrace();
            }
        }
    };
    t2.setName("t2");
    t2.start();
}
```
同时可以回忆一下synchronzied的用法，一种是对方法进行加锁，第二种是对对象进行加锁，第三种是对代码块进行加锁
那么在血量修改的例子中，可以使用synchronized关键字保证someObject对象同一时间只能被一个线程占有
核心代码

```java
//定义一个someObject对象
final Object someObject=new Object();

for(int i=0;i<n;i++){
	Thread t=new Thread(){
    	public void run(){
        	synchronized(someObject){
            	gareen.recover();
                try{
                	gareen.recover();
                }catch(InterruptedException e){
                	e.printStackTrace();
                }
            }
        }
    }
}
```
上面的代码是为了理解大佬这么想的，但是实际中也没必要平白无故的去写一个someObject，所以直接对当前对象进行加锁
```java
Thread t=new Thread(){
	public void run(){
    	synchronized(gareen){
        	gareen.recover();
        }
        try{
        	Thread.sleep();
        }catch(InterruptedException e){
        	e.printStack();
        }
    }
}
```
**方法之前添加synchronized**

#### 线程安全的类

如果使用synchronized来修饰类的话那么该类就叫做线程安全的类,同一时间，只能由一个线程绑定一个类的实例去修改数据。
我觉得还是线程和对象实例进行了绑定，或者说是和代码块中的局部变量进行了绑定，同一时间，只能有一个线程来进行访问和修改。
比如StringBuffer就是使用synchronized来修饰的

#### 区别

1.HashMap和HashTable的区别
2.StringBuffer和StringBuilder的区别
3.ArrayList和Vector
**4.把非线程安全的集合转换为线程安全**
List<Integer> list2=Collections.synchronized(list1);

### day04

#### 死锁

当出现竞态条件时，那么自然而然的就会发生死锁，两个线程互相等待，非常容易理解

```java
public void run(){
	synchronized(obj1){
    	//设置线程暂停
        
        Thread.sleep(1000);
        
        synchronized(obj2){
        	dosomething
        }
    }
}

public void run(){
	synchronized(obj1){
    	//设置线程暂停
        
        Thread.sleep(1000);
        
        synchronized(obj2){
        	dosomething
        }
    }
}
}
```
#### 如何让3个线程彼此形成死锁

```java
synchronized(a){
	Thread.sleep(1000);
    synchronized(b){
    	dosomething...
    }
}
synchronized(b){
	Thread.sleep(1000);
    synchronized(c){
    	dosomething...
    }
}
synchronized(c){
	Thread.sleep(1000);
    synchronized(a){
    	dosomething...
    }
}
```
#### 线程之间的交互

如果现在的场景是
首先，需要清楚什么是好的设计方式和坏的设计方式，坏的解决方案就是使用while()来判断当前英雄的血量是否见底。很容易理解，但是这样处理的话CPU的负载就比较大，很明显不符合我们的设计思路。

```java
Thread t1=new Thread(){
	public void run(){
    	while(true){
        	while(gareen.hp==1){
            	continue;
            }
            gareen.hurt();
            System.out.println("t1为%s 减血1点，减少血后，%s的血量是%.0f%n",gareen.name,gareen.hp);
        }
    }
}
```
#### 使用wait和notify进行线程交互

this.wait()表示让占有this对象的线程等待，并临时释放占有
this.wait()会让减血线程临时释放this的占有。这样加血线程，就有机会进入recover()加血方法了。
然后，在执行了加血方法之后，增加了血量，执行this.notify()方法来唤醒减血线程，其实notify()会唤醒等待的线程，但是在队列中只有减血的线程处于等待。

```java
public synchronized void hurt(){
	if(hp==1){
    	try{
    		this.wait();
        }catch(InterruptedException e){
        	e.printStackTrace();
        }
    }
    hp=hp-1;
    System.out.println("%s减血1点,减少血后,%s的血量为%.0f%",name,name,hp);
}

public synchronized void recover(){
	hp=hp+1;
    System.out.printf("%s回血1点，回血后%s的血量为%.0f%",name,name,hp);
    this.notify();
}
```
上面的printf("%.0f%",a)是为了确定后面hp的格式;
**注意**
由this.notify()可以很明显地看出，这个是对象的方法而不是Thread的方法，这个对象一定需要是同步对象，所以需要使用synchronized关键字来进行修饰。
在生产者消费者的例子中，通过实际中的场景来帮助我们更加深刻地理解线程之间的交互

```java
public class Mystack{
	LinkedList<T> values=new LinkedList<T>();
    
    public synchronized void push(T t){
    	wihile(vlaues.size()>=200){
        	try{
            	this.wait();
            }catch(InterruptedException e){
            	e.printStackTrace();
            }
        }
        this.notifyAll();
        values.addLast();
    }
    
    public synchronized T pull(){
    	while(values.isEmpty()){
        	try{
            	this.wait();
            }catch(){
            	e.printStackTrace();
            }
        }
        this.notifyAll();
        return values.removeLast();
    }
    
    public T peek(){
    	return values.getLast();
    }
}
```
生产者
```java
public class ProducerThread extends Thread{
	private MyStack<Character> stack;
   	
    public ProducerThread(MyStack<Character> stack,String name){
    	super(name);
        this.stack=stack;
    }
    
    public void run(){
    	while(true){
        	char c=randomChar();
            System.out.println(this.getName()+"压入:"+c);
            stack.push(c);
            try{
            	Thread.sleep(100);
            }catch(InterruptedException e){
            	e.printStackTrace();
            }
        }
    }
    
    public char randomChar(){
    	return (char)(Math.random()*('Z'+1-'A')+'A')
    }
}
```
消费者线程
```java
public class ConsumerThread extends Thread{
	private MyStack<Character> stack;
    
    public ConsumerThread(MyStack<Character> stack,String name){\
    	super(name);
        this.stack=stack;
    }
    
    public void run(){
    	while(true){
        	char c=stack.poll();
            System.out.println(this.getName()+"弹出:"+c);
            stack.poll();
            try{
            	Thread.sleep(100);
            }catch(InterruptedException e){
            	e.printStackTrace();
            }
        }
    }
}
```

### day05

#### 线程池

#### 设计线程思路
![选区_256.png](https://i.loli.net/2021/07/01/y9i52B4wFDsPfOJ.png)
从上图中可以得知，计算机内存中存在一个任务队列。各个线程会根据一定的逻辑和任务进行绑定，然后再次等待下一个任务。
之前的生产者消费者模型:
1.准备一个任务容器
2.一次性启动10个消费者线程
3.刚开始任务容器是空的，所以线程都会wait
4.知道一个外部线程往这个任务容器中扔了一个"任务"，就会有一个线程被notify
5.这个消费者线程取出"任务",并且执行这个任务，执行完毕后，继续等待下一次任务的到来。
6.如果短时间内，有较多的任务加入队列，那么就会有多个线程被唤醒，去执行这些任务。
**开发一个自定义线程**
1.首先，明确线程池中的数据都有什么，size长度、链表(存放线程)
2.需要完成什么事儿，启动消费10个线程、往线程池中扔一个任务，唤醒其他线程
核心代码

```java
//线程池大小
int PoolSize;

//任务容器
LinkedList<Runnable> tasks=new LinkedList<Runnable>();

//初始化
public ThreadPool(){
	threadPoolSize=10;
    
    synchronized(tasks){
    	for(int i=0;i<threadPoolSize;i++){
        	new TaskConsumeThread("任务消费者线程").star();
        }
    }
}

//既是重点，也是难点
public void add(Runnable r){
	synchronized(tasks){
    	tasks.add(r);
        tasks.notifyAll();
    }
}

class TaskConusmeThread extends Thread{
	public TaskConsumeThread(String name){
    	super(name);
    }
    
    //难点
    while(true){
    	sout("启动:"+this.getName());
        synchronized(tasks){
        while(tasks.isEmpty()){
            	try{
                	tasks.wait();
                }catch(InterruptedException e){
                	e.printStackTrace();
                }
            }
            task=tasks.removeLast();
            
            this.notifyAll();
        }
        sout(this.getName()+"获取到任务，并执行");
        task.run();
    }
}
```
上面的add()方法就是用来添加任务的，从代码中可以看到传入的参数是Runnable类型的，所以说明任务的类型可以像这样设计，不得不说这里的干货非常的足。
从run()方法中可以得知如果说任务队列中有任务，那么就去执行任务；如果队列为空，那么就需要wait()

#### Lock对象

####　原子访问

