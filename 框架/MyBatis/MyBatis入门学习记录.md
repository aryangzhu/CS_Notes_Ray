参考狂神说mybatis系列教程
mybatis和jdbc、IO一样是持久化的手段;  
持久化的目的  
1.断电后保证重要数据不丢失。 
2.内存资源昂贵，需要持久化缓存到外存。 
持久层--简单来说就是为了操作数据库
mybatis是一个半自动化的ORM(object relationship mapping)-->对象关系映射
优点：
    1.简单易学  
    2.灵活  
    3.解除sql与程序之间的耦合  
    4.xml，动态sql  
# 项目搭建
##  1.搭建数据库
##  2.pom.xml导入jar包
```xml
    <dependency>
       <groupId>org.mybatis</groupId>
       <artifactId>mybatis</artifactId>
       <version>3.5.2</version>
    </dependency>
    <dependency>
       <groupId>mysql</groupId>
       <artifactId>mysql-connector-java</artifactId>
       <version>5.1.47</version>
    </dependency>
```
## 3.resource下核心配置文件
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
<environments default="development">
    <environment id="development">
        <transactionManager type="JDBC"/>
        <dataSource type="POOLED">
            <property name="driver" value="com.mysql.jdbc.Driver"/>
            <property name="url" value="jdbc:mysql://localhost:3306/mybatis?useUnicode=true&amp;useJDBCCompliantTimezoneShift=true&amp;useLegacyDatetimeCode=false&amp;serverTimezone=GMT%2B8&amp;characterEncoding=UTF8"/>
            <property name="username" value="root"/>
            <property name="password" value="123"/>
        </dataSource>
    </environment>
</environments>
<mappers>
    <mapper resource="mapper/UserMapper"/>
</mappers>
</configuration>
```

## 4.编写工具类SqlSessionFactory

```java
    查看官方文档
    private static SqlSessionFactory sqlSessionFactory;

   static {
   try {
       String resource = "mybatis-config.xml";
       InputStream inputStream = Resources.getResourceAsStream(resource);
       sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
  } catch (IOException e) {
       e.printStackTrace();
  }
  }

   //获取SqlSession连接
   public static SqlSession getSession(){
   return sqlSessionFactory.openSession();
  }
```
## 5.创建实体类
## 6.dao层mapper接口
## 7.resource文件下编写mapper.xml
## 8.编写测试，结果返回
# 面试
## $和#的区别
1. 符号类型
2. 防sql注入问题
#能够有效防止sql注入，而$不能
3. 参数替换位置
#{}在DBMS中,${}在DBMS外
4. 参数解析
#{}将传入的参数当做字符串，并添加'',例如userId='111';
而${}直接将其添加到sql中，如userId=111。
5. 用法
ORDER BY ${columnName}，这里MyBatis不会修改或转义字符串
${}方式一般用于传入数据库对象，例如传入表名
6. sql执行过程
#{}：编译好SQL后语句再去取值
${}：取值以后再去编译SQL语句