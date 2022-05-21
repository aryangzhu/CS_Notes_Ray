- [时代发展](#时代发展)
- [IOC初入门](#ioc初入门)
  - [什么是IOC](#什么是ioc)
  - [什么方式通知](#什么方式通知)
    - [构造方法注入](#构造方法注入)
    - [setter方法注入](#setter方法注入)
    - [接口注入](#接口注入)
    - [IOC的附加值](#ioc的附加值)
- [掌管大局的IoC Service Provider](#掌管大局的ioc-service-provider)
  - [IoC Service Provider的职责](#ioc-service-provider的职责)
    - [业务的创建与管理](#业务的创建与管理)
    - [业务对象之间的依赖绑定](#业务对象之间的依赖绑定)
  - [IoC Service Provider如何管理对象间的依赖关系](#ioc-service-provider如何管理对象间的依赖关系)
    - [直接编码方式](#直接编码方式)
    - [配置文件](#配置文件)
    - [元数据方式](#元数据方式)
- [Spring的IoC容器之BeanFactory](#spring的ioc容器之beanfactory)
  - [BeanFactory的对象注册和依赖管理](#beanfactory的对象注册和依赖管理)
    - [直接编码方式](#直接编码方式-1)
    - [外部配置文件、](#外部配置文件)
    - [注解方式](#注解方式)
  - [BeanFatory的xml(配置文件方式详细)](#beanfatory的xml配置文件方式详细)
    - [\<beans>和 \<bean>](#beans和-bean)
    - [\<alias>、\<description>和\<impot>](#aliasdescription和impot)
    - [\<bean>的属性](#bean的属性)
      - [id属性、name属性和class属性](#id属性name属性和class属性)
    - [xml实现对象间的依赖](#xml实现对象间的依赖)
      - [构造方法注入](#构造方法注入-1)
      - [setter注入](#setter注入)
      - [\<constrcutor>和\<property>都能用的配置项(也就是说下面能写什么子元素)](#constrcutor和property都能用的配置项也就是说下面能写什么子元素)
        - [value](#value)
        - [ref](#ref)
      - [depends-on](#depends-on)
      - [autowire](#autowire)
        - [no](#no)
        - [byName](#byname)
        - [byType](#bytype)
        - [constructor](#constructor)
      - [dependency-oncheck](#dependency-oncheck)
      - [lazy-init](#lazy-init)
    - [关于继承](#关于继承)
    - [bean的scope(范围)](#bean的scope范围)
      - [singleton](#singleton)
      - [protype](#protype)
      - [request、session和global session](#requestsession和global-session)
    - [工厂方法与BeanFactory](#工厂方法与beanfactory)
      - [静态工厂方法](#静态工厂方法)
      - [非静态工厂方法(instance Factory Mehod)](#非静态工厂方法instance-factory-mehod)
      - [FactoryBean](#factorybean)
    - [偷梁换柱之法(修改默认配置)](#偷梁换柱之法修改默认配置)
      - [方法注入](#方法注入)
      - [BeanFactoryAware接口](#beanfactoryaware接口)
      - [方法替换](#方法替换)
  - [容器背后的秘密](#容器背后的秘密)
    - [IoC容器的两个阶段](#ioc容器的两个阶段)
      - [启动阶段](#启动阶段)
        - [加载配置](#加载配置)
        - [装备到BeanDefinition](#装备到beandefinition)
      - [实例化阶段](#实例化阶段)
        - [实例化对象](#实例化对象)
        - [装配依赖](#装配依赖)
    - [插手容器启动的BeanFactoryPostProcessor机制](#插手容器启动的beanfactorypostprocessor机制)
      - [PropertyPlaceholderConfigurer](#propertyplaceholderconfigurer)
      - [PropertyOverrideConigurer](#propertyoverrideconigurer)
      - [CustomeEditorConfigurer](#customeeditorconfigurer)
    - [bean的一生](#bean的一生)
      - [Bean的实例化与BeanWrapper](#bean的实例化与beanwrapper)
      - [各色的Aware接口](#各色的aware接口)
## 时代发展
## IOC初入门
### 什么是IOC

IOC-Inversion Of Control,又名Denpendency Inject

从自己拿衣服到别人给你衣服

但是,你得通过某种方式来通知别人给你衣服

将代码调用者想象成个体的话,为你服务的角色就是IOC容器。

### 什么方式通知

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

## Spring的IoC容器之BeanFactory
BeanFactory和ApplicationContext是有区别的,主要区别就是BeanFactory主要是IoC Service Provider功能的实现,而Application承担的更多例如Web场景、Aop场景。

### BeanFactory的对象注册和依赖管理
既然是IoC Service Provider的实现，那么就需要和IoC Service Provider一样三种实现。
#### 直接编码方式
BeanFactory只是一个接口,具体的实现由子类DefaultListableBeanFactory来实现,同时DefaultListableBeanFactory又实现了BeanDefinationRegistry接口,该接口才担任Bean组件的注册管理的角色。
在容器中每一个受管的对象都会有一个BeanDefination实例,包含了该对象的class类型、是否是抽象类、构造方法等属性。
#### 外部配置文件、
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
#### bean的scope(范围)
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
##### 方法替换
### 容器背后的秘密
#### IoC容器的两个阶段
##### 启动阶段
###### 加载配置
加载Configuration Meta
分析配置信息
通常是利用BeanDefinitionReader分析
###### 装备到BeanDefinition
其他后处理
将BeanDefinition注册到BeanDefinitionRegistry
##### 实例化阶段
调用getBean()方法或者隐式调用getBean()方法时就会启动
###### 实例化对象
检查是否被初始化，如果没有就根据BeanDefinition提供的信息来创建对象实例
###### 装配依赖
注入依赖
生命周期回顾
对象其他处理
注册回调接口
#### 插手容器启动的BeanFactoryPostProcessor机制
注意:上面说的再多也只是一种机制。
场景:在实例化之前想要对BeanDefinition做一些修改。
##### PropertyPlaceholderConfigurer
使用占位符(placeholder)来编写xml,并将这些占位符代表的配置放在简单的properties文件中。
当第一阶段加载完配置信息时，BeanFactory中对象的属性信息还是占位符，但是当PropertyPlaceholderConfigurer作为BeanFactoryPostProcessor时就会加载properties文件中的数据进行相应替换。
##### PropertyOverrideConigurer
与上面不同的是用来修改xml的默认配置。例如，dataSource.maxActive=200
##### CustomeEditorConfigurer
从xml中读取的数据是String类型的，所以要进行转换，需要为每种对象提供一种PropertyEditor，Spring自带了许多完成这种功能的类
ClassEditor:根据String类型的Class名称，直接将其转换成相应的Class对象，相当于完成Class.forName(String)。
后面通过CustomEditorConfigurer注册自定义的PropetyEditor。
#### bean的一生
BeanFactory和ApplicationContext的区别
##### Bean的实例化与BeanWrapper
容器内部实例化使用了"策略"模式来决定何种方式初始化bean实例，可选的策略有反射或者CGlib动态字节码来生成初始化相应的bean实例或者动态生成其子类。默认情况下，容器内部采用的是CGlibSubClassingInstantiationStrategy。
容器根据BeanDefinition取得实例化信息，再加上上面的CGlibSubClassingInstantantiationStrategy就可以创建实例。但是，由于返回方式上有些"点缀"，所以不直接返回Bean而是BeanWrapper。
下来的这段话很重要，也表明了为什么需要BeanWrapper的存在。

##### 各色的Aware接口