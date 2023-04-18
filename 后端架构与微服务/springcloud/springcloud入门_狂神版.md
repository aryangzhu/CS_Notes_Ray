# 出现的问题
SpringCloud提供了一个完整的微服务生态下的解决方案
在使用微服务之后，会面临以下四个方面的问题:
1.如何发现服务?(网关)
2.服务之间如何通信？
3.服务如何治理？
4.服务发生问题怎么办？
# Rest服务通信
主要是服务之间进行通信，最常见的就是consumer和provider服务。通过学习视频，我觉得下面这端代码很清晰地展示了Rest服务通信。
```java
@RestController
public class ConsumerController{
    
    @Autowired
    private RestTemplate template;


    //在后面使用ribbon之后，生产者不关心具体调用哪个服务实例，所以这里直接设置为服务的Application名称。
    private static final String REST_URL_PREFIX = "http://SPRINGCLOUD-PROVIDER-DEPT";

    @GetMapping("/list")
    public List<Dept> getAll(){
        //这里的getForObject非常重要，有三个参数：url(生产者服务url),responseType,uriVariables(这是一个可变长参数)
        return template.getForObject(REST_URL_PREFIX+"/list",List.class);
    }
}
```
# Eureka服务注册中心
将服务都注册到分布式集群上，就像zookeeper一样，下面来看看步骤  
1.导入依赖  
2.配置application.yml   
```yaml
eureka:
  instance:
    hostname: eureka7001.com # Eureka 服务端的实例名称 
    #hostname: eureka7001.com # Eureka 服务端的实例名称 
  client:
    register-with-eureka: false #表示是否向eureka注册中心注册自己 对应监控界面Application
    fetch-registry: false # 为false，表示自己为注册中心 对应监控界面DS
    # 监控页面,
    service-url:
      # 单机配置:
      #defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/
      # 集群配置
      defaultZone: http://eureka7002.com:7002/eureka/,http://eureka7003.com:7003/eureka/

```
3.使用注解@EnableEurekaServer开启Eureka  
4.生产者服务注册到中心
```yaml
# Eureka 配置，服务注册到哪里
eureka:
  client:
    service-url:
      defaultZone: http://eureka7001.com:7001/eureka/,http://eureka7002.com:7002/eureka/,http://eureka7003.com:7003/eureka/
  instance:
    instance-id: springcloud-provider-dept8001 # 修改eureka上的默认描述信息
# info配置
info:
  app.name: springcloud-provider-8001
  compay.name: springcloud
```
启动之后，浏览器输入localhost:7001就可以看到配置的服务
# Ribbon负载均衡
负载均衡是一个概念，它的实现可以是想Lvs这种基于client端或者消费者这一级的，也可以是从调用中心到服务这一层的Nigix。  
下面来看一下如何配置Ribbon   
1.导入依赖  
2.application.yml  
3.配置config  
使用注解@LoadBalanced  
4.主启动类添加开启Ribbon
# Feign
Feign是对Ribbon做了一层封装，更加符合接口编程的风格。
```java
@RestController
public class DeptConsumerController {

    @Autowired
    private DeptClientService deptClientService = null;

    @RequestMapping("/consumer/add")
    public boolean add(Dept dept){
        return this.deptClientService.add(dept);
    }
    @RequestMapping("/consumer/get/{id}")
    public Dept get(@PathVariable("id") Long id){
        return this.deptClientService.queryById(id);
    }
    @RequestMapping("/consumer/list")
    public List<Dept> querAll(){
        return this.deptClientService.queryAll();
    }
}
```
# Hystrix服务熔断、降级和监视
## 服务熔断
首先需要明确的是，服务熔断是在服务端，当一个服务出现问题，链路断掉，断电、网络问题，这个时候就需要有一手后备的解决方案，服务熔断为此而生。   
1.导入依赖  
2.配置文件中编写相关配置项  
```yml
# Eureka 配置，服务注册到哪里
eureka:
  client:
    service-url:
      defaultZone: http://eureka7001.com:7001/eureka/,http://eureka7002.com:7002/eureka/,http://eureka7003.com:7003/eureka/
  instance:
    instance-id: springcloud-provider-dept8001 # 修改eureka上的默认描述信息
    prefer-ip-address: true # 隐藏ip
# info配置
info:
  app.name: springcloud-provider-8001
  compay.name: 现实

```
配置中基本没有什么变化，当然你也可以根据需求来更改  
3.编写fallbackMehod兜底
```java
   @GetMapping("/dept/get/{id}")
    @HystrixCommand(fallbackMethod = "hystrixGet") // 当出现问题，就会跳到备选方法
    public Dept get(@PathVariable("id") Long id){
        Dept dept = deptService.queryDeptById(id);
        if(dept==null){
            throw new RuntimeException("id=>"+id+",不存在");
        }
        return dept;
    }

    // 备选方案
    public Dept hystrixGet(@PathVariable("id") Long id){
        return new Dept()
                .setDeptno(id)
                .setDname("id=>"+id+",没有对应的信息，null~@Hystrix")
                .setDb_source("no this database in Mysql");
    }
```
4.开启Hystrix熔断功能
@EnableCircuitBreaker // 添加对熔断的支持
## 服务降级
当我们的某个服务在集群上被访问的过多时，可以将其他服务关闭，来节省资源给并发访问高的服务给以特殊照顾，这就是服务降级(降的是被声明关闭的那个服务)。  
注意:服务降级是在客户端(也就是消费者模块)  
1. 导入依赖  
2. 编写配置
```yml
# 开启feign降级 feign.hystrix
feign:
  hystrix:
    enabled: true
# Eureka配置
eureka:
  client:
    register-with-eureka: false #不向eureka中注册自己
    service-url:
      defaultZone: http://eureka7001.com:7001/eureka/,http://eureka7002.com:7002/eureka/,http://eureka7003.com:7003/eureka/

```
3. 在Controller层中其实并不需要进行更改，只需要在调用接口编写hystrix的fallbackMethod即可。
```java
@Component
@FeignClient(value = "SPRINGCLOUD-PROVIDER-DEPT",fallbackFactory = DeptClientServiceFallbackFactory.class)
public interface DeptClientService {
    @GetMapping("/dept/get/{id}")
    public Dept queryById(@PathVariable("id") Long id);
    @GetMapping("/dept/list")
    public List<Dept> queryAll();
    @PostMapping("/dept/add")
    public boolean add(Dept dept);
}

```
还需要自己动手实现一个fallbackFatory
```java
@Component
public class DeptClientServiceFallbackFactory implements FallbackFactory {

    @Override
    public DeptClientService create(Throwable throwable) {
        return new DeptClientService() {
            @Override
            public Dept queryById(Long id) {
                return new Dept()
                        .setDeptno(id)
                        .setDname("id=>"+id+"没有对应的信息，客户端提供了降级服务，这个服务现在已经被关闭")
                        .setDb_source("没有数据");
            }

            @Override
            public List<Dept> queryAll() {
                return null;
            }

            @Override
            public boolean add(Dept dept) {
                return false;
            }
        };
    }
}
```
记得要开启feign的场景。 
## 监视
我们需要可视化的工具来帮助我们查看服务是否出现问题以及并发的访问量如何。
1. 导入依赖
2. 编写配置
```yml
hystrix:
  dashboard:
    proxy-stream-allow-list: "*"
```
3. 在生产者服务中开启监视
需要在主类中添加一个Bean组件
```java
//增加一个servlet
    @Bean
    public ServletRegistrationBean servletRegistrationBean(){
        ServletRegistrationBean servletRegistrationBean = new ServletRegistrationBean(new HystrixMetricsStreamServlet());
        servletRegistrationBean.addUrlMappings("/actuator/hystrix.stream");
        return servletRegistrationBean;
    }
```
4. 访问路径  
  localhost:8001/hystrix/actuator/hystrix.stream
  # zuul网关
1. 导入依赖
eureka与zuul整合,即注册中心和网关整合
2. application.yml配置
```yml
spring:
  application:
    name: springcloud-zuul # 服务名称
eureka:
  client:
    service-url:
      defaultZone: http://eureka7001.com:7001/eureka/,http://eureka7002.com:7002/eureka/,http://eureka7003.com:7003/eureka/
  instance:
    instance-id: zuul9223.com # 修改eureka上的默认描述信息
    prefer-ip-address: true
info:
  app.name: meng-springcloud
  company.name: www.mwb.com

zuul:
  routes:
    mydept.serviceId: springcloud-provider-dept # 原来的服务名
    mydept.path: /mydept/** # 改成我们想设置的，代替服务名
  ignored-services: "*" # 隐藏全部的 ignore 忽略
  # prefix: 配置前缀
```
3. 编写启动类
```java
@SpringBootApplication
@EnableZuulProxy
public class ZuulApplication_9223 {
    public static void main(String[] args) {
        SpringApplication.run(ZuulApplication_9223.class,args);
    }
}
```
# SpringCloud-Config配置中心
场景:将微服务的配置从远程的配置中心进行独读取
1. 首先需要github新建仓库pull到本地
2. 然后编写相关配置，push到远程仓库
3. 在自己的服务中编写
   1.  编写bootstrap.yml文件(系统级别文件)
```yml
  spring:
  cloud:
    config:
      name: config-eureka
      label: master
      profile: dev
      uri: http://localhost:3344/ # 服务器的地址
```
   2. 编写applcation.yml文件
```yml
  # 用户级别的配置
  spring:
  application:
    name: springcloud-config-eureka-7001
```
   