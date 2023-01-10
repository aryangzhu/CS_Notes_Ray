# 创建一个springboot项目(非官方文档)
选择SpringBoot Intlizer然后选择Java Web组件
# yml文件
目的是作为配置文件来使用
## 配置文件
作用:修改SpringBoot自动配置的默认值,因为SpringBoot在底层给我们配置好了。

例如

```
server.port=8081;
```

## yml描述

让我们先回顾一下xml长什么样子
### xml
```xml
<server>
    <port>8081<port>
</server>
```
### yaml
**yml以数据作为中心,而不是以标记语言为重点**

yml配置

```yml
server：
  prot: 8080
```

## yml基础语法

### 注意事项

1.空格不能省略。

2.以缩进来控制层级关系,只要是左边对齐的一列数据都是同一个层级的。

3.属性和值的大小都是十分敏感的。

### 字面量[数字,布尔值,字符串]

```
k: v
```

#### 注意

“”不会转义字符串里面的特殊字符,特殊字符会作为本身想表示的意思

比如:name:"kuang \n shen"，输出:kuang换行shen

''单引号会转义特殊字符

### 对象、Map(键值对)shi

```yml
#对象、Map格式
k:
	v1:
	v2:
```

缩进写法

```yml
student:
	name:qinjiang
	age:3
```

行内写法

```yml
student:{name:qinjiang,age:3}
```

### 数组

用-值表示数组中的一个元素,比如:

```yml
pets:
	- cat
	- dog
	- pig
```

行内写法

```yml
pets:[cat,dog,pig]
```
### 注入配置
@Value可以注入我们自己的配置值
# 多环境切换
```yaml
spring:
	profies:
		active:
```
# 数据校验
使用注解@Valid来完成数据校验
# Web开发
我们知道，在Spring框架中主要的和web相关的无非就是静态资源的配置和SpringMVC的相关配置。
## 静态资源导入
通过官方文档可以得知，自定义的配置主要可以放在resource文件下的几个文件里面(这个是由源码所决定的)。
## 首页和图标定制
自定义配置文件里面，我们可以自行修改。
# 如何写一个网站
## 尽量用前端模板
### 栅格
### 导航栏
### 侧边栏
### 表单
### 尾部
## 设计数据库(难点!)
## 前端让它能够自动运行,独立化工程
## 数据接口对接
### json
### 前后端联调
# 整合持久层
## 整合JDBC
## 整合Druid
## 整合Mybatis(重点)
1.在springboot.yaml文件编写数据库相关配置  
2.在dao/mapper包下使用注解来声明组件  
3.现在需要在resource下编写xml文件.  
# 权限&认证
## 整合SpringSecurity
建议直接阅读官方文档
https://www.docs4dev.com/docs/zh/spring-security/5.1.2.RELEASE/reference/jc.html

首先需要创建Config类来实现WebSecurityConfigurerAdapter
在配置类中我们需要做的工作有下面这两个步骤:
注:下面的代码是链式的，关于这里可以看书
### 1.授权
主要是设置什么权限能够访问什么页面
```java
protected void configure(HttpSecurity http) throws Excption{
	//设置路径访问权限
	http.authorizeRequest()
	.anMacters("/").permitAll()
	.antMacters("/level1/**").hasRoles("vip1");
	...

	//登录
	// http.login()，主要是对登录做一系列的设置，具体可以参考官方文档
	http.formLogin(); 

	//登出
	http.logout().logoutSuccessfulurl("/");

	//开启记住我，默认remeber-me
	http.remeberMe().remeberMeParameter("remeber-me");
}
```
### 2.认证
```java
protected void configure(AuthenticationManagerBuilder auth)throws Exception{
	//注意，这里要对密码进行加密，否则会报错
	auth.inMemoryAuthentication().passwordEncoder(new BCryptPasswordEncoder())
	.withUser("root").password(new BCryptPasswordEncoder().encode("123456")).roles("vip1","vip2","vip3")
	.and()
	.withUser("user").password(new BCryptPasswordEncoder().encode("123456")).roles("vip1");
}
```
踩过的坑
1.直接导入springsecurity5和thymleaf的依赖
并且在thymleaf模板加上下面这行代码
```html
<html lang="en" xmlns:th="http://www.thymeleaf.org"
      xmlns:sec="http://www.thymeleaf.org/extras/spring-security">
<head>
```
## 整合Shiro
1.导入依赖
2.配置文件
3.HelloWorld
### Shiro中经常使用的三个类
#### Subject
我们正在进行的认证和授权的对象,通过subject.getPrinpal()获取。
#### SubjectManager
安全管理器,所有与安全有关的操作都会与SecruityManager,与Subject的所有交互都会委托给SecruityManager;可以把Subject认为是一个**门面(???)**;
#### Realm
真正连接数据库并进行认证和授权的类。
### 常用API
Subject currentUser=SecurityUtils.getSubject();  
Session session=currentUser.getSession();  
currentUser.isAuthenticated();  
currentUser.getPrincipal();  
currentUser.hasRole("schwartz");  
currentUser.isPermitted("lightsaber:wield");  
currentUser.logout();  
### 项目使用
#### 1.配置Shiro相关的类即ShrioConfig
在这个类下我们主要是配置访问拦截以及工厂方法
```Java
@Bean(name="manage")
public DefalutWebSecurityManager(UserRealm userRealm){
	DefaultWebSecurityManager manager = new DefaultWebSecurityManager();
        manager.setRealm(userRealm);
        return manager;
}
@Bean
public ShiroFilterFactoryBean getShiroFilterFactoryBean(@Qulifier("manager") DefaultWebSecurityManager defaultWebSecurityManager){
	 ShiroFilterFactoryBean bean = new ShiroFilterFactoryBean();
	 bean.setSecurityManager(defaultWebSecurityManager);

	/**
	访问拦截一共有5种级别
	anno 任何人都可访问
	authc 必须得认证，这里提一下，登录时是先认证然后再授权访问页面的
	user 必须记住我
	perms 必须有某个资源的权限
	roles 必须是某个角色
	**/

	Map<String,String > map=new HashMap<>();
	Map<String,String > filterMap=new HashMap<>();
	map.put("/user/add","perms[user:add]");
	map.put("/user/*","authc");
	map.put("/admin/*","roles[amin]");
	bean.setFilterChainDefinitionMap(filterMap);

	//设置登录请求
	bean.setLoginUrl("/toLoging");

	//设置未授权请求
	bean.unauthorizedUrl("/login");

	return bean;
}
```
#### 2.Realm
下来就是Realm,来进行授权和认证操作
我们自己的类需要继承AuthorizeRealm这个类
先来看认证
令牌是从Controller传过来的，主要是对密码进行加密
```java
public  Authentication doGetAuthectication(AuthenticationToken token){
	//指令令牌进行了加密
	  UsernamePasswordToken passwordToken = (UsernamePasswordToken) token;
        User user = userService.getOne(new QueryWrapper<User>()
                .eq("username", passwordToken.getUsername()));
        if (user == null){
            return null;
        }
        return new SimpleAuthenticationInfo(user,user.getPassword(),"");
}
```
接着再来看授权
```java
public AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principal){
	        SimpleAuthorizationInfo authorizationInfo = new SimpleAuthorizationInfo();

			Subject subject=SecurityUitls.getSubject();
			User user=subject.getPrincipal();
			authorizationInfo.addStringPermission(user.getPerms());
			return authorizeInfo;
}
```
令牌需要在Controller中获取登录信息时指定。
# SpringBoot集成Swagger
1.导入swagger和swagger-ui的依赖
2.编写一个配置类，并声明@Coifiguration和@EnableSwagger2(其中有一个@Import,导入的是Swagger2DocumentationConfiguration)
## 详细配置
需要在配置类中自己编写接口相关信息
首先需要创建一个Docket，如果需要多个组的话那么就创建过个Docket。
```java
public Docket createRestApi(){
	return new Docket(DocumentationType.SWAGGER_2)
	.enable(swaggerShow); //在Swagger类中可以定义，也可以在配置文件中定义用@Value()注解读取数据。
	.apiInfo(apiInfo)
	.select()
	.apis(RequestHandlerSelectors.basePackage("")) //扫描什么位置的接口,从java包路径开始
	.paths(PathSelectors.any())//过滤路径
	.build();
}
```
编写apiInfo方法
```java
private ApiInfo apiInfo(){
	return new ApiInfoBuilder()
	.title("xxx")
	.description("xxx")
	.termsOfServiceUrl("")
	.contact("Luis chen")
	.version("1.0")
	.build();
}
```
# 异步任务
在Service层使用@Service注解
# 发送邮件
最重要的两个类是MailSender和MimeMessage
1.需要在自己的邮箱进行配置，qq邮箱开启POP3/SMTP服务
2.application.yml中进行配置
```yml
spring:
	emial:
		username:xxx
		password:xxx
		host:stamp.qq.com
		properties:{xxx:xxx}
```
3.编写Service
下面以发送带附件的为例(这就是复杂邮件，最重要的是JavaMailSender这个类)
```java
@Override
    public void sendAttachmentsMail(String to, String subject, String content, String filePath, String... cc) throws MessagingException {
        Message message=mailSender.createMimeMessage();

		MimeMessageHelper helper=new MimeMessageHelper(message,true);
		helper.setFrom(from);
		helper.setTo(to);
		helper.setSubject(subject);
		helper.setText(content,true);
		if(ArrayUtil.isEmpty(Cc)){
			helper.setCc(Cc);
		}
		FileSystemResource file=new FileSystemResource(new File(filePath));
		String fileName=filePath.substring(filePath.lastIndexOf(File.separator.));
		helper.addAttachment(fileName,file);
		mailSender.send(message);
    }
```
# 定时任务
@Secdule
# Redis缓存
## 本地缓存
1. 导入依赖
2. application.yml配置文件
3. RedisConfig文件编写，其中最重要的就是RedisTemplate
4. RedisUtil，用来存放我们处理缓存的常用方法
### RedisTemplate
操作字符串: redisATemplate.opsForValue()
操作Hash: redisTemplate.opsForHash()
操作List: redisTemplate.opsForList()
操作Set: redisTemplate.opsForSet()
操作ZSet: redisTemplate.opsForZSet()
### RedisTemplate与StringRedisTemplate
1. RedisTemplate是一个**泛型类**，而StringRedisTemplate不是，后者只能对键和值都为String类型的数据进行操作，而前者则可以操作任何类型
2. 两者的数据是不共通的，StringRedisTemplate只能管理StringRedisTemplate里面的数据，RedisATemplate只能管理RedisTemplate中的数据
### RedisConfig
#### 配置工厂
其实配置文件中已经进行了配置，我看的别人的源码进行了工厂的配置，猜测可能不需要进行配置
#### 实例化RedisTemplate组件，注入到容器中
核心方法:**initDomainRedisTemplate**(redisTemplate,redisConnectionFactory)
#### 设置Redis的序列化方式，并开启事务
```java
 /*
         * 设置 序列化器 .
         * 如果不设置，那么在用实体类(未序列化)进行存储的时候，会提示错误: Failed to serialize object using DefaultSerializer;
         */
        redisTemplate.setKeySerializer(new StringRedisSerializer());

        redisTemplate.setHashKeySerializer(new StringRedisSerializer());
        redisTemplate.setHashValueSerializer(new GenericJackson2JsonRedisSerializer());
        redisTemplate.setValueSerializer(new GenericJackson2JsonRedisSerializer());
        // 开启事务
		redisTemplate.setEnableTransactionSupport(true);
        // 将连接工厂设置到模板类中
        redisTemplate.setConnectionFactory(factory);
```
### 注入封装的RedisUtil
```java
/**
     * 注入封装RedisTemplate
     */
    @Bean(name = "redisUtil")
    public RedisUtil redisUtil(RedisTemplate<String, Object> redisTemplate) {
        RedisUtil redisUtil = new RedisUtil();
        redisUtil.setRedisTemplate(redisTemplate);
        return redisUtil;
    }
```
## 订阅和发布
Redis中有原生的命令publish channel message
以及subscribe channel来订阅消息
###  SpringBoot项目中实现Redis的订阅与发布
#### RedisConfig配置
##### 实例化RedisMessageListenerXContainer组件并注入到容器中
核心代码
```Java
RedisMessageListenerContainer container=new RedisMessageListenerContainer();
container.setConnectionFactory(connectionFactory);
container.addMessageListener(listenerAdapter,new PatternTopic(channel));
container.addMessageListener(listenerAdapter,new PatternTopic(channel));
```
#### 实例化两个监听方法
```java
@Bean(name="listennerAdapter")
MessageListenerAdapter listenerAdapter(ConsumerImpl consumer){
	return new MessageListenerAdapterr(consumer,"on message");
}
```
## 消息队列
### 生产者lpush
### 消费这rpop
## 分布式缓存
# SpringBoot自定义包
1. 创建maven项目  
2. pom文件中需要引入autoconfigure的依赖和maven打包插件,这两个是最基础的
其他的可以按需引入依赖  
3. 编写业务代码
4. mvn install打包到仓库，如果有需要的话上传至镜像仓库
5. 其他项目中pom中引入依赖
原理在另一篇文章中有提及到
# 整合RabbiMq
# SpringAop使用
今天看了公司的项目和mall项目,发现aop的两种使用方式
## 针对某个包的切点
@Pointcut("execution(public * com.macro.mall.tiny.controller.*.*(..))")
    public void webLog() {
}  
从上面可以看出,这种方案针对的是某个包下的类来进行切面处理
## 针对某个注解的切点
@Pointcut("@annotation(com.xxx.core.anno.LogOption)")
    public void logPointCut() {
}
这种方案需要先定义一个LogOption的注解,此后所有使用这个注解的方法都会引入切面  
