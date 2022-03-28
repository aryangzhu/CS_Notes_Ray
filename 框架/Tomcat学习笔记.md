### 1.概述
**Tomcat简单的说就是一个运行Java的网络服务器，底层是Socket的一个程序，也是servlet的一个容器**。
![选区_091.png](/img/bVcMVsM)
![选区_092.png](/img/bVcMVsR)

### 2.结构目录
   ![选区_093.png](/img/bVcMVsV)
   １．bin--启动和关闭tomcat的bat文件
   ２．conf--配置
        server.xml配置server相关的信息，例如端口号，主机（host)
   3.lib--放置运行tomcat需要的jar包
   4.logs--存放日志
   5.webapps--存放我们的web应用
   6.work工作目录--该目录存放server文件和.class文件

### 3.webapps目录详细说明（重点）
   ![选区_094.png](/img/bVcMVtW)
   这样设置站点目录是为了修改html文件作为站点的首页。 
   如果没有web.xml文件支持，无法满足需求。同时这个规范是约定俗成的。

   配置访问首页
    １．首先新建一个WEB-INF目录
    ２．在WEB-INF目录下创建一个web.xml,其中的代码可以其他现有的直接进行拷贝。
    3.web.xml中添加下面的代码

  ```java
  <welcome-file-list>  
  <welcome-file>helloword2.html</welcome-file>
  </welcome-file-list>
  ```

### 虚拟目录
​    1.如果所有web站点的目录都放在webapps下，可能导致磁盘空间不够用，也不利于web站点目录（其实就是网站的物理目录，也即是真实目录）的管理（假设存在很多web站点目录）
​    2.把web站点(由一组html文档、媒体文件及相关目录结构组成，注重的是信息的浏览)的目录分散到其他磁盘管理就需要配置虚拟目录（默认只有webapps下的目录才能被tomcat自动管理成一个web站点）
​    ３.把web应用所在的目录交给web服务器管理，这个过程称之为虚拟目录的映射。
 配置虚拟目录方法一
​    １．在其他盘符下创建一个web站点目录，并创建 WEB-INF目录和一个html文件。
​    ２．找到tomcat目录下的/conf/server.xml文件
​    ３．在server.xml中的节点添加如下代码。path表示的是访问时输入的web项目名，docBase是站点目录的绝对路径
​    `
​    <context path="/web1" docBase="D:
​    \web1"/>
​    `
​    访问配置好的web站点目录
  方法二
​    进入到conf\Catalina\localhost文件下，创建一个xml文件，该文件的名字就是站点的名字。
​    xml文件的代码如下，docBase是你web站点的绝对路径。
​    
   `<?xml version="1.0" encoding="UTF-8"?>`
  `<Context`
 `docBase="D:web1"`
`reloadable="true">`
`</Context>`
### 配置临时域名
ｗin 步骤：C:\Windows\System32\drivers\etc下，找到hosts文件
   ![选区_095.png](/img/bVcMW6A)
### 设置虚拟主机
   1.什么是虚拟主机
        多个不同域名的网站共存于一个tomcat中
   2.为什么需要用到虚拟主机
        例子：我现在开发了4个网站，有4个域名。如果我不配置虚拟主机，一个Tomcat服务器运行一个网站，我就需要4台电脑才能把4个网站运行起来。
  3.配置步骤
        在tomcat的server.xml文件中添加主机名
        

   1.  `<Host name="zhongfucheng" appBase="D:web1">`
    
2.   `<Context path="/web1" docBase="D:web1"/>`
    
3.   `</Host>`
![image.png](/img/bVcMW7f)
![选区_097.png](/img/bVcMW7g)
我们平时idea中运行的项目不在tomcat安装目录下webapps中，而在/.IntelijIdea/system中存放配置文件，在启动tomcat后运行catalina.sh，读取我们的配置文件，从而完成完整项目的一次部署。
![选区_116.png](https://i.loli.net/2021/02/21/uJoN5KblvtqOzjh.png)
![](https://i.loli.net/2021/02/21/aMusyCqA54TGehk.png)
也就是上面所述的虚拟目录的第二种配置方法
参考博客https://blog.csdn.net/hgffhh/article/details/84137878