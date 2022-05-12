# SSM框架学习总结

## Mybatis

### 1.CRUD操作及配置解析

核心配置文件mybatis-config.xml,这个下面包含许多的属性，详情可以参考狂神的笔记
生命周期和作用域
![选区_111.png](https://i.loli.net/2021/02/20/FEukKesylBG8Rbh.png)
SqlSessionFactoryBuilder的作用在于创建SqlSessionFactory,完成后

失去作用，SqlSessionFactoryBuilder实例的最佳作用域是方法作用域。

SqlSessionFactory的作用创建SqlSession接口对象，长期存在，同时为了

避免消耗资源，作为一个单例SqlSession是一个连接对象，存活在一

个业务请求中。
![选区_112.png](https://i.loli.net/2021/02/20/ouO3L4Ie8QNR7iT.png)

### 2.ResultMap及分页

mybatis中内置有日志工厂功能，会按顺序查找。如果一个都没有找到，日志功

能将禁用

例如log4j

1.导入log4j包

2.配置文件编写

3.setting设置日志实现

4.在程序中使用log4j进行输出

5.查看控制台输出

### 3.使用注解开发

### 4.一对多和多对一处理

### 5.动态SQL

### 6.缓存

一级缓存默认开启，SqlSession级别

二级缓存即全局缓存，基于namespace级别的缓存，一个名称空间，对应一个二级缓存

### 工作机制
1.一个回话查询到一条数据，这条数据就会被存储在一级缓存中

2.如果会话关闭，一级失效；开启二级的话，一级缓存中的数据被保存到二级中

3.新的会话查询信息，就可以从二级缓存中获取内容

4.不同的mapper查出的数据会放在自己对应的缓存中
![选区_113.png](https://i.loli.net/2021/02/20/GCu89qfvhXwdUWP.png)

## Spring

### 1.IOC理论推导

### 2.项目创建和配置

### 3.依赖注入（DI）denpendency Injection

依赖：指的是bean对象的创建依赖于容器，Bean对象的依赖资源

注入：指的是bean对象所依赖的资源，由容器来设置和装配

bean的作用域

1.singleton--容器中只存在一个实例对象

2.prototype--每次对应该bean请求都会创建一个对象

3.request--表示在一次请求中对应一个bean实例

4.session--http Session中对应一个实例，和request一样都是基于web的Spring ApplicationContext情形下有效

### 4.自动装配

1.在xml中显式配置

2.在java中显式配置

3.隐式的bean发现机制和自动装配

spring的自动装配需要从两个角度来实现，或者说是两个操作
1.组件扫描(componetn scanning)：spring会自动发现应用上下文中所创建的bean。

2.组件装配(autowiring)：spring自动满足bean之间的依赖，即IOC/DI;

xml装配可以参考狂神非常清晰

**@Autowired和@Resource都可以用来装配bean，Autowired先byType @Resource先byName**

@Autowired，默认按照类型装配，默认情况下要求依赖对象必须存在，如果允许null值，可以设置required属性为false;

@Resource，默认按照名称进行装配，名称可以通过name属性进行指定。如果没有指定name属性，当注解写在字段上，默认取字段名进行按照名称查找

### 5.使用注解开发

### 6.静态/动态代理模式

### 7.aop面向切面（oop的拓展）

### 8.整合mybatis

### 9.声明式事务

## SpringMVC

### 1.概述

### 2.项目创建和配置

### 3.RestFul和控制器

### 4.数据处理和跳转

结果跳转方式
ModelAndView
页面：{视图解析器}+viewName+{视图解析器后缀}
![选区_114.png](https://i.loli.net/2021/02/20/xOdKXh7MkzZvgYQ.png)

```java
public class ControllerTest1 implents Controller{
    public ModelAndView handleRequest(HttpServletRequest httpServletRequest,HttpServletResponse httpServletResponse){
        ModelAndView model=new ModelAndView();
        model.addObject("msg","TestController1");
        model.setViewName("test");
        return model;
    }
}
```

ServletAPI
1.通过HttpServletResponse进行输出
2.通过HttpServletResponse实现重定向
3.通过HttpServletResponse实现转发
@Controller
public class ResultGo{

```java
@RequestMapping("/result/t1")
  public void test1(HttpServletRequest req,HttpServletResponse rsp) throws IOException{
    rsp.getWriter().println("rsp");
}
```

}
SpringMVC
通过SpringMVC来实现转发和重定向-无视图解析器
转发
return "/index.jsp";
转发
return "forward:/index.jsp"
重定向
return "redirect:/index.jsp"
有视图解析器
转发
return "test";
重定向
return "redirect:/index.jsp"
数据处理

####　处理提交数据--参考狂神的博客

1.@RequestParam注解
2.提交的是一个对象
数据显示到前端
1.ModelAndView
2.ModelMap

```java
@RequestMapping("/hello")
public String hello(@RequestParam("username")String name,ModelMap model){
    model.addAttribute("name",name);//相当于req.setAttribute(name);
    return "hello";
}
```

3.Model

```java
@RequestMapping("/hello")
public String hello(@RequestParam("username")String name,Model model){
    model.addAttribute("name",name);//相当于req.setAttribute(name);
    return "hello";
}
```

乱码问题可以自定义一个过滤器

### 5.整合ssm框架

### 6.json交互

### 7.ajax研究

### 8.配置拦截器

### 9.工作常用文件的上传和下载