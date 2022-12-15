# 深入Web请求过程
## B/S网络架构
## 发起一个请求
## HTTP解析
### 浏览器缓存机制
## DNS域名解析
### C记录和A记录
总结:
1. 域名到IP地址的映射就是通过DNS服务来实现的
2. 多个域名之间的映射就是C记录
3. 通过trace命令来追踪DNS解析的详细过程
## CDN工作机制(Content Delivery Network)
### 负载均衡
#### 分布式CDN节点
### 回源???
回到原始的Web Server拿到资源???回到原始的Name Server
获取ip地址
# 深入分析Java I/O的工作机制
StreamDecoder 负责字节流和字符之间转换。
# 数据在传输过程中的编码问题
# Servelet
容器分级,Container容器,Engine容器,Host容器,Servlet容器,Context容器。Context容器管理的是Servlet的包装类Wrapper。 
## 从Servlet开始
书上的例子是Tomcat的example项目  
Tomcat类中的getInstance获取一个实例对象(Tomcat7之后内嵌)  
调用addWebapp(url,path) 其中url是tomcat中路径,而path是实际的物理路径  
在addWebapp方法中会创建一个**StandardContext**类型的实例对象,除了将上面的url和path设置为属性之外 还会新创建一个**ConextConfig**(负责整个Web应用的配置),并将ConextConfig添加到Context的Listener中(注册监听器),书上还提到了WebXmlListener这个监听器  
## Servlet启动过程
### 观察者模式(监听器就是实际例子)
观察者Listerner 被观察者 StandardContext对象(事件源)  
所有的容器都会实现Lifecycle接口(被观察者作为接口),也就是说Listener会监听多个容器的属性变化。
 ![](https://raw.githubusercontent.com/aryangzhu/blogImage/master/%E6%88%AA%E5%B1%8F2022-12-14%20%E4%B8%8B%E5%8D%887.05.31.png)  
 ContextConfig负责整个应用的配置,会执行init方法,会读取一系列的配置文件,它是在调用tomcat.addWebapp时被加入到容器中   
 在上面的流程走完之后Context就会执行startIntel方法,其中比较个人觉得比较重要的两个环节是读取资源配置和创建  ClassLoader。  
  
