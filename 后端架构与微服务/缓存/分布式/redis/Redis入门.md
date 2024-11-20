## 历史发展
### MySQL单机演变
单机读写压力，主要是读压力，所以需要多台服务器。
### 当今企业架构
数据服务也是以集群形式存在
### NoSQL
#### 键值
#### 文档
####  图
社交、搜索
#### 列数据
### 阿里巴巴的架构演进
## Redis简介
key-value存储系统，非关系型数据库，由ANSI C (动态C语言)编写。
### 特点
## 安装Redis
### ubuntu18.04安装
直接参照菜鸟教程即可
### Redis下的配置文件redis.conf
在启动时需要使用命令redis-server xxx.conf
xxx.conf就是我们的配置文件，后面尝试主从复制时需要复制一份**原文件**(单机伪集群)
我将这个文件放在了/usr/local/bin目录下
通过 nstat -ntlp|grep 6379来查看端口号
通过 ps -f|grep redis 
### 性能测试redis-benchmark
redis-benchmark -n 10000 -q
### 远程连接
redis-cli -h host -p port -a password
## Redis 数据类型
### 键
#### 选择数据库
默认有16个数据库，select index
#### 查看当前数据库大小
dbsize
#### 清楚当前数据库
清空当前数据库 flushdb|清空所有数据库 flushall
#### 检查是否存在 
EXIST key
#### 查看所有键
key *
#### 删除key
del key1 
#### 设置失效时间
expire key 10
#### 查看当前key的剩余时间
ttl key
#### 查看指定key的类型
type key
### String
#### 使用场景
1. 计数器
2. 统计多单位的数量
3. 粉丝数
4. 对象缓存存储
#### 设置值 
set k1 v1
#### 获得值 
get k1
#### 追加字符串
append k1 hello 如果不存在就相当于set
#### 获取字符串长度
strlen k1
#### 自增和自减
incr views|decr views
#### 设置步长自增自减
incrby views 10| decrby views 10
#### 截取字符串
getrang k1 0 3(获取字符串[0,3]的元素)
#### 替换指定位置开始的字符串
setrange k1 1 xxx(下标从0开始，同时后面的字符不会被替换)
#### 设置过期时间(set with expire)
setex k3 30 hello
#### 不存在在设置(set if not exist),分布式锁经常会用到
setnx mykey mongodb
#### 批量设置多个值(mulit set)
mset k1 v1 k2 v2
#### 批量获取多个值
mget k1 k2
#### 批量设置原子操作(并发中的情况)
msetnx k1 v1 k4 v4
#### 设置对象
set user:1{name:张三,age:13}
采用的是user:{id}:{field}
#### getset 先获取再存储
如果存在则返回原来的值并且设置新的值
### List
可以将List用作栈、队列、阻塞队列
#### lpush 添加一个或者多个值
lpush list one
lpush list two
#### rpush 从右边插入
#### lrange 获取区间的值
##### lrange 0 -1 获取所有的值
##### lrange 0 3 指定区间
#### lpop 移出list的第一个元素
lpop list
#### rpop 从右边移出list的最后一个元素
#### lindex list 根据下标获取元素
lindex  list 0
#### llen list 获取长度
#### lrem 删除指定个数的value
lrem list 1 one
#### ltrim 截取指定长度的元素
ltrim list 1 2 通过下标截取指定的长度,只剩下截取的元素
#### rpoplpush list1 list2 移除列表的最后一个元素，将他移动到新的列表中
#### lset 更新list当中存在的元素
list list 0 item 根据下标来更新
#### insert list before b xxx指定位置插入
### Set
#### 添加元素 
sadd myset ...
#### 查看集合中元素 smembers
查看是否存在某个元素 smember myset hello
#### 查看个数
scard myset 
#### 随机获取集合中的元素
sranmember myset 1
#### 随机删除集合中的元素
spop myset 1
#### 移动指定元素
smove myset myset1 a
#### 差集/交集/并集SDIFF/SINTER/SUNION
### Zset(sorted set)
#### 添加元素
zadd myset 1 one 2 two 3 three
#### 获取所有元素
zrange myset 0 -1
#### 排序
zrevrangebyscore key max in withscores limit offset count
##### 从大到小
zrevrangebyscore salary +inf -inf	## zrevrangebyscore 
##### 显示全部用户并且附带成绩
zrevrangebyscore salary +inf -inf withscores
#### 升/降序
zrange myzset 0 -1/zrevrange myset 0 -1
#### 获取指定区间元素个数
zcount myzset 1 4
### 哈希(Hash)
#### 设置字段值 
hset myhash field2 value2
##### 设置多个字段值
hmset myhash field2 value2 field3 value3
#### 获取多个指定的字段值
hmget myhash field1 field2
#### 获取全部的值
hgetall myhash
#### 删除指定的键
hdel myhash field1
#### 获取键值对的个数
hlen myhash
#### 判断hash中指定字段是否存在 
hexists myhash field1
#### 获取所有的key/value
hkeys/hvals
### 3种特殊的数据结构
#### geospatial地理位置
#### Hyperloger基数统计
基数在有穷集中就是个数，来源于无穷集(知乎)。
用途:网站的UV
#### Bitmaps位存储
用途:统计用户信息，活跃/不活跃等。本质上就是以二进制0和1来进行判断。
## 事务
Redis没有事务的隔离级别这个概念。Redis单条命令是原子性的但是事务并不是原子性的。
#### 开启与执行
multi 开启
set ...
exec 执行
#### 开启与取消
multi 开启
set ...
discard 取消
编译型异常(代码本身有问题) 事务不会执行
运行时异常 异常的这一行不会执行，其他的照常执行
## Redis实现乐观锁
#### 乐观锁
1. 认为什么时候都不会有问题,更新数据的时候去判断一下,在此期间是否有人修改过这个数据
2. 获取version
3. 更新的时候比较version
#### 悲观锁
## redis.conf配置
### 基本配置
### 快照配置
### 主从配置
### 持久化操作
#### RDB(Redis Database)
![](https://raw.githubusercontent.com/aryangzhu/blogImage/master/RDB.png)
#### 触发机制
save时触发rdb规则
flushall触发rdb规则
退出redis触发rdb规则
备份的快照 dump.rdb
**恢复时只需要将备份放在redis的启动目录下就可以**
#### 


