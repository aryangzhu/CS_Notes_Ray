# 创建型模式
关心如何创建对象，将对象的创建和使用分离
## 工厂方法
定义一个用于创建对象的接口，让子类决定实例化哪一个类。Factory Method使一个类的实例化延迟到其子类。
```java
public interface NumberFactory{
    // 创建方法:
    Number parse(String s);

    public static NumberFactory getFactory(){
        return impl;
    }

    static NumberFactory impl=new NumberFactoryImpl();
}
```
客户端
```java
NumberFactory factory=NumberFactory.getFactory();
Number result=factory.parse("123.4");
```
好处是调用者不关心子类实现，实现类可以发生更改。
# 结构行模式
如何组合各种对象以便获得更好、更灵活的结构
## 适配器模式
将一个类的接口转换成客户希望的另外一个接口，使得原本由于接口不兼容而不能一起工作的那些类可以一起工作。
# 行为型模式
涉及算法和对象间的职责分配
## 策略模式
将算法一个个封装起来，运行时可以灵活地使用任何一个算法。