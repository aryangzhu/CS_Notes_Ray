# 什么是进程和线程

## 进程

进程是程序的一次**执行过程**,**是系统运行程序的基本单位**(任务管理器看到的说明程序正在运行),因此进程是动态的。系统运行程序即是一个进程从创建,运行到消亡的过程。

在Java中,我们启动main函数就是启动了一个JVM的进程,main函数所在的线程就是这个进程中的主线程。

## 线程

线程是比进程更小的**执行单位**。**一个进程在其执行的过程中可以产生多个线程**。与进程不同的是同类的多个线程**共享**进程的**堆**和**方法区**资源,但是每个线程之间切换工作时,负担要比进程小得多,也正因为如此,线程也被称作轻量级进程。

Java程序天生就是多线程程序,我们可以通过JMX来看一下一个普通的Java程序有哪些线程,代码如下:

```java
public class MutiThread{
    public static void main(String[] args){
        
        //获取Java线程管理MXBean
        ThreadMXBean threadMXBean=ManagementFactory.getThreadMXBean();
        
        //不需要获取同步的minitor和synchronizer信息,仅获取线程和线程堆栈信息
        ThreadInfo[] threadInfos=threadMXBean.dumpAllSThreads(false,false);
        
        //遍历线程信息,仅打印线程ID和线程名称信息
        for(ThreadInfo threadInfo:threadInfo){
            System.out.println("["+threadInfo.getThredId()+"]"+threadInfo.getThreadName());
        }
    }
}
```

上述程序输出如下(输出内容不同)：

```shell
[5] Attach Listener //添加事件
[4] Signal Dispatcher  //分发处理给JVM信号的线程
[3] Finalizer //调用对象的 finalize 方法的线程
[2] Reference Handler //清除 reference 线程
[1] main //main 线程,程序入口
```

从上面的输出内容可以看出:**一个Java程序的运行是main线程和多个其他线程同时运行**。

# 简要描述线程与进程的关系,区别以及优缺点

## 图解进程和线程的关系

这里用一下Guide的图片

![](https://gitee.com/aryangzhu/picture/raw/master/java/Java%E5%86%85%E5%AD%98%E5%8C%BA%E5%9F%9F.png)

从上面的图片可以看到,JDK1.8之后,方法区变为了**元空间**,但是每个线程有自己的程序计数器、虚拟机栈和本地方法栈。

线程是进程的更小执行单位,线程执行开销小,但不利于资源的保护管理,因为执行时会相互影响。

## 为什么程序计数器是私有的？

程序计数器主要有两个作用:

1.**字节码解释器通过改变程序计数器来来依次读取指令**,从而实现代码的流程控制,如:顺序执行、选择、循环、异常处理(说实话不是很明白)

2.在多线程的情况下,**程序计数器用于记录当前线程执行的位置**,从而当线程被切换回来的时候能够知道该线程上次运行到了什么位置。

需要注意的是,如果执行的是native方法,那么程序计数器记录的是undefined地址,只有执行的是Java代码时程序技术器记录的才是**下一条指令的地址**。
