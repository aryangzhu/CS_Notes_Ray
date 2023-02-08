之前在源码阅读网上看了Spring源码,但是对于我来说即使有流程图例,即使也看过Spring揭秘的学习,但是源码对于我来说还是云里雾里,我在github上找到了small-spring项目,个人觉得结合源码阅读非常的nice。
# BeanFactory与BeanDefinition
这就是IOC中最重要的两个角色,而Spring揭秘是从如何处理对象之间的依赖这个角度去看Spring框架的,其实不论从任何角度去观察或者说深入这个框架,都会发现它的强大之处  
# 将职责进行分离
从这一步开始开始代码就需要细细体会了    
如果没有UML图的话,那么很快就会忘记,根据功能将方法放在了不同的地方,这么理解可能更加地清晰  
从源码中可以看出抽象类既承担了一部分真正意义上的功能实现,同时也是抽象层次分离的重要一步  
![](https://raw.githubusercontent.com/aryangzhu/blogImage/master/Spring%E6%A1%86%E6%9E%B6%E9%A6%96%E6%AC%A1%E5%88%86%E5%B1%82.drawio.png)
此后的代码将在这个框架上添砖加瓦
# 进一步完善-创建Bean实例时自定义策略
类如果有自定义的构造方法的话,那么创建类的实例对象时就必须调用构造方法  
由于容器创建实例实例对象时使用了动态代理,所以自然而然就有两种选择  
1. JDK动态代理
2. cglib字节码  
这里使用了策略模式,如何体现的呢？
一般来说需要有个策略的接口,然后不同的策略由不同的类来实现,同时更合理的是要有策略的持有者即应用上下文  
https://www.liaoxuefeng.com/wiki/1252599548343744/1281319606681634
在这个环节中create(String name,BeanDefinition)方法持有策略接口,默认是字节码代理  
# 进一步完善-创建Bean实例时带构造参数

# 进一步完善-创建Bean实例时参数的属性填充

# Resource与ResourceLoader

# 插手容器的启动机制(Processor)和应用上下文(Context)

# Bean实例的初始化方法和销毁方法

# 感应容器(Aware)

# 单例模式/原型模式与FactoryBean

# 监听器(容器关闭与刷新???)在Spring中的应用

# Aop的实现

参考博客
https://github.com/fuzhengwei/small-spring  
《Spring揭秘》  