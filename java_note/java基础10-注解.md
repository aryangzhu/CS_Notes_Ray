## 使用注解

### 什么是注解

注解是放在Java的类、方法、字段或者说参数前面的一种特殊的注释(是给程序看的)

```java
@Resource("hello")
public class Hello{
    @Inject;
    int n;
    
    @PostConstrucrt
    public void hello(@Param String name){\
        System.out.println(name);
	}
    
    @Override
    public String toString(){
        return "Hello";
    }
}
```

### 注解分为三类

#### 1.编译器使用的注解

@override:让编译器检查该方法是否正确地实现了复写;

@SuppressWarnings:告诉编译器忽略此处的代码产生的警告。

#### 2.由工具处理.class文件使用的注解

例如有些工具在加载class的时候,对class做动态修改,实现一些特殊的功能。

#### 3.在程序运行期间能够读取的注解

例如,一个配置了@PostConstuct的方法会在调用构造方法后自动被调用(这是Java代码读取该注解实现的功能)。

## 定义注解

下面是最基本的格式

```java
public @interface Report{
    int type() default 0;
    String level() default "info";
    String value() default "";
}
```



### 元注解

有些注解可以修饰其他注解,常用的几个元注解:

#### @Target

用来指定注解应用于(能够修饰)代码的哪个部分,这个必须得有

类或者接口 ElementType.Type

字段 ElementType.FIFLD

方法 ElementType.METHOD

构造方法 ElemenType.CONSTRUCTOR

方法参数 ElementType.PARAMETER

来看一下下面的例子

```java
@Target(ElementType.Method)
public @interface Report{
    int type() default 0;
    String levle() default "info";
    String value() default "";
}
```

实际上@Target定义的是一个ElementType[]数组,只有一个元素时**忽略数组的写法**。

#### @Retention

另一个重要的元注解@Retention定义了Annotation的**声明周期**(我们自定义的注解通常需要设置为运行期,所以这个注解得写):
仅编译期:RetentionPolicy.SOURCE

仅class文件:RetentionPolicy.CLASS

运行期:RetentionPolicy.RUNTIME

如果@Retention不存在,则该Annotation默认为**CLASS**。

#### @Repeatable

#### @Inherited

使用@Inherited定义子类时候可以继承父类定义的Annotation。

@Inherited只对@Target(ElementType.TYPE)生效,并且只对class继承有效,对于interface无效

```java
@Interited
@Target(ElementType.TYPE)
public @interface Report{
    int type() default 0;
    String level() default "info";
    String value() default "";
}
}
```

定义步骤

1.基本格式 带默认值

2.使用元注解进行修改

​	2.1 使用@Target

​	2.2 使用@Retention

## 处理/读取注解

在代码中使用了注解以后如何发挥注解的功能呢？这就要用到反射的机制了。

下面来看一下如何读取RUNTIME类型的注解

所有的注解都继承自Java.lang.annotation.Annotation,因此读取注解需要使用反射API

我们知道反射体系中常用的类有Class、Field、Method和Constructor

那么判断某个注解应用于哪个地方(Target)就要使用下面这几种API

Class.isAnnotationPresent(Class)

Field.isAnnotationPresent(Class)

Method.isAnnotationPresent(Class)

Constructor.isAnnotationPresent(Class)

例如

```java
//判断@Report是否存在于Person类中
Person.class.isAnnotationPresent(Report.class);
```

使用下面这几种APi来读取Annotation:

Class.getAnnotation(Class)
Field.getAnnotation(Class)
Method.getAnnotation(Class)
Construcutor.getAnnotation(Class)

```java
//获取Person注解的@Report注解
Report report=Person.class.getAnnotation(Report.class);
int type=report.type();
String level=report.level();
```

使用反射API读取Annotation有两种方法

1.先判断Annotaiton是否存在

```java
Class cl=Person.class
if(cls.isAnnotationPresent(Report.class)){
    Report report=cls.getAnnotation(Report.class);
}
```

2.直接读取,如果report为null，则不处理注解

### 读取方法参数的注解

方法参数本身可以看成一个数组,而每个参数又可以定义多个注解,所以可以使用二维数组

```java
Class clazz=Person.class;
Method[] methods=clazz.getDeclearedMethod();
for(Method method:methods){
    String name=method.getName();
    if(name.equals("xxx")){
        Annotation[][] annos=method.getParameterAnnotations();
        Annotation[] annosOfName=annos[0];
        for(Annotation anno:annosOfName){
            if(anno instanceof Range){
                Range r=(Range) anno;
            }
            ...
        }
    }
}
```

### 使用注解

注解如何使用,完全由程序自己决定。例如,Jniit是一个测试框架,他会自动运行所有标记为@Test的方法。

@Range注解定义了一个String字段的规则:字段长度满足@Range的参数定义:字段长度满足@Range的参数定义

```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.FIELD)
public @interface Range{
    int min() default 0;
    int max() default 255;
}
```

在某个JavaBean中,我们可以使用注解

```java
public class Person{
    @Range(min=1,max=20)
    public String name;
    
    @Range(max=10)
    public String city;
}
```

我们需要自己编写代码来使用注解

```java
void check(Person person)throws IllegalArgumentException,ReflectOperationException{
  //遍历所有Field
  for(Field field:person.getClass().getFields()){
      //获取Field定义的@Range
      Range range=field.getAnnotation(Range.class);
      //如果@Range存在
      if(range!=null){
          //获取Filed的值
			Object value=field.get(person);
          //如果值是value
          if(value instanceof String){
              String s=(String) value;
              if(s.length()<range.min()||s.length()>range.max()){
                  throw new IllegalArgumentException("Invalid field"+field.getName());
              }
          }
      }
  }  
}
```

