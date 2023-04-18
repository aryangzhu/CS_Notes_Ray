# 新建一个线程
## 三种方式
### extends Thread
### implements Runnbale
创建Thread实例时,传入一个Runnable
### 匿名内部类
new Thread(){
​	@override
​	public void run(){
​		...
​	}
}
sleep()方法可以暂停线程一段时间
# 线程的状态
## 6种状态
新建 New  
运行 Runnable  
阻塞 Blocked 
等待 Waitingshiyng  
计时等待 Time Waiting  
终止 Terminated
如果在main线程中调用t.join()，**那么main线程就会等待t线程执行完再执行**。
# 中断线程
在其他线程中对目标线程使用interrupted方法，**目标线程会检测自身状态是否是interrupted状态**，如果是则立马结束运行。  
但是,interrupt()方法仅仅向t线程发出了"中断请求",**至于t线程能否立刻响应,要看具体代码**。  
如果线程处于等待状态,例如,t.join()会让main线程进入等待状态,此时,如果对main线程调用interrupt(),**join()方法会立刻抛出InterruptedException**。  

如果线程处于等待状态时,暂停的话会抛出InterruptedException
# 守护线程
# 线程同步
## sychronized加锁的三种方式
### 对象加锁
### 静态方法加锁
### 实例方法加锁
# 使用wait和notify
多线程协调运行的原则就是：当条件不满足时，线程进入等待状态；当条件满足时，线程被唤醒，继续执行任务。  
wait方法会挂起线程并释放线程持有的锁,notify会唤醒线程并持有锁。  
notify()方法只会随机唤醒一个线程,而notifyAll方法会唤醒所有线程,但是只有一个线程能够拿到锁。  
wait方法和notify方法结合使用,而且只能在加锁的块中使用。  
我们来看一个例子

```java
class TaskQueue{
    private Queue<String > queue=new LinkedList<>();
    
    public void addTask(String s){
        queue.add(s);
		this.notifyAll();
    }
    
    public String getTask(){
        while(queue.isEmpty()){
            this.wait();
        }
        return queue.remove();
    }
}
```
1. 在synchronized内部可以调用wait()方法使线程进入等待状态
2. 必须在**已获得的锁对象**上调用wait()方法。
3. 在synchronized内部可以使用notify()和notifyAll()方法唤醒其他等待线程
4. 必须在**已获得的锁对象上**调用notify()和notifyAll()方法
5. **已唤醒的线程还需要重新获得锁**后才能继续执行。
# sychronzied和ReentrantLock

下面来看个例子

```java
public class Counter{
    private final Lock lock=new ReetrantLock();
    private int count;
    
    public void add(){
        lock.lock(); //这是加锁的关键步骤
        try{
            count+=n;
        }finally{
            lock.unlock();
        }
    }
}
```
和synchronized不同的是,ReetrantLock可以尝试获取锁
```java
if(lock.tryLock(1,TimeUnit.SECONDS)){
    try{
        //如果超过1秒仍未获取到锁，那么就会返回false。
    }finally{
        lock.unlock();
    }
}
```
ReentrantLock和synchronized都是可重入锁。

ReentrantLock获取锁更加安全。

# Condition
**和ReetrantLock配合使用,相当于synchornized搭配wait()和notify()**。

```java
class TaskQueue{
	private final Lock lock=new ReentrantLock();
    private final Condition condition=lock.newCondition();
    
    private Queue<String> queue=new LinkedList<>();
    
    public void addTask(String s){
        lock.lock();
        try{
            queue.add(s);
            condition.signalAll();
        }finally{
            lock.unlock();
        }
    }
    
    public String getTask(){
        lock.lock();
        try{
         	while(queue.isEmpty()){
                condition.await();
            }   
            return queue.remove();
        }finally{
            lock.unlock();
        }
    }
}
```

1. **使用Condition时,引用的Condition对象必须从Lock实例的newCondition()返回**,这样才能获得一个绑定了Lock实例的Condition实例。  
2. await()会释放当前锁,进入等待状态
3. signal()会唤醒某个等待线程
4. signalAll()会唤醒所有等待线程
唤醒线程从await()返回后需要重新获得锁
# 使用ReadWriteLock
设想这样一个场景:上面代码的写入要保证多线程的安全性,**但是当没有没有写入/修改数据时,这种加锁的方式就有点过于消耗性能**
ReadWriterLock就是用来解决这个问题的:
1.**只允许一个线程写入**(其他线程既不能吸入也不能读取);  
2.**没有写入时,多个线程允许同时读**(提高性能)
```java
public class Counter{
    private final ReadWriteLock rwLock-=new ReentrantReadWriteLock();

    private final Lock rlock=rwLock.readLock();

    private final Lock wlock=rwLock.writeLock();

    private int [] counts=new int[10];

    public void inc(int index){
        wlock.lock(); //写加锁
        try{
            counts[index]+=1;
        }finally{
            wlock.unlock();
        }
    }

    public int[] get(){
        rlock.lock(); //读加锁
        try{
            return Arrays.copyOf(counts,counts.length);
        }finally{
            rlock.unlock();
        }
    }
}
```
1. ReadWriteLock允许多个线程在没有写入时同时读取
# 使用线程池
上面的ReadWriteLock有一个缺陷就是如果线程正在读，那么写线程需要等待读线程释放锁以后才能获取写锁，即读的过程中不允许写，这是**悲观锁**
StampedLock与ReadWriteLock相比就是允许线程读的时候获取锁后写入！  
乐观锁的意思就是乐观地估计读的过程中大概率不会有写入，因此被称为乐观锁。悲观锁则在读的过程中拒绝写入，放在读到数据不一致。  
乐观锁通过检测机制来判断读取数据是否一致。  
```java
public double distanceFromOrigin(){
    long stamp=stampedLock.tryOptimisticRead(); //获取一个乐观读锁

    //下面针对x和y的不是原子操作
    double currentX=x;

    //如果发生写入，则(x,y)会变成其他值
    double currentY=y;
    //在读完y以后，可能会出现错误的值

    if(!stampedLock.validate(stamp)){//检查乐观读锁后是否有其他写锁发生
        stamp=stampedLock.readLock(); //获取悲观读锁

        try{
            currentX=x;
            currentY=y;
        }finally{
            stampedLock.unLockRead(stamp); //释放悲观锁
        }

        return Math.sqrt(currentX*currentX+currentY*currentY);
    }    
}
```
其实long stamp是所对象的版本号。通过validate()去验证版本号。
StampedLock把所细分为乐观锁和悲观锁，那么代价是什么:
1. 代码更加复杂
2. StampedLock是不可重入锁
# Concurrent集合
## CopyOnWriteArrayList
## ConcurrentHashMap
## CopyOnWriteArraySet
## ArrayBlockingQueue/LinkedBlockingQueue
## LinkedBlockingDeque
# Atomic
java.util.concurrent除了提供底层锁和并发集合外，还提供了一组原子操作的封装类。
# 使用线程池
主要的两个类是**ExecutorService**和**Executors**
前者用来表示线程池，后者通过newFixedThreadPool()方法来创建线程。
```java
//创建线程池
ExecutorService executor=Executor.newFixedThreadPool();

//提交任务
executor.submit(task1);
executor.submit(task2);
```
## 线程池有三种
### FixedThreadPool:线程数固定的线程池
### CachedThreadPool:线程数根据任务动态调整的线程池
### SingleThreadExecutor:仅单线程执行的线程池
```java
 public static void main(String[] args) {
        // 创建一个固定大小的线程池:
        ExecutorService es = Executors.newFixedThreadPool(4);
        for (int i = 0; i < 6; i++) {
            es.submit(new Task("" + i));
        }
        // 关闭线程池:
        es.shutdown();
    }
```
线程池使用完毕之后必须要关闭
shutdown()方法关闭时会等待当前任务完成再关闭。
## 动态限制线程池的大小
```java
public static ExecutorService newCachedThreadPool(){
    return new ThreadPoolExecutor(0,Integr.MAX_VALUE,60L,TimeUnit.SECOONDS,new SynchronousQueue<Runnable>());
} 
```
## ScheduledThreadPool定时任务
```java
ScheduledExcutorService ses=Executors.newSchduledThreadPool(4);

//1秒后执行一次任务
ses.schedule(new Task("one-time"),1,TimeUnit.SECONDS);
```
### 固定的执行时间
```java
ses.scheduledAtFixedRate(new Task("fix-rate"),2,3,TimeUnit.SECONDS);
```
### 固定的间隔时间
```java
ses.scheduledWithFixedDelay(new Task("fixed-delay"),2,3,TimeUnit.SECONDS);
```
# Futue
## Callable接口
Runnable接口有个问题就是没有返回值。如果程序需要返回的话那么就得使用**Callable接口**。
```java
class Task implements Callable<String>{
    public String call()throws Exception{
        return longTimeCalculation();
    }
}
```
下面的示例展示了如何使用Future
```java
ExecutorService executor=Executor.newFixedThreadPool(4);
//定义任务
Callable<String > task=new Task();

//提交任务并获取Future
Future<String> future=executor.submit(task);
//从Future获取异步执行返回的结果
String result=future.get();//可能阻塞
```
## Future<V>接口表示一个未来可能会返回的结果
### 常用API
#### get()
获取结果，可能会等待
#### get(long timeout,TimeUnit unit)
获取结果，但只等待指定的时间
#### cancel(boolean mayInerruptIfRunning)
取消当前任务
#### isDone()
判断当前任务是否已完成
