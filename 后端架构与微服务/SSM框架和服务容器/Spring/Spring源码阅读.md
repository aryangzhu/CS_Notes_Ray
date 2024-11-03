之前在网上看了Spring源码,但是对于我来说即使有流程图例,即使也看过Spring揭秘,但是源码对于我来说还是云里雾里,我在github上找到了small-spring项目,个人觉得结合源码阅读非常的nice。
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
这一章的重点是ApplicationContext
# Bean实例的初始化方法和销毁方法

# 感应容器(Aware)

# 单例模式/原型模式与FactoryBean

# 监听器(容器关闭与刷新???)在Spring中的应用
 Spring容器中监听器最先联想到的场景就是容器的刷新和关闭了,监听器其实就是观察者设计模式最常见的应用。   
 我们知道Spring的由于职责分离的设计导致UML图复杂(浅浅地吐槽一下,我在脑子里是真的记不下,可能平时不怎么用),该记还是得记,要不然面试的时候项目不行,技术学习的又不够底层, 跟人家面试官聊什么,又怎么赚到小钱钱   
 话说回来,Event、Lisenter、Multicaster和Publisher是Spring的监听器中重要的4个部分,EventObject是jdk中提供的接口,event的翻译是事件,但是我觉得将其理解为通知更加合理一点。廖雪峰老师在讲观察者模式时举了一个例子,我理解之后复现如下  
 ```java
 //A关注商品是否涨价
class ListenerA implements Listener{
    //不写onEvent(evnet e)是为了展示演变过程
    public void onPublish(Product product){//String name
        System.out.printf(name+"涨价了");
    }
}
//B商品涨价之后想要出售
class ListenerB implements Listener{
    public void onPublish(Product product){//String price
        System.out.printf("以"+price+"的价格卖出");
    }
}
//被观察者,这里用的是Observerable
public class Observerable{
    private String name;
    private String price;

    private Set<Listener> listeners=new LinkedList<>();

    public void addListener(Listener listener){
        listeners.add(listener);
    } 

    public Observerable(String name,String price){
        this.name=name;
        this.price=price;
    }

    public void changePrice(String price){
        this.price=price;
        //新建一个能将要通知的信息封装起来的类
        Product product=new Product(price,name);
        listener.forEach(a-a.onPublish(product));
    }
}
 ```
 从上面的示例中可以看到,其实无非就是将**关注的事件的信息和监听器需要的信息**封装到了一个类中,这也是我之前不理解的地方  
 继续再讲Spring,上面提到了Event、Listener,那么Multicaster又是什么呢？广播器,我的理解是由于Spring的抽象层次分,所以在被观察者中通知被分为了两个面向对象,一个是Multicaster接口(通知,抽出来一部分功能),一个是publisher(也就是传统意义上的事件发布),上面的代码实例中发布(changePrice)和通知(forEach)是在一块儿的,可能Spring的设计者也觉得这么设计毛病太大,所以将面向对象的思想又思维发散了一下(不是)。  
最后,还是得祭出UML图  
![](https://raw.githubusercontent.com/aryangzhu/blogImage/master/640.png)
如果只是这样够了吗，我觉得我还是没有讲清楚   
我删掉了AbstractApplicationMulticaster的文件,重新将这个类下的方法重写了一遍,除了显而易见的addListener和removeListener的方法之外,这个抽象类下还有一个方法那么就是getSupportListener(Event evevnt),这里的功能实现就是找到与Event匹配的listener,设计者是通过反射来匹配event所需要的listener,我感觉我描述的不是很清楚,给大家粘贴一段源码吧  
```java
Type actualTypeArgument = ((ParameterizedType) genericInterface).getActualTypeArguments()[0];
String className= actualTypeArgument.getTypeName();
//中间部分的代码省略
return eventClass.isAssignableFrom(event.getClass());
```
不得不说,反射真的是一个非常强大的功能
# Aop的实现
越是到了难啃的骨头的地方,反复的思考与实践就尤为重要,小傅哥的文章讲的非常的好,但是对于基础知识薄弱的同学,在没有了解一些概念之前,即使我们知道Spring做了大量的指责分离的工作来保证框架的扩展性,但是还是有一层似透非透的感觉
首先来看一下Aop的公民  
1. JoinPoint
切点
2. PointCut
根据这个来确定切点
3. Advice
针对切点的逻辑
4. Aspect
切面,多个切点和逻辑的封装
5. Wave和织入器
有可能是类加载器来完成class的动态修改,也可能是别的工具
6. 目标对象target
要切的目标
上面的这些是逻辑概念,但是在具体的实现上根据环境的不同也有所差异(有种梦回数据结构和算法的感觉)   
文章会持续更新...
参考博客
https://github.com/fuzhengwei/small-spring  
《Spring揭秘》  