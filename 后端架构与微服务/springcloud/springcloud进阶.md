**为什么写**
去年的时候我没有实际的工作经历,所以对于SpringCloud这个框架都是以学习为主,现在有了工作经历之后再来谈谈新的感受与理解。  
## nacos的相关概念
nacos和eruke一样，都是来用来提供服务注册功能的微服务组件,但是nacos也用做配置中心使用,以下是几个常见的概念  
1. data id
2. namespace
## 快速上手nacos
## 公司中的实际使用
首先由于是微服务项目,所以使用Nacos来作为配置中心
### 配置模板
```bash
server.port=35000
spring.application.name=system
ACTIVE=${spring.profiles.active}
spring.profiles.active=sit
## nacos
spring.cloud.nacos.config.shared-dataids=common-${spring.profiles.active}.properties,testJson2.properties
spring.cloud.nacos.config.namespace=${spring.profiles.active}
spring.cloud.nacos.config.prefix=${spring.application.name}
spring.cloud.nacos.config.file-extension=properties
spring.cloud.nacos.config.server-addr=192.168.1.61:8848
mybatis-plus.global-config.logic-not-delete-value=0
mybatis-plus.global-config.logic-delete-value=1
mybatis-plus.global-config.sql-injector=com.baomidou.mybatisplus.mapper.LogicSqlInjector
jsyh.pk.game.companyId=1067032163482787840
jsyh.pk.game.siteId=1067032163549896704
jsyh.pk.game.orgId=1067032163528925184


logging.level.root=info
logging.level.io.swagger.models.parameters.AbstractSerializableParameter=error
##mybatis-plus.configuration.log-impl=org.apache.ibatis.logging.stdout.StdOutImpl
```
### 添加自定义配置
现在Saas平台加入了许多自定义项目,为了满足客户的要求需要加入很多配置,所以想办法来解决添加多个用户自定义配置
#### 实现Nacos的Listener
参考链接: https://www.jianshu.com/p/7f7d903e657a    
nacos的Listener这个类,我们通过是实现这个类来返回指定DataId的数据 
#### shared-dataids
```bash
spring.cloud.nacos.config.shared-dataids=actuator.properties,log.properties
spring.cloud.nacos.config.refreshable-dataids=actuator.properties,log.properties
```
https://blog.csdn.net/weixin_43296313/article/details/120954890  
用逗号将多个配置隔开，同时还需要添加自动刷新的配置项refreshable-dataids