## 介绍
Authentication(认证)、Authorization(授权)、Session Management(会话管理)、Cryptography(加密)被称为Shiro框架的四大基石。  
1. **Authentication(认证)**:用户身份识别，通常称为用户"登录"；
2. **Authorization(授权)**:访问控制。比如某个用户是否有某个操作的权限。
3. **Session Management(会话管理)**:特定于用户的会话管理，甚至在非web或EJB应用程序。
4. **Cryptography(加密)**:在对数据源使用加密算法加密的同时，保证易于使用。
同时支持以下功能 
web支持  
缓存  
并发性  
测试  
## Shiro的抽象概念
![image.png](https://i.loli.net/2021/05/25/nELBoQ3WhUMkO7H.png)
在概念层，Shiro 架构包含三个主要的理念:
Subject,SercurityManage和Realm  
下面的图展示了这些组件如何相互作用，我们将在下面依次对其进行描述。 
![选区_241.png](https://i.loli.net/2021/05/25/WUwX6sTDGA9ImSh.png)  
1. **Subject**:当前用户，Subject可以是一个人，但也可以是第三方服务、守护进程账户、时钟守护任务或者其他-当前和软件交互的任何事件。
2. **SecurityManager**:管理所有的Subject,SecurityManager是shiro架构的核心，配合内部安全组件共同组成安全伞。
3. **Realms**:用于进行权限信息的验证，我们自己实现。Realm本质上是一个特定的安全DAO:它封装与数据源连接的细节，得到shiro所需的相关数据。在配置shiro的时候，你必须指定至少一个REalm来实现认证(authentication)和/或授权(authorization)。
**注意**:我们需要实现Realms的Authenication和Authorization。其中Authenticaiton是用来验证用户身份，Authorization是授权访问控制，用于对用户进行的操作授权，证明该用户是否允许进行当前操作，如访问某个链接，某个资源文件等。
## Shiro认证过程
![选区_242.png](https://i.loli.net/2021/05/25/y4XnhmKaPv5Df9p.png)
通过文章的测试类来看里面最重要的代码就是subject.login(token);
程序运行之后可以通过isAuthenticatd:true表示登陆成功。
![选区_243.png](https://i.loli.net/2021/05/25/wb5F7W6GQ1mlavc.png)
流程如下:
1. 首先调用Subject.login(token)进行登陆，其会自动委托给SecurityManager，调用之前必须通过SecurityUtils.setSecurityManager()设置;
2. SecurityManager负责真正的身份验证者，他会委托给Authenticator进行身份验证;
3. Authenticator才是真正的身份验证者，Shiro API中核心的身份认证入口点，此处可以进行自定义插入自己的实现;
4. Authenticator可能会委托给相应的AuthenticationStrategy进行多Realm身份验证，默认ModularRealmAuthenticator会调用AuthenticationStrategy进行多Realm身份验证;
5. Authenticator会把相应的token传入Realm，从Realm获取身份验证信息，如果没有返回/抛出异常表示身份验证失败了。此处可以配置多个Realm，将按照相应的顺序及策略进行访问。
**Shiro授权过程**
![选区_244.png](https://i.loli.net/2021/05/25/YLXjT4EnOepuy2M.png)

```Java
subject.checkRoles("admin","user");
```
上面代码会进行admin和user这两个角色权限的校验，如果没有则会报错。
**自定义Realm**
我们可以在合适的包下创建一个MyRealm类，继承Shiro框架下的AuthorizingRealm类，并实现默认的两个方法:
```java
/**
* 授权
*
* @param principalCollection
* @return
*/
@Override
protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principalCollection){
String username=(String)principalCollection.getPrimaryPrincipal();
//从数据库获取角色和权限数据
Set<String> roles=getRolesByUserName(userName);
Set<String> permissons=getPrimissionByUserName(userName);

SimpleAuthorizationInfo simpleAuthorizationInfo=new SimpleAuthorizationInfo();
simpleAuthorizationInfo.setStringPermissions(permissions);
simpleAuthorizaitonInfo.setRoles(roles);
return simpleAuthorizationInfo;
}
```
## 参考资料  
[知乎_54176956](https://zhuanlan.zhihu.com/p/54176956)
感觉讲得非常清楚。