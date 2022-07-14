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
# 第四章 
