## Spring到SpringBoot
首先回顾一下Spring能够做什么  
microservices 微服务  
reactive 响应式编程  
clond 分布式  
webapps web应用开发   
SpringBoot是为了解决SpringFrameWork中繁琐的配置而出现的。   
SpringBoot的优势可以直接看官网spring.io来进行查看。  
### 时代背景  
#### 微服务 
微服务是一种架构风格   
微服务就是将我们的应用拆分成多个服务,这些服务可以部署在不同的服务器上。  
通常是以业务来进行拆分,例如购物车服务和订单服务。   
服务之间通过Http进行通信。  
自动化部署机制独立部署。  
#### 分布式
由于服务可以自动化部署在不同的服务器上,所以出现了分布式的问题:  
远程调用   
服务发现  
负载均衡  
服务容错  
...
#### 云原生  
### SpringBoot入门
直接看官方文档就好  
## SpringBoot特点和配置  
### SpringBoot特点  
#### 依赖管理
使用了\<parent>标签的话,那么会自动默认为继承父依赖的版本号。  
官方的依赖模块都以sprinboot-starter-*来开头,如果我们自己想要创建应用模块的话就不能以这种形式开头。  =
和maven一样,\<denpendies>用来加载我们场景启动器。  
##### 版本仲裁  
引入的依赖默认都不可以不写版本号　 
##### 修改默认版本号  
查看spring-boot-dependencies里面规定当前依赖的版本用的 key。
在当前项目里面重写配置
```xml
    <properties>
        <mysql.version>5.1.43</mysql.version>
    </properties>  
```
#### 自动配置  
##### 配置Tomcat  
##### 自动配好SpringMVC   
##### 自动配好Web常见的功能,如:字符编码  
##### 默认的包结构  
### 容器功能  
#### 组件添加   
##### @Configuration  
这里需要注意的是@Configuration(ProxyBeanMethods=true/false)  
如果是true的话,那么对于这个配置类的@Bean注解修饰的方法来说  
MyConfiguration myconfiguration=run.getBean(MyConfiguration);
就会得到一个代理对象,后面无论调用多少次来说都会这个Bean实例。  
false则会获取一个普通的对象。  
##### @Bean/@Component/@Service/@Controller  
##### @ComponentScan/@Import  
@ComponentScan  
**指定要扫描的包**,将其添加到容器中???  
@Import(User.class)  
将指定的类在容器中添加、默认组件的名字就是全类名。  
##### @Conditional  
条件装配  
### 原生配置文件引入  
#### @ImportResource("classpath:beans.xml")
### 配置绑定
#### @ConfigurationProperties
##### 1.@Componnet+@ConfigurationProperties(prefix="mycar")
##### 2.@EnableConfigurationProperties+@ConfigurationProperties
### 自动配置原理入门
#### 引导加载自动配置类
##### @SpringBootConfiguration
表明这是一个配置类  
##### @ComponentScan
指定扫描的包
##### @EnableAutoConfiguration
###### @AutoCofigurationPackage
向容器中添加指定包下的组件(主包)
###### @Import(AutoConfigurationImportSelector.class)
通过一系列的方法加载jar包下的META-INF下的.    
factories文件中的配置类，但是具体生不生效根据条件装配。
#### 按需开启自动配置项
@ConditionalOnClass(User.class)
#### 修改默认配置
有**两种方式来修改**  
1. 自定义Bean  
2. 直接修改配置文件  @EnableConfigurationProperties(xxxProperties.class)中的属性值会发生更改。
#### 最佳实践
1. 自定义一个starter,然后从其他项目中引入依赖
2. 自定义一个包，在项目启动时将其注入到容器中