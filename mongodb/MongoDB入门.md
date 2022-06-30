- [NoSQL简介](#nosql简介)
  - [常见的概念](#常见的概念)
- [MongoDB](#mongodb)
  - [特点](#特点)
- [安装](#安装)
  - [ubuntu18.04安装MongoDB](#ubuntu1804安装mongodb)
    - [踩坑](#踩坑)
- [MongoDB术语解析](#mongodb术语解析)
  - [数据库](#数据库)
  - [文档](#文档)
  - [集合](#集合)
  - [元数据](#元数据)
  - [数据类型](#数据类型)
    - [String](#string)
    - [Date](#date)
    - [ObjectID](#objectid)
- [连接](#连接)
- [基本操作指令](#基本操作指令)
  - [创建数据库](#创建数据库)
  - [删除数据库](#删除数据库)
  - [创建集合(表)](#创建集合表)
  - [删除集合](#删除集合)
  - [插入文档](#插入文档)
  - [更新文档](#更新文档)
    - [update()方法](#update方法)
    - [save()方法](#save方法)
  - [删除文档](#删除文档)
  - [查询文档](#查询文档)
    - [pretty()](#pretty)
    - [findOne()](#findone)
    - [where 语句](#where-语句)
    - [AND条件](#and条件)
    - [OR条件](#or条件)
    - [AND与OR联合使用](#and与or联合使用)
  - [条件查询](#条件查询)
    - [范围查询](#范围查询)
  - [$type操作符](#type操作符)
  - [limit 与 skip](#limit-与-skip)
  - [](#)
# NoSQL简介
## 常见的概念
1. NoSQL(Not only SQL)
不只是数据库,用于超大规模的存储。
2. 分布式系统
多台计算机和通信的软件组件通过计算机网络连接组成。
3. RDBMS vs NoSQL
RDBMS:
高度组织化结构化数据
结构化查询语言-SQL
数据和关系都存储在单独的表中
严格的一致性
基础事务
NoSQL:
代表着不仅仅是SQL
没有声明性查询语言
键-值对存储，列存储，文档存储，图形数据库
最终一致性，而非ACID属性
非结构化和不可预知的数据
CAP定理
高性能、高可用和可伸缩性
# MongoDB
C++语言编写的**基于分布式文件存储**的开源数据库系统
将数据存储为一个**文档**
## 特点
1. MongoDB是一个面向文档存储的数据库，操作起来比较简单和容易。
2. 可以在MongoDB记录中设置任何属性的索引(如:FirstHName="Sameer",Address="8 Gandhi Road")来实现更快的排序
3. Map和Reduce。Map函数调用emit(key,value)遍历集合中所有的记录，将key与value传给Reduce函数进行处理
# 安装
## ubuntu18.04安装MongoDB
参考菜鸟教程即可
### 踩坑
libcurl.so.4: version `CURL_OPENSSL_3’ not found (required by ./bin/mongod)
解决方案:
sudo apt-get install libgconf-2-4
sudo apt-get install libcurl3
# MongoDB术语解析
SQL术语   MongoDB术语   解释
table          collection           数据库表/集合
row             document          数据记录行/文档
column      field                      数据字段/域
## 数据库
show dbs 显示所有数据的列表
db 显示当前数据库对象或集合
use local 连接指定数据库
## 文档
文档就是一组键值对
## 集合
集合就是MongoDB文档组，类似于表格
## 元数据
数据库信息存储在集合中，使用了系统的命名空间
## 数据类型
### String
字符串
**BSON数据类型**  
我的理解是MongoDB在JSON的基础上扩展了数据类型，下面提到的数据类型都是基于BSON的，也结识了为什么可以使用$type关键字来判断类型  
Integer  
Boolean   
Double  
Min/Max Keys  
Array  
TimeStamp  
Object  
Null  
### Date
表示当前距离 Unix新纪元（1970年1月1日）的毫秒数。日期类型是有符号的, 负数表示 1970 年之前的日期。
```js
> var mydate1 = new Date()     //格林尼治时间
> mydate1
ISODate("2018-03-04T14:58:51.233Z")
> typeof mydate1
object
```
### ObjectID
唯一主键，可以很快的生成和排序，包含12bytes
Binary Data
Code
Regular expression
# 连接
mongodb://[username:password@]host1[:port1][,host2[:port2],...[,hostN[:portN]]][/[database][?options]]
username:password@:可选项
host1:必填项,至少一个URI。如果是集群，指定多个主机地址
portX:可选的指定端口,如果不填,默认为27017
database:可选项，默认指定test数据库
?options连接选项，如果不用database，则前面需要加上/。所有连接选项都是键值对name=value
# 基本操作指令
## 创建数据库
use database_name
## 删除数据库
db.dropDatabase() 删除当前数据库
## 创建集合(表)
db.createCollection(name,options)
插入时会自己创建
db.mycol2.insert({"name":"菜鸟教程"})
WriteResult({ "nInserted" : 1 })
## 删除集合
db.collection.drop()
## 插入文档
db.collection_name.insert(document)
或者
db.colletion_name.save(document)
3.2之后有了insertOne()和insertMany()
## 更新文档
### update()方法
db.collection.update(
   \<query>,
   \<update>,
   {
     upsert: \<boolean>,
     multi: \<boolean>,
     writeConcern: \<document>
   }
)

示例
db.col.update({'title':'MongoDB 教程'},{$set:{'title':'MongoDB'}})
### save()方法
db.collection.save(
   \<document>,
   {
     writeConcern: \<document>
   }
)

save方法通过传过来文档进行更新
```js
db.col.save({
    "_id" : ObjectId("56064f89ade2f21f36b03136"),
    "title" : "MongoDB",
    "description" : "MongoDB 是一个 Nosql 数据库",
    "by" : "Runoob",
    "url" : "http://www.runoob.com",
    "tags" : [
            "mongodb",
            "NoSQL"
    ],
    "likes" : 110
})
```
## 删除文档
db.collection_name.remove(\<query>,\<justOne>)
示例
db.col.remove({'title':'MongoDB 教程'})
## 查询文档
db.collection.find(query,prejection)  
query:查询条件  
project:可选,使用投影操作符指定返回的键。
### pretty()
以易读方式来来读取数据,可以使用pretty()方法  
db.col.find().pretty()
### findOne()
### where 语句
都是通过find()
```
等于 {<key>:<value>}  
小于 {<key>:{$lt:<value>}} 
小于或者等于{<key>:{$lte:<value>}}  
大于:{<key>:{gt:<value>}}
大于等于$gt
不等于 {<key>:${ne:<value>}}
```
### AND条件
用逗号隔开  
 db.col.find({"by":"菜鸟教程", "title":"MongoDB 教程"}).pretty()
### OR条件
使用关键字
$or  
db.col.find(
   {
      $or: [
         {key1: value1}, {key2:value2}
      ]
   }
).pretty()
###  AND与OR联合使用
SQL语句
 'where likes>50 AND (by = '菜鸟教程' OR title = 'MongoDB 教程')'  
MongoDB语句  
db.col.find({"likes": {$gt:50}, $or: [{"by": "菜鸟教程"},{"title": "MongoDB 教程"}]}).pretty()
## 条件查询
常用的有:  
大于 $gt  >  
小于 $lt  <  
大于等于 $gte >=  
小于等于 $lte <=
### 范围查询
db.col.find({likes : {$lt :200, $gt : 100}})
## $type操作符
\$type基于BSON(时间戳)来进行查询  
db.col.find({"title":{$type:2}})
## limit 与 skip
## 
