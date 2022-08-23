# 速读
## 第一章 概述
MySQL数据库与数据库实例
## 第二章 InnoDB存储模型
### 体系结构 
#### 线程模型
Master Thread IO Thread重点
#### 内存模型
缓冲池 
LRUList  FreeList FlushList
其中LRUList和FlushList是共存的，而FreeList和LRUList则是互斥的
redolog(重做日志)缓冲池
额外缓冲池
### checkpoint(检查点)技术
1. 宕机更快恢复
2. 在redo log不可用时将脏页刷新到磁盘
3. 当脏页的缓冲池不够用时刷新到磁盘
## 第三章 文件
### 参数文件
### 日志文件
#### 查询日志
#### 慢查询日志
# 第四章 表
## 表空间
所有的表都共享一个表空间(逻辑结构),但是也可以通过设置来为每张表单独创建一个表空间。
表空间下面又有段、区和页.
## 数据页
数据页不只是逻辑结构,也是物理存储结构。
## 行
InnoDB面向列存储,数据都是以行形式存放的。
### Compact格式
### Redundant格式
### 两者的区别
## 数据页结构
1. File Header 记录页的头信息,38个字节
2. Page Header 数据页的状态信息,56个字节
3. Infimun+supermun records 用来记录边界
4. User records 实际存储的内容,由B+树索引组织
5. Free space 空闲列表
6. Page directory 页目录指示了记录的相对位置
7. File trailer 检测记录是否已经存入磁盘,用一个标志来进行区分
## 约束
### 约束和索引的区别
约束是一个逻辑上的概念,而索引不只是逻辑上的概念,在物理存储上也是存在的。
## 分区表
这块应该是比较难的部分,但是也应该是比较复杂的部分。 
# 第五章 索引
