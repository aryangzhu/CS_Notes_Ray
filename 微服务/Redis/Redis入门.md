- [历史发展](#历史发展)
  - [MySQL单机演变](#mysql单机演变)
  - [当今企业架构](#当今企业架构)
  - [NoSQL](#nosql)
    - [键值](#键值)
    - [文档](#文档)
    - [图](#图)
    - [列数据](#列数据)
  - [阿里巴巴的架构演进](#阿里巴巴的架构演进)
- [Redis简介](#redis简介)
  - [特点](#特点)
- [安装Redis](#安装redis)
  - [ubuntu18.04安装](#ubuntu1804安装)
  - [Redis下的配置文件redis.conf](#redis下的配置文件redisconf)
  - [性能测试redis-benchmark](#性能测试redis-benchmark)
  - [远程连接](#远程连接)
- [Redis 数据类型](#redis-数据类型)
  - [键](#键)
    - [选择数据库](#选择数据库)
    - [查看当前数据库大小](#查看当前数据库大小)
    - [清楚当前数据库](#清楚当前数据库)
    - [检查是否存在](#检查是否存在)
    - [查看所有键](#查看所有键)
    - [删除key](#删除key)
    - [设置失效时间](#设置失效时间)
    - [查看当前key的剩余时间](#查看当前key的剩余时间)
    - [查看指定key的类型](#查看指定key的类型)
  - [String](#string)
    - [使用场景](#使用场景)
    - [设置值](#设置值)
    - [获得值](#获得值)
    - [追加字符串](#追加字符串)
    - [获取字符串长度](#获取字符串长度)
    - [自增和自减](#自增和自减)
    - [设置步长自增自减](#设置步长自增自减)
    - [截取字符串](#截取字符串)
    - [替换指定位置开始的字符串](#替换指定位置开始的字符串)
    - [设置过期时间(set with expire)](#设置过期时间set-with-expire)
    - [不存在在设置(set if not exist),分布式锁经常会用到](#不存在在设置set-if-not-exist分布式锁经常会用到)
    - [批量设置多个值(mulit set)](#批量设置多个值mulit-set)
    - [批量获取多个值](#批量获取多个值)
    - [批量设置原子操作(并发中的情况)](#批量设置原子操作并发中的情况)
    - [设置对象](#设置对象)
    - [getset 先获取再存储](#getset-先获取再存储)
  - [List](#list)
    - [lpush 添加一个或者多个值](#lpush-添加一个或者多个值)
    - [rpush 从右边插入](#rpush-从右边插入)
    - [lrange 获取区间的值](#lrange-获取区间的值)
      - [lrange 0 -1 获取所有的值](#lrange-0--1-获取所有的值)
      - [lrange 0 3 指定区间](#lrange-0-3-指定区间)
    - [lpop 移出list的第一个元素](#lpop-移出list的第一个元素)
    - [rpop 从右边移出list的最后一个元素](#rpop-从右边移出list的最后一个元素)
    - [lindex list 根据下标获取元素](#lindex-list-根据下标获取元素)
    - [llen list 获取长度](#llen-list-获取长度)
    - [lrem 删除指定个数的value](#lrem-删除指定个数的value)
    - [ltrim 截取指定长度的元素](#ltrim-截取指定长度的元素)
    - [rpoplpush list1 list2 移除列表的最后一个元素，将他移动到新的列表中](#rpoplpush-list1-list2-移除列表的最后一个元素将他移动到新的列表中)
    - [lset 更新list当中存在的元素](#lset-更新list当中存在的元素)
    - [insert list before b xxx指定位置插入](#insert-list-before-b-xxx指定位置插入)
  - [Set](#set)
  - [zset(sorted set)](#zsetsorted-set)
  - [哈希](#哈希)
# 历史发展
## MySQL单机演变
单机读写压力，主要是读压力，所以需要多台服务器。
## 当今企业架构
数据服务也是以集群形式存在
## NoSQL
### 键值
### 文档
###  图
社交、搜索
### 列数据
## 阿里巴巴的架构演进
# Redis简介
key-value存储系统，非关系型数据库，由ANSI C (动态C语言)编写。
## 特点
# 安装Redis
## ubuntu18.04安装
直接参照菜鸟教程即可
## Redis下的配置文件redis.conf
在启动时需要使用命令redis-server xxx.conf
xxx.conf就是我们的配置文件，通常需要复制一份原文件(单机集群)
我将这个文件放在了/usr/local/bin目录下
通过 nstat -ntlp|grep 6379来查看端口号
通过 ps -f|grep redis 
## 性能测试redis-benchmark
redis-benchmark -n 10000 -q
## 远程连接
redis-cli -h host -p port -a password
# Redis 数据类型
## 键
### 选择数据库
默认有16个数据库，select index
### 查看当前数据库大小
dbsize
### 清楚当前数据库
清空当前数据库 flushdb|清空所有数据库 flushall
### 检查是否存在 
EXIST key
### 查看所有键
key *
### 删除key
del key1 
### 设置失效时间
expire key 10
### 查看当前key的剩余时间
ttl key
### 查看指定key的类型
type key
## String
### 使用场景
1. 计数器
2. 统计多单位的数量
3. 粉丝数
4. 对象缓存存储
### 设置值 
set k1 v1
### 获得值 
get k1
### 追加字符串
append k1 hello 如果不存在就相当于set
### 获取字符串长度
strlen k1
### 自增和自减
incr views|decr views
### 设置步长自增自减
incrby views 10| decrby views 10
### 截取字符串
getrang k1 0 3(获取字符串[0,3]的元素)
### 替换指定位置开始的字符串
setrange k1 1 xxx(下标从0开始，同时后面的字符不会被替换)
### 设置过期时间(set with expire)
setex k3 30 hello
### 不存在在设置(set if not exist),分布式锁经常会用到
setnx mykey mongodb
### 批量设置多个值(mulit set)
mset k1 v1 k2 v2
### 批量获取多个值
mget k1 k2
### 批量设置原子操作(并发中的情况)
msetnx k1 v1 k4 v4
### 设置对象
set user:1{name:张三,age:13}
采用的是user:{id}:{field}
### getset 先获取再存储
如果存在则返回原来的值并且设置新的值
## List
可以将List用作栈、队列、阻塞队列
### lpush 添加一个或者多个值
lpush list one
lpush list two
### rpush 从右边插入
### lrange 获取区间的值
#### lrange 0 -1 获取所有的值
#### lrange 0 3 指定区间
### lpop 移出list的第一个元素
lpop list
### rpop 从右边移出list的最后一个元素
### lindex list 根据下标获取元素
lindex  list 0
### llen list 获取长度
### lrem 删除指定个数的value
lrem list 1 one
### ltrim 截取指定长度的元素
ltrim list 1 2 通过下标截取指定的长度,只剩下截取的元素
### rpoplpush list1 list2 移除列表的最后一个元素，将他移动到新的列表中
### lset 更新list当中存在的元素
list list 0 item 根据下标来更新
### insert list before b xxx指定位置插入
## Set
## zset(sorted set)
## 哈希