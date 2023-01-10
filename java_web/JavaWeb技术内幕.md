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
 在上面的流程走完之后Context就会执行startIntel方法,其中比较个人觉得比较重要的两个环节是读取资源配置和创建ClassLoader。  
### Web应用的初始化过程
逐级寻找配置文件,然后将对应的属性值添加到Context,最终会创建一个WebXml对象,然后调用configurestart方法,创建servlet,filter和listener。将Servlet包装成为StandardWrapper添加进Context容器中。
### 创建Servlet实例
#### 创建对象
根据load-on-startup配置项的值来决定(之前有提到过web应用初始化的时候会有许多属性),如果这个值大于0,那么就是Context容器启动的时候就会创建Servlet。  
创建依赖的是StardardWrapper的loadServlet方法,这个方法会交给initManager对象一个servletClass,然后由initManager来创建实例对象。  
#### 初始化
StandardWrapper.init->调用Servlet.init,本质上就是wrapper的门面类作为ServletConfig传给Servlet。
## Servlet体系结构
从关联关系来看的话体系结构过于复杂不如就用对象传递过程来表示  
Servlet体系中重要的几个类:ServletRequest、ServletResponse和ServletConfig 
ServletConfig是在Servlet初始化的时候就被传进来  
ServletRequest和ServeltResponse是调用时才会被创建  
除此之外,还有一个ServletContext就是数据交互的场景  
### Request和Response
Request和Response都会经过转换才会成为HttpServletRequest和HttpServletResponse
### 如何工作
Tomcat中专门有个类Mapper来进行匹配,根据hostname和path来设置DataMapping属性。本质上就是将MapperListener作为监听器加入到每个子容器中,容器更改Mapper类中的map属性也会更改。
## Listener
主要分为两大类
EventListener和LifecycleListener
![](https://raw.githubusercontent.com/aryangzhu/blogImage/master/%E6%88%AA%E5%B1%8F2022-12-15%20%E4%B8%8A%E5%8D%8811.52.30.png)
前者针对某个属性更改执行,后者针对生命周期不同状态触发
## Filter
AppclitonFilterChain是核心(猜测也是在容器初始化的时候创建的),靠的是url-pattern来匹配,Chain里面有一个数组,里面添加的就是FilterConfig对象。
# Session与Cookie
为什么需要？
Http无状态,下次会话不知道上次的用户有没有更改
## Cookie
按照书上的说法是有两个版本的,version0和version1,但是Java Web Servlet支持的是version0。
## Session
# Tomcat的设计架构

