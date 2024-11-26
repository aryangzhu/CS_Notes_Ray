## 介绍
隶属于ShardingSphere生态,本身是一个轻量级的Java框架,在应用程序层实现读写分离。
## 数据分片
## 读写分离
### 步骤
#### 1.引入依赖
#### 2.添加配置
```yaml
sharding:  
    jdbc:  
      sharding:  
        table-rules:  
          ## 配置分片规则，此处以用户表为例  
          - table-name: user  
            logic-table: user_${0..1}  ## 根据分片键的值决定真实的表名，${0..1}表示取模后的余数，将数据分片到两个表  
            key-generator:  
              type: SNOWFLAKE  
              column: id  
            ## 其他分片规则配置...
```
#### 3.数据源路由规则
```yaml
spring:  
  sharding:  
    master-slave:  
      route-algorithm-class: com.example.UserRouteAlgorithm  ## 配置路由算法类，实现自己的路由算法
```
#### 4.路由算法
```java
@Component  
public class UserRouteAlgorithm implements RouteAlgorithm<User> {  
  @Autowired  
  private DataSource dataSource;  // 注入数据源对象  
  @Override  
  public String route(List<String> keys) {  
    // 根据分片规则将keys列表中的数据路由到正确的数据库实例  
    // ... ... ...   
    return "";  // 返回路由结果，此处为示例代码，实际应用中需要根据具体情况返回对应的路由结果字符串。  
  }  
}
```
#### 5.Mapper
```java
@ShardingJdbcMapper(table = "user")  // 指定分片表实体类对应的表名，此处为示例代码，实际应用中需要根据具体情况进行修改。  
public interface UserMapper {  
  @Select("SELECT * FROM ${logicTable} WHERE id = #{id}")  // 查询指定id的用户信息，逻辑表名使用占位符${logicTable}来代替，id使用占位符#{id}来代替。
  public UserVo getUserById(@Param("id") Long id);
}
```