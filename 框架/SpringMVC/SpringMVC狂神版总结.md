看了狂神的博客，做一下学习记录

# 1.MVC
MVC是将Model(模型)、视图(view)、控制器(controller)的简写，是一种软件设计规范。
是将业务逻辑、数据、显示分离的方法来组织代码。
MVC主要作用是降低了视图与业务逻辑的双向耦合。

**MVC不是一种设计模式，MVC是一种架构模式**，当然不同的MVC存在差异。
Model：数据模型，提供要展示的数据，因此包含数据和行为，可以认为是领域模型或者JavaBean组件。现在分割开来，提供了模型数据查询和模型数据状态更新等功能，包括业务和数据。
View:负责进行模型的展示，一般就是我们见到的用户界面，客户想看到的东西。
Controller:接收用户请求，委托给模型进行处理（状态改变），处理完毕后把返回的模型数据返回给视图，由视图负责展示。
JSP+Servlet+JavaBean的模式

![选区_103.png](https://segmentfault.com/img/bVcNvRd)

# 2.发展

## 1.model1时代--两层，视图层和模型层，导致 jsp职责过重

![选区_104.png](https://segmentfault.com/img/bVcNvRN)


## 2.model2时代--基础MVC

![选区_105.png](https://segmentfault.com/img/bVcNvSs)



### 流程
1.用户发请求  
​2.用Servlet处理完毕，并调用对应的业务逻辑方法。  
​3.业务处理完毕，返回更新后的数据给Servlet。  
​4.Servlet转向到JSP,由JSP来渲染页面。  
​5.响应给前端更新后的页面。
# SpringMVC
## 1.概述
SpringMVC是spring framework的一部分，是基于Java实现MVC的轻量级Web框架。
### 特点：
1.轻量级，简单易学。
2.高效，基于请求相应的MVC框架
3.与Spring兼容性好，无缝缝合。
4.约定优于配置。
5.功能强大：restful、数据验证、格式化、本地化、主题等。
6.简洁灵活。

Spring的web框架围绕**DispatcherServlet**[调度Servlet]设计。

## 2.中心控制器
DispatcherServlet的作用是将请求分发到不同的**处理器**。
SpringMVC框架以请求为驱动，围绕一个中心Servlet分派请求提供其他功能，DispatcherServlet是一个实际的Servlet。

![选区_106.png](https://segmentfault.com/img/bVcNvYY)

### 原理  
当发起请求时被前置的控制气拦截到请求，根据请求参数生成代理请求，找到对应的实际控制器，控制器处理请求，创建数据模型，访问数据库，将模型响应给中心控制器，控制器使用模型与视图渲染结果，将结果返回给中心控制器，再将结果返回给请求者。

![选区_107.png](https://segmentfault.com/img/bVcNv0Q)

## 3.SpringMVC执行原理

![选区_108.png](https://segmentfault.com/img/bVcNv2U)

1.DispatcherServlet表示前置控制器，是整个SpringMVC的控制中心，用户发送请求，DispatcherServlet接受请求并拦截请求。
例如：http://localhost:8080/SpringMVC/hello
分成三部分来看
    http://localhost:8080服务器域名（主机名和服务端口号）
    SpringMVC部署在服务器上的web站点（web站点目录，tomcat中有学习过）
    hello表示控制器

2.HandlerMapping为处理映射。DispatcherServlet调用HandlerMapping,HandlerMapping根据请求url查找Handler。

3.HandlerExecution表示具体的Handler,其主要作用是根据url查找控制器，如上url被查找控制器为：hello。

4.HandlerExecution将解析后的信息传递给DispatcherServlet,如解析控制器映射等。

5.HandlerAdapter表示处理适配器，其按照特定的规则去执行Handler。

6.Handler让具体的Controller执行。

7.Controller将具体的执行信息返回给HandlerAdapter,如ModelAndView。

8.HandlerAdapter将视图逻辑名或者模型传递给DispatcherServelt.

9.DispatchrServlet调用视图解析器（ViewResolver）来解析HandlerAdapter传递的逻辑视图名。

10.视图解析器将解析的逻辑视图名传给DispatcherServlet。

11.DispatcherServlet根据视图解析器解析的视图结果，调用具体的视图。

12.最终视图呈现给用户。

# 4.项目创建

可分为配置和注解
配置
注解(推荐)
## 1.新建一个Module,添加web支持（可以选择maven模板）
## 2.由于maven可能存在资源过滤的问题，我们将配置完善。

```xml
<build>
 <resources>
 <resource>
 <directory>src/main/java</directory>
 <includes>
 <include>**/*.properties</include>
 <include>**/*.xml</include>
 </includes>
 <filtering>false</filtering>
 </resource>
 <resource>
 <directory>src/main/resources</directory>
 <includes>
 <include>**/*.properties</include>
 <include>**/*.xml</include>
 </includes>
 <filtering>false</filtering>
 </resource>
 </resources>
</build>
```

## 3.在pom.xml中引入依赖
包括spring框架核心库，SpringMVC、Servlet,JSTL等。
## 4.配置web.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
 xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
 version="4.0">
 <!--1.注册servlet-->
 <servlet>
 <servlet-name>SpringMVC</servlet-name>
 <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
 <!--通过初始化参数指定SpringMVC配置文件的位置，进行关联-->
 <init-param>
 <param-name>contextConfigLocation</param-name>
 <param-value>classpath:springmvc-servlet.xml</param-value>
 </init-param>
 <!-- 启动顺序，数字越小，启动越早 -->
 <load-on-startup>1</load-on-startup>
 </servlet>
 <!--所有请求都会被springmvc拦截 -->
 <servlet-mapping>
 <servlet-name>SpringMVC</servlet-name>
 <url-pattern>/</url-pattern>
 </servlet-mapping>
</web-app>
```

/和/*的区别：<url-pattern>/</url-pattern>不会匹配到.jsp，只针对我们编写的请求；即.jsp会出现返回jsp视图时再次进入到spring的DispatcherServlet类，导致找不到对应的controller所以报404.

注意事项：

    1.web.xml最新版

    2.注册DispatcherServlet

    3.关联SpringMVC的配置文件

    4.启动级别为1

    5.映射路径为/


## 5.添加SpringMVC配置文件
在resources下配置springmvc-servlet.xml,配置的形式与spring容器基本类似，为了支持基于注解的IOC,设置了自动扫描包的功能。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
 xmlns:context="http://www.springframework.org/schema/context"
 xmlns:mvc="http://www.springframework.org/schema/mvc"
 xsi:schemaLocation="http://www.springframework.org/schema/beans
 http://www.springframework.org/schema/beans/spring-beans.xsd
 http://www.springframework.org/schema/context
 https://www.springframework.org/schema/context/spring-context.xsd
 http://www.springframework.org/schema/mvc
 https://www.springframework.org/schema/mvc/spring-mvc.xsd">
 <!-- 自动扫描包，让指定包下的注解生效,由IOC容器统一管理 -->
 <context:component-scan base-package="com.kuang.controller"/>
 <!-- 让Spring MVC不处理静态资源 -->
 <mvc:default-servlet-handler />
 <!--
 支持mvc注解驱动
 在spring中一般采用@RequestMapping注解来完成映射关系
 要想使@RequestMapping注解生效
 必须向上下文中注册DefaultAnnotationHandlerMapping
 和一个AnnotationMethodHandlerAdapter实例
 这两个实例分别在类级别和方法级别处理。
 而annotation-driven配置帮助我们自动完成上述两个实例的注入。
 -->
 <mvc:annotation-driven />
 <!-- 视图解析器 -->
 <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver"
 id="internalResourceViewResolver">
 <!-- 前缀 -->
 <property name="prefix" value="/WEB-INF/jsp/" />
 <!-- 后缀 -->
 <property name="suffix" value=".jsp" />
 </bean>
</beans>
```

## 6.创建controller

```kotlin
package com.kuang.controller;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;
@Controller
@RequestMapping("/HelloController")
public class HelloController {
 //真实访问地址 : 项目名/HelloController/hello
 @RequestMapping("/hello")
 public String sayHello(Model model){
 //向模型中添加属性msg与值，可以在JSP页面中取出并渲染
 model.addAttribute("msg","hello,SpringMVC");
 //web-inf/jsp/hello.jsp
 return "hello";
 }
}
@Controller是为了让springIOC容器初始化时自动扫描到
@RequestMapping是为了映射请求路径，这里因为类与方法上都有映射所以访问的应该是/HelloController/hello
方法中Model类型的参数是为了把Action中的数据带到视图中。
方法返回的结果是视图的名称hello,加上配置文件的前后缀变成 WEB-INF/hello.jsp.
```

## 7.创建视图层

```gams
通过EL表示取出Model中存放的值，或者对象。
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
<title>SpringMVC</title>
</head>
<body>
${msg}
</body>
</html>
```

## 8.配置tomcat运行

