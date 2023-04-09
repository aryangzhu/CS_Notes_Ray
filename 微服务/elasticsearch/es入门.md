# 简介
一个分布式的全文搜索引擎
# docker安装
docker pull elasticsearch:6.4.0  
docker命令创建容器  
docker run -p 9200:9200 -p 9300:9300 --name elasticsearch \
-e "discovery.type=single-node" \
-e "cluster.name=elasticsearch" \
-v /mydata/elasticsearch/plugins:/usr/share/elasticsearch/plugins \
-v /mydata/elasticsearch/data:/usr/share/elasticsearch/data \
-d elasticsearch:6.4.0  
安装中文分词器  
docker exec -it elasticsearch /bin/bash
此命令需要在容器中运行  
elasticsearch-plugin install https://github.com/medcl/elasticsearch-analysis-ik/releases/download/v6.4.0/elasticsearch-analysis-ik-6.4.0.zip
docker restart elasticsearch
# docker安装Kibana
kibana相当于访问es的客户端
# 相关概念
Index:索引时一些具有相似特征的文档集合,类似于MySql数据库的概念。
Type:类型是索引的逻辑分区,类似于表
Document: 文档是可被索引的基本单位,类似于行记录。
# 实践
