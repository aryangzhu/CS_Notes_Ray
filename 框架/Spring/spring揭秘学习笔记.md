---
html:
    toc: true
    # number_sections: true
    toc_depth: 6
    toc_float: true
        collapsed: true
        smooth_scroll: true
--- 
## 时代发展
## IOC初入门
### 什么是IOC

IOC-Inversion Of Control,又名Denpendency Inject

从自己拿衣服到别人给你衣服

但是,你得通过某种方式来通知别人给你衣服

将代码调用者想象成个体的话,为你服务的角色就是IOC容器。

### 什么方式通知(依赖注入方式)

#### 构造方法注入

#### setter方法注入

#### 接口注入
![](https://raw.githubusercontent.com/aryangzhu/blogImage/master/spring%E6%8E%A5%E5%8F%A3%E6%B3%A8%E5%85%A5.png)
这种方式使用较少

#### IOC的附加值

## 掌管大局的IoC Service Provider

首先必须明确IoC Service Provider是一个**概念**,它的主要用途就是将业务对象Bean绑定在一起。

### IoC Service Provider的职责

#### 业务的创建与管理

#### 业务对象之间的依赖绑定

IoC Service Provider通过结合之前构建和管理的所有业务对象，以及各个业务对象之间可以识别的依赖关系，将这些对象所依赖的对象注入绑定，从而保证每个业务对象的依赖关系。

### IoC Service Provider如何管理对象间的依赖关系
#### 直接编码方式
#### 配置文件
#### 元数据方式
基于java5的注解和Generic的基础上开发的框架。
## Spring的IoC容器之BeanFactory
BeanFactory和ApplicationContext是有区别的,主要区别就是**BeanFactory主要是IoC Service Provider功能的实现**,而Application承担的更多例如Web场景、Aop场景。

### BeanFactory的对象注册和依赖管理
既然是IoC Service Provider的实现，那么就需要和IoC Service Provider一样三种实现。
#### 直接编码方式
BeanFactory只是一个接口,具体的实现由子类**DefaultListableBeanFactory**来实现,同时DefaultListableBeanFactory又实现了BeanDefinationRegistry接口,该接口才担任Bean组件的注册管理的角色。
**在容器中每一个受管的对象都会有一个BeanDefination实例**,包含了该对象的class类型、是否是抽象类、构造方法等属性。
#### 外部配置文件
这里以properties文件为例
```java
public static void main(String[] args){
    DefaultListableBeanFactory beanRegistry=new DefaultListableBeanFactory();
    BeanFactory container=bindFile(beanRegistry);
    FNewsListener fl=(FNewsListener)container.getBean("djFNewsListener");
    fl.method();
}

public static BeanFactory bindFile(BeanRegistry registry){
    BeanDefinitionReader reader=new BeanDefinitionReader(registry);
    reader.loadBeanDefinition("classpath:..../../binding-config.properties");
    return (BeanFactory)registry;
}
```
#### 注解方式
@Autowired是最重要的一个注解。
### BeanFatory的xml(配置文件方式详细)
#### \<beans>和 \<bean>
\<beans>下面包含许多\<bean>,也包含许多属性
例如default-lazy-init、default-autowire、default-dependency-check等等。
#### \<alias>、\<description>和\<impot>
前者是为了起别名
```xml
<alias name="dataSourceMasterDataBase" alias="masterDataSource">
```
\<import>是为了导入其他依赖模块
#### \<bean>的属性
##### id属性、name属性和class属性
id是bean组件在容器中唯一名称,name属性用来指定别名。
class指定\<bean>元素的类型 ,一般来说是必需的。
#### xml实现对象间的依赖
##### 构造方法注入
```xml
<constructor-arg index="0" type="int">
<!-- index用来指定第几个位置的参数，type用来指定参数类型 -->
    <ref bean="djListener">
</constructor-arg>   
```
##### setter注入
```xml
<property>
</property>
```
#####  \<constrcutor>和\<property>都能用的配置项(也就是说下面能写什么子元素)
###### value
###### ref
用来引用容器中其他对象实例
三个属性
local当前配置文件
parent 当前容器的父容器中的对象引用
bean 基本通吃
\<idref>
内部\<bean>
\<list>
\<set>
##### depends-on
如果没有ref来指明依赖的话就是用这个属性
##### autowire
直接自动绑定依赖,无需在xml中手动编写依赖,和@Autowire注解的功能一致。
```java
public class Foo{
    private Bar  attributeA;
}
public class Bar{
    ...
}
``` 
```xml
<bean id="fooBean" calss="...Foo" autowire="byType">
</bean>
<bean id="atrributeA" class="Bar"></bean>

```
###### no
###### byName
###### byType
###### constructor
##### dependency-oncheck
##### lazy-init
#### 关于继承
parenet属性
\<bean id="" parent="">
#### bean的scope(范围,也就是作用域)
##### singleton
容器中只有一个实例,声明周期一直伴随IoC容器结束。
##### protype
每次接到该类型对象的请求时，都会重新创建一个对象放回。
##### request、session和global session
web场景下才有
#### 工厂方法与BeanFactory
##### 静态工厂方法
为什么用工厂？
有时候需要依赖第三方的库
\<bean id="foo">
    <property name="barInterface">
        <ref bean="bar">
    </property> 
\</bean>

\<bean id="bar" class="....Factory" factory-method="getInstance">\</bean>
```java
public class StaticFatoryBean{
    public static  BarInterface getInstance(){
        return new BarInterfaceImpl();
    }
}
```
##### 非静态工厂方法(instance Factory Mehod)
\<bean id="bar" factory-bean="barFactory" factory-method="getInstance">
\</bean>

非静态是以factory-bean属性来指定工厂方法所在的工厂类实例。
##### FactoryBean
不要和容器名称BeanFactory搞混，FactoryBewan是Spring容器提供的可以扩展容器实例化的接口。
直接看个例子

```java
public class NextDateFactoryBean {
    public Object getObject() throws Exception{
        return new DateTime().plusDay(1);
    }
}
```

\<bean id="nextDate" class="NextDateFactoryBean">
\</bean>

可以和之前的静态方法和非静态方法做个对比
#### 偷梁换柱之法(修改默认配置)
有的时候声明了protype，但是当从另一个类中getBean()得到的仍然是singleton。
主要是针对prototype(原型)和singleton(单例)的场景做了分析，如果我们需要的是原型应该怎么配置xml。
让我想起了SpringBoot中@Configuration(ProxyBeanMethod="xxx")有着异曲同工之妙。
##### 方法注入
为了解决请求的对象是原型的问题。
方法要求能被子类实现或者是复写,Spring会使用Cglib对方法注入的对象动态生成一个子类实现，从而代替对象。
Spring还有其他的机制来帮我们解决这个问题
```xml
<bean id="mockPersister" class="...impl.MockNewsPersister">
    <look-up-method name="getNewsBean"
    bean="newsBean"
     />
</bean>
```
##### BeanFactoryAware接口
只要使用了这个接口，那么在会在当前实例中注入BeanFactory，所以我们就能直接使用BeanFactory.getBean(xxx.class);
这个时候Bean就是原型的。
##### 方法替换
### 容器背后的秘密
#### IoC容器的两个阶段
下面这个图很重要  
![](https://raw.githubusercontent.com/aryangzhu/blogImage/master/%E5%AE%B9%E5%99%A8%E5%8A%9F%E8%83%BD%E5%90%84%E4%B8%AA%E5%AE%9E%E7%8E%B0%E7%9A%84%E9%98%B6%E6%AE%B5.png)
##### 启动阶段
###### 加载配置
加载Configuration Meta
分析配置信息
通常是利用BeanDefinitionReader分析
###### 装备到BeanDefinition
###### 其他后处理
将BeanDefinition注册到BeanDefinitionRegistry  
**插手**容器的启动  
通过BeanFactoryProcessor接口及其实现类来完成。
试想这样一个场景:
当BeanDefiniation被创建之后如果要对其中的属性进行修改该如何操作(假设有配置生成的Bean)。  
BeanFctoryProcessor的一个实现类**BeanFatoryPropertyPlaceholderConfigure**就是将**占位符**${jdbc.url}这类属性(注意:生成的BeanDefiniton中的jdbcUrl属性还是占位符形式)与配置文件properties中的值进行填入。  
BeanFactory的另一个实现类**BeanFactoryPropertyPropertyOverrideConfigure**可以将配置文件中的属性进行替换，这里BeanFactoryPropertyProperyOverrideConfigure有对应的配置文件，可以将Bean里面的属性值替换掉。  
**CustomEditorConfigure与自定义PropertyEditor**
从xml中读取出来的都是字符串形式，如何将其转化为一个对象是也是很重要的一项职责。  
Spring中提供了很多的propertyEditor来帮助我们完成这一转化。  
但是在ApplicationContext的时候，由于Application本身就具有ClassLoader的功能，所以对于Resource类型来说，不用我们自己编写代码。而在BeanFactory中则需要字节编写代码来完成自定义PropertyEditor的职责。
##### 实例化阶段
对于BeanFactory来说调用getBean()方法或者隐式调用getBean()方法时就会启动。  
ApplicationContext则是启动完成就会实例化。
###### 实例化对象
检查是否被初始化，如果没有就根据BeanDefinition提供的信息来创建对象实例。
###### 装配依赖
注入依赖
生命周期回顾
对象其他处理
注册回调接口
#### bean的一生(生命周期)
##### Bean的初始化与BeanWrapper
容器内部实例化使用了"策略"模式来决定何种方式初始化bean实例，**可选的策略有反射或者CGlib动态字节码来生成初始化相应的bean实例或者动态生成其子类**。默认情况下，容器内部采用的是CGlibSubClassingInstantiationStrategy。  
容器根据**BeanDefinition取得实例化信息**，再加上上面的CGlibSubClassingInstantantiationStrategy(从名字就可以看出生成字节码且是某个类的子类)就可以创建实例。但是，由于返回方式上有些"点缀"，所以不直接返回Bean而是BeanWrapper。  
下来的这段话很重要，也表明了为什么需要BeanWrapper的存在。  
BeanWrapper实现了实现了PropertyAccessor和TypeConVert接口，所以之前阶段定义的PorpetyEditorRegistry就是这个时候用的，也就是说启动阶段只负责加载了这些玩意儿，而在真正的实例化阶段属性和依赖设置的时候这些才会真正的起作用。
##### 各色的Aware接口
Aware接口的作用通常是将某个依赖注入到实例中。  
BeanNameAware将BeanName添加到当前实例中。  
ResourceContextAware将Application添加到当前实例中。  
##### BeanPostFactoryProcessor与BeanFactory
这个阶段常见的就是处理标记接口实现类Aware。
##### Initical阶段
检查是否初始化
是否有初始化方法
##### 后置处理
也是Processro机制的一种。
## ApplicationContext
### 统一资源加载策略
URL统一资源定位
#### Spring中的Resource
ByteResource
ClassPathResource
FileSystemResource
...
#### ResourceLoader
##### 默认实现DefalutResourceLoader
1.先检查是否以classpath:开头，如果是则使用ClassResource来封装，如果不是跳到2  
2.1先判断是否是url形式，如果是的话则使用URLResource封装，如果不是则抛出Exception  
2.2前面无法获取资源直接getResourceByPath(String)
##### FileSytemResourceLoader和FileSystemXmlResourceLoader
重写了GetResourceByPath()方法
##### ResourcePatternReslover
批量获得Resource
Resource[] getResource(String)
###### 实现类org.springframework.core.io.support.PathMatchingResourcePatternResolover
classpath:*/
#### ApplicationContext与ResourceLoader
首先我们来看先面的图  
![](https://raw.githubusercontent.com/aryangzhu/blogImage/master/ApplicationContext%E4%B8%8EResourceLoader.png)  
##### Application扮演ResourceLoader的角色
##### ResourceLoader类型的注入
这就跟我们之前提到的Aware接口的时候一样
##### Resource注入
Spring提供了针对Resource类型的PropertyEditor的实现到容器中。
##### ClassPathXmlApplication和FileSystemXmlApplication
两者的区别就是ClassPathXmlApplication及时pattern没有标明为classpath:,它也会到classpath下去找。