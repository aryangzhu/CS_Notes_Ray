- [新建一个线程](#新建一个线程)
  - [三种方式](#三种方式)
    - [extends Thread](#extends-thread)
    - [implements Runnbale](#implements-runnbale)
    - [匿名内部类](#匿名内部类)
- [线程的状态](#线程的状态)
  - [6种状态](#6种状态)
- [中断线程](#中断线程)
- [守护线程](#守护线程)
- [线程同步](#线程同步)
  - [sychronized加锁的三种方式](#sychronized加锁的三种方式)
    - [对象加锁](#对象加锁)
    - [静态方法加锁](#静态方法加锁)
    - [实例方法加锁](#实例方法加锁)
- [使用wait和notify](#使用wait和notify)
- [sychronzied和ReentrantLock](#sychronzied和reentrantlock)
- [Condition](#condition)
- [使用ReadWriteLock](#使用readwritelock)
- [使用线程池](#使用线程池)
## 新建一个线程

### 三种方式

#### extends Thread

#### implements Runnbale

#### 匿名内部类

new Thread(){

​	@override

​	public void ru(){

​		...

​	}

}

## 线程的状态

### 6种状态

新建 New

运行 Runnable

阻塞 Blocked

等待 Waitingshiyng

计时等待 Time Waiting

终止 Terminated

## 中断线程

如果线程处于等待状态时,暂停的话会抛出InterruptedException

## 守护线程



## 线程同步

### sychronized加锁的三种方式

#### 对象加锁

#### 静态方法加锁

#### 实例方法加锁

## 使用wait和notify

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

## sychronzied和ReentrantLock

下面来看个例子

```java
public class Counter{
    private final Lock lock=new ReetrantLock();
    private int count;
    
    public void add(){
        lock.lock();
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
        
    }finally{
        lock.unlock();
    }
}
```

ReentrantLock和synchronized都是可重入锁。

ReentrantLock获取锁更加安全。

## Condition

和ReetrantLock配合使用,相当于synchornized搭配wait()和notify()。

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

可见,使用Condition时,引用的Condition对象必须从Lock实例的newCondition()返回,这样才能获得一个绑定了Lock实例的Condition实例。

await()会释放当前锁,进入等待状态

signal()会唤醒某个等待线程

signalAll()会唤醒所有等待线程

唤醒线程从await()返回后需要重新获得锁

## 使用ReadWriteLock

设想这样一个场景:上面代码的写入要保证多线程的安全性,但是当没有没有写入/修改数据时,这种加锁的方式就有点过于消耗性能

ReadWriterLock就是用来解决这个问题的:

1.只允许一个线程写入(其他线程既不能吸入也不能读取);

2.没有写入时,多个线程允许同时读(提高性能)

```java
public class Counter{
    
}
```



## 使用线程池



