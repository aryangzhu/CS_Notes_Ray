## 网络编程基础
### 常见概念
#### OSI7层与TCP/IP模型
##### OSI
##### TCP/IP5层模型
两种网络模型时对一个概念或者抽象事物的两种理解,当然5层模型更加容易理解
## TCP编程
首先需要了解Socket这个**抽象概念**,一个应用程序通过一个Socket来建立一个远程连接,而Socket内部通过TCP/IP协议把数据传输到网络。   
Socket、TCP和部分IP的功能都是由操作系统提供的，不同的编程语言只是提供了对操作系统调用的简单封装。
### 为什么需要Socket?
因为同一台计算机某一时刻可能运行多个进程(多个应用程序),当应用程序接收到数据包的时候如果**只有IP地址没法判断应该发送给哪个应用程序**,所以操作系统抽象出Socket接口，每个应用程序对应到不同的Socket。
#### Socket的组成
IP地址+端口号(0-65535)
Socket编程本质上就是**两个进程之间的通信**。其中一个进程必须充当服务器,它会主动监听某个指定端口。客户端必须主动连接服务器的ip地址和端口号。
#### 服务器端
Java提供了**ServerSocket**来指定IP和端口的监听,同时需要一个**Handler**来处理Socket中的数据(一般流程就是拿到Socket里面的流对象进行处理)。  
#### 客户端
使用Socket(InetAddress,port)连接服务器
#### Socket流
TCP协议基于流,和I/O流类似
### 总结
1. 服务器端用**ServerSocket**监听指定端口  
2. 客户端使用**Socket**(InetAddress,port)连接服务器  
3. 服务器端用**accept()**接收并返回Socket  
4. 双方通过Socket打开InputStream/OutputStream读写数据  
5. 服务器端通常使用多线程同时处理多个客户端连接,利用**线程池**可大幅提升效率
6. **flush()**用于强制刷新缓冲区到网络
## HTTP编程
## Netty
### BIO、NIO和AIO
JDK中提供一个更加方便快捷的包,参考资料
https://bugstack.cn/md/netty/base/2019-08-01-netty%E6%A1%88%E4%BE%8B%EF%BC%8Cnetty4.1%E5%9F%BA%E7%A1%80%E5%85%A5%E9%97%A8%E7%AF%87%E4%B8%80%E3%80%8A%E5%97%A8%EF%BC%81NettyServer%E3%80%8B.html
### 服务端
### 
