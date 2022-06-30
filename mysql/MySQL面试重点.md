# 索引
## 主键索引
主键:能从属性组唯一确定一条数据
## 唯一索引
作用 : 避免同一个表中某数据列中的值重复
## 常规索引
作用 : 快速定位特定数据
## 全文索引
注意 :
只能用于MyISAM类型的数据表
只能用于CHAR , VARCHAR , TEXT数据列类型
适合大型数据集
CREATE INDEX idx_app_user_name ON app_user(name);
# 事务
