## 场景一
今天我从当前项目的数据库中找了一张数据量最多的表,这张表是在测试服务器上的,不是正式版,所以我可以对其进行优化,来补充我的实际项目经验。
场景如下,a表有50w条数据,b表只有1k+数据
首先对数据库表数据量查询
```SQL
SELECT table_name, table_rows 
FROM information_schema.tables 
WHERE table_schema = 'cloud_point' 
ORDER BY table_rows DESC
limit 0,5;
```
1. 使用show profiling 
来查看sql执行时间或者使用navicat客户端工具直接查看
0.5s
2. 使用explain查看执行计划
结果发现两张表只有各自的主键
3. 想着建立索引,根据查询建立联合索引
没有效果,也没有用到索引
4. 然后联想到连接查询的成本重要的是什么
然后建立被驱动表关联字段
0.02s
5. 然后再观察执行计划,发现extra是using where,说明被驱动表还是全表扫描
思考
6. 由于where条件的字段联想到单表的优化所以思考添加查询字段的索引
时间0.004s
## 场景二
1. 由于到了新公司,有机会去接触到更加高的并发访问量,与之而来的是更大的访问压力，所以我想看看生产环境下的数据库QPS,至于为什么不去计算接口的QPS,因为我忘了怎么用jmeter
2. 使用计算公式  
Question/Uptime
```
show global status like 'Question%';
show global status like 'Uptime';
```
## 场景三
exam服务用户最终的答题结果这张表的数据量非常大，所以考虑使用分库分表插件进行优化
1. 查看表的容量
```
SHOW TABLE STATUS LIKE '表名'
```
其实也可以查看 du -h /var/lib/mysql/数据库名/表名.ibd数据库
2. 
## 方向
结合业务进行表字段拆分、索引优化、容量规划  
1. 提升QPS  
缓存、多线程、分布式协同和异步消费  
1. 读多写少的场景  
从DB优化到缓存  
1. 数据一致性要求不高的场景  
同步改成异步，举一反三当数据一致性要求高时，有什么好的解决方案   
1. 某个服务在某个时刻瞬时流量比较大  
直播和考试???这块儿讲不好就是自掘坟墓  
1. 报表多线程同步写入时如何确保安全性  
futu  
1. 事务的隔离级别innodb如何实现  
futu  
MySQL主从复制
