## 第一章 了解SQL
### 1.数据库基础
#### 1.什么是数据库
以某种有组织的方式存储的**文件集合**(可以理解为文件柜)。  
数据库保存有组织的数据的**容器**(通常是一个文件或一组文件)。  
**数据库软件**应称为DBMS(数据库管理系统),**数据库**是通过DBMS创建和操作的容器。数据库究竟是文件还是别的什么东西并不重要,因为你并不直接访问数据库。  
#### 2.表
某种**特定结构类型**的**结构化清单**。  
**注：不应该将顾客的清单和订单的清单存储在同一个数据库表中。**存储在表中的数据是一种类型的数据或一个清单。绝不应该将顾客的清单和订单的清单存储在同一个数据库表中。  
数据库中不能有相同的表名。  
#### 3.列和数据类型
列-存放表中**某部分的信息**  
分解数据：如查询特定的州或特定城市的序列，州和城市放在同一列会难以过滤。  
数据类型(datatype)所容许的数据的类型。每个表列都有相应的数据类型,它限制(或容许)该列中存储的数据。  
#### 4.行
表中的数据是按行存储的,所保存的每个记录存储在自己的行内。  
行(row)-表中的记录。  
#### 5.主键
表中每一行应该有可以唯一标识自己的一列(或一组列)。例如,一个顾客表可以使用顾客编号列,而订单表可以使用订单ID。  
主键(primary key)一列(或一组列),其值能够唯一区分表中每个行。**主键用来表示一个特定的文件**。  
表中的**任何列**都可以作为主键,只要它满足以下条件:  
1.任意两行**都不具有相同的主键值**;    
2.每个行都必须具有一个主键值(一定要有值,主键值不允许NULL值)   
##### 主键的好习惯:
1.不更新主键列中的值;  
2.不重用主键列的值;  
3.不在主键列中使用可能会更改的值(例如,如果使用一个名字作为主键标识某个记录,那么后面可能改名)   
### 2.什么是SQL
**结构化查询语言**，用来和数据库通信的语言。  
## 第二章 MySQL简介
### 1 什么是MySQL
MySQL是一种DBMS,即它是一种数据库软件。  
优点:  
1.开源  
2.性能-MySQL执行很快     
3.可信赖  
4.简单-很容易安装和使用   
缺点:它并不总是支持其他DBMS提供的功能和特性     
#### 1.客户机-服务器软件
DBMS可以分为两类:一类为**基于共享文件系统的DBMS**,另一类为**基于客户机-服务器的DBMS**。  
客户机-服务器应用分为两个不同的部分。**服务器**部分是负责所有数据访问和处理的一个软件。这个软件运行在称为**数据库服务器**的计算机上。  
​	与数据文件打交道的只有**服务器软件**。关于数据、数据添加、删除和数据更新的所有请求都由服务器软件完成。**客户机**是与用户打交道的软件。例如,如果你请求一个按字母顺序列出的产品表,则客户机软件通过网络提交该请求给服务器软件。  
​	客户机和服务器软件可能安装在两台计算机或一台计算机上。  
为了使用MySQL,你需要访问运行MySQL服务器软件的计算机和发布命令到MySQL的客户机软件的计算机。  
1.服务器软件为MSQL DBMS。你可以在**本地安装的副本上运行**,也可以连接到运行在你**具有访问权的远程服务器上的一个副本**。  
2.客户机可以是MySQL提供的**工具、脚本语言(如Perl)、Web应用开发语言(如PHP)**、程序设计语言(如C、C++、Java)等。  
#### 2.MySQL版本  
几个重要的引入:InnoDB引擎、增加事务处理、存储过程、触发器、游标。  
### 2.MySQL工具 
为了需要MySQL打交道,你需要一个客户机应用。
#### 1.MySQL命令行实用程序
1.命令输入在mysql>之后;  
2.命令用;或\g结束;  
3.输入help或\h获得帮助;  
4.输入quit或exit退出命令行使用程序。  
#### 2.MySQL Administrator
MySQL Administrator(MySQL管理器)是一个图形交互客户机,用来简化MySQL服务器的管理。
#### 3.MySQL Query Browser
图形交互客户机,用来编写和执行MySQL命令。
## 第三章 使用MySQL
### 1.连接
MySQL与所有客户机-服务器DBMS一样,**求在能执行命令之前登录到DBMS**。MySQL在内部保存自己的用户列表,并且把每个用户与各种权限关联起来。  
为了连接到MySQL,需要以下信息:  
1.主机名-如果连接到本地MySQL服务器,为localhost;
2.端口(如果使用默认端口3306之外的端口);
3.一个合法的用户名;
4.用户口令(如果需要)。
### 2.选择数据库
刚连接上时需要选择一个数据库。可以使用**use**关键字。  
**关键字**(key word)作为MySQL语言组成部分的一个保留字。绝不要用关键字命名一个表或列。
```mysql
use bookstore;
```
### 3.了解数据库和表
数据库、表、列、用户、权限等信息被存储被存储在数据库和表中。内部的表一般不直接访问。可用MySQL的SHOW命令来显示这些信息
```mysql
show databases;  
```
为了获得一个数据库内的列表,使用show databases;  
```mysql
show tables;
```
show也可以用来显示表列:  
```mysql
show columns from custormers;
```
**自动增量**:某些表列需要唯一值。每添加一行，MySQL可以自动地为每个行分配下一个可用编号,不用手动分配。  
describe语句作为show columns from的替代  
```mysql
describe customers;
```
## 第四章 检索数据
### 1.select语句
为了使用select检索表数据,必须至少给出两条信息-想选择什么,以及从什么地方选择。
### 2.检索单个列
```mysql
select product_name from products;
```
### 3.检索多个列
```mysql
select prod_id,prod_name,prod_price from products;
```
#### 数据表示
很多时候我们不会使用从数据库中直接返回的数据,而是需要进行格式转化。
### 4.检索所有列
```mysql
select * from products;
```
注意:除非真的需要一个表的所有列,否则不要去使用*通配符。  
### 5.检索不同的行
```mysql
select distinct vend_id from products;
```
如上使用**distinct**关键字指示MySQL返回**不同**(如果有重复只返回一个)的值。  
### 6.限制结果
```mysql
select prod_name form products limit 5;
```
limit 5指示MySQL返回不多于5行。  
也可以同时指示要检索的起始位置和数量:  
```mysql
select prod_name form products limit 5,5;
```
注:起始位置从0开始而不是从1开始
### 7.使用完全限定的表名
```mysql
select products.prod_name from products;
```
使用完全限定表名:  
```mysql
select products.prod_name 
from crashcourse.products;
```
假定products表确实位于crashcourse数据库中。
## 第五章 排序检索数据
### 1.排列数据
```mysql
select prod_name form products;
```
上面的代码查询返回结果是按照底层表的顺序来的,但是如果表有过修改、删除则可能底层表会被影响。
#### 子句
SQL语句由子句组成,有些是可选的,有些是必需的。  
如果想要对检索出的数据进行排序就要使用**order by**子句。  
```mysql
select prod_name from products order by prod_name;
```
### 2.按多个列排序
经常需要按不止一个列进行数据排序。例如,如果要显示雇员清单,可能希望按照姓和名排序(首先按照姓排序,每个姓中的数据再按名排序)。  
```mysql
select prod_id,prod_price,prod_name from products order by prod_price,prod_name;
```
##　3. 指定排序方向
为了降序排序,可以使用**desc**关键字。  
```mysql
select prod_id,prod_price,prod_name from products order by prod_price desc;
```
注:在多个列上降序排序的时候需要对每个列都是用desc关键字。  
现实场景中经常将order by和limit组合使用  
```mysql
select prod_price 
from products  
order by prod_price desc 
limit 5;
```
## 第六章 过滤数据
### 1.使用where子句
通常我们会遇到指定检索条件的场景,一般将检索条件称为**过滤条件**。  
```mysql
select prod_name,prod_price from products where prod_price=2.50;
```
在同时使用order by和where子句时,应该让order by位于where之后,否则会产生错误。  
### 2.where子句操作符
操作符直接查看手册,between:在两个指定的值之间。
#### 1.检查单个值
```mysql
select prod_name,prod_price from products where prod_name='fuses';
```
#### 2.不匹配检查
```mysql
select vend_id,prod_name from products where vend_id <>1003;
```
也可将上面的"<>"替换为"!="。  
#### 3.范围值检查
```mysql
select prod_name,prod_price from products where prod_price between 5 and 10;
```
#### 4.空值检查
```mysql
select prod_name from products where prod_price is NULL;
```
## 第七章 数据过滤
### 1.组合where子句
通过and子句或者or子句的方式来使用。
#### 1.and操作符
```mysql
select prod_id,prod_price,prod_name 
from products 
where vend_id=1003 and prod_price <=10; 
```
搜索价格小于10而且id为1003的产品信息。
#### 2.or操作符
```mysql
select prod_name,prod_price from products where vend_id=1002 or vend_id-1003;
```
#### 3.计算次序
可以使用任意数目的and和or操作符,允许两者结合以进行复杂和高级的过滤。  
```mysql
select prod_name,prod_price 
from products
where vend_id=1002 or vend_id=1003 and prod_price>=10;
```
上面的代码将会被执行为id为1003并且价格大于10或者id为1002的任意产品,**显然and操作符的优先级要高于or**。  
这与我们的想法背道而驰,解决方案是使用()来明确地分组相应的操作符  
```mysql
select prod_name,prod_price 
from products
where (vend_id=1002 or vend_id=1003) and prod_price>=10;
```
### 2.in操作符
in用来指定条件范围,in的范围在(x,y...)中。  
```mysql
select prod_name,prod_price 
from products
where vend_id in (1002,1003)
order by prod_name;
```
优点:  
1.代码更加清晰易懂;
2.in操作符一般比or操作符清单执行更快;
### 3.not操作符
not:where子句中用来否定后跟条件的关键字。
```mysql
select prod_name,prod_price 
from products
where vend_id not in (1002,1003)
order by prod_name;
```
## 第八章 使用通配符过滤
### 1.like操作符
通配符：用来匹配值的一部分的特殊字符。  
**搜索模式(search pattern)** 由字面值、通配符或两者组合而成的搜索条件(例如,'%jet')。
#### 1.百分号(%)通配符
```mysql
select prod_id,prod_name 
from products
where prod_name like 'jet%';
```
此例子使用了搜索模式'jet%',在执行时将检索任意以jet开头的词。  
注:根据MySQL的配置方式,搜索可以是区分大小写的。如果区分大小写,'jet%'与JetPack 1000将不匹配。  
```mysql
select prod_id,prod_name 
from products
where prod_name like '%anvil%';
```
搜索模式'%anvil%'将匹配任何位置包含文本anvil的值。  
```mysql
select prod_name
from products
where prod_name like 's%e';
```
**除了一个或者多个字符之外,%还能匹配0个字符**。
注意:%不能匹配NULL  
```mysql
select ...
where prod_name like '%';
```
上面的代码(可以执行,匹配所有值)也无法匹配NULL。  
#### 2.下划线(_)通配符
```mysql
select prod_id,prod_name
from products
where prod_name like '_ ton anvil';
```
_用来匹配单个字符。  
### 2.通配符使用技巧
1.不要过度使用通配符。如果其他操作符能达到相同的目的,应该使用其他操作符。  
2.在确实需要使用通配符时,除非绝对有必要,否则不要把它们用在搜索模式的开始处。把通配符置于搜索模式的开始处,搜索起来是最慢的。  
3.注意通配符的位置,否则将会返回错误的结果。  
## 第九章 用正则表达式进行搜索
### 1.正则表达式介绍
为什么使用正则表达式？  
为了应对更加复杂的过滤条件。  
正则表达式用来匹配文本的**特殊的串**(字符集合)。例如,从文本中提取电话号码,找到文本中所有重复的单词等等。  
任何语言、文本编辑器和操作系统都支持正则表达式。  
### 2.使用MySQL正则表达式
#### 1.基本字符匹配
```mysql
select prod_name 
from products
where prod_name regexp '1000'
order by prod_name;
```
就像like的语句一样,他告诉MySQL:后面跟的东西为正则表达式。  
```mysql
select ...
where prod_name regexp '.000'
```
.可以匹配任意一个字符。  
匹配不区分大小写,即大写和小写都匹配。如果想要区分大小写,可以使用BINARY关键字,放在regexp后面。  
##### regexp和like的区别
like匹配的是整个列  
regexp中是值(例如字符串)逐个匹配的功能。  
#### 2.进行or匹配
```mysql
select prod_name
from products
where prod_name regexp '1000|2000'
order by prod_name;
```
使用|,功能和or操作符一样,多个or可以并入单个正则表达式。  
#### 3.匹配几个字符之一
```mysql
select prod_name
...
where prod_name regexp '[123] Ton'
...
```
[123]定义一组字符,它的意思是匹配1或2或3,因此,1ton和2ton都匹配且返回。  
```mysql
select prod_name
from products
where prod_name regexp '1|2|3 Ton'
order by prod_name;
```
#### 4.匹配范围
```mysql
select prod_name
from products
where prod_name regexp '[1-5] Ton'
order by prod_name;
```
#### 5.匹配特殊字符
```mysql
select vend_name
from vendors
where vend_name regexp '.'
order by vend_name;
```
上面检索返回的结果并不对,想要匹配特殊字符,必须用\\\\为前导。  
```mysql
select vend_name
from vendors
where vend_name regexp '\\.'
order by vend_name;
```
特殊字符包括.、|、[]以及迄今为止用过的其他字符。  
注意:  
1.为了匹配反斜杠(\\)字符本身,需要使用\\\\\\。  
2.\\或\\\\？多数正则表达式实现使用单个反斜杠转义特殊字符,以便能使用这些字符本身。但是MySQL要求两个反斜杠(MySQL自己解释一个,正则表达式库解释另一个)。  
#### 6.匹配字符类
建议直接观看手册
#### 7.匹配多个实例
| 元字符 | 说明                      |
| ------ | ------------------------- |
| *      | 0个或者多个匹配           |
| +      | 1个或者多个匹配({1,})     |
| ？     | 0个或者1个匹配(等于{0,1}) |
| {n}    | 指定数目的匹配            |
| {n,}   | 不少于指定数目的匹配      |

```mysql
select prod_name 
from products
where prod_name regexp '\\([0-9] stick?)\\'
order by prod_name;
```
||()就是为了转义()  
[0-9]范围  
stick？通配符检索  
```mysql
select prod_name
from products
where prod_name regexp '[[:digit:]]{4}'
order by prod_name;
```
#### 8.定位符
之前有提到过like匹配的是列,使用regexp也能匹配列,配合下面这几个元字符一起使用  

| 元字符  | 说明       |
| ------- | ---------- |
| ^       | 文本的开始 |
| $       | 文本的结尾 |
| [[:<:]] | 词的开始   |
| [[:>:]] | 词的结尾   |

```mysql
select prod_name 
from products
where prod_name regexp '^[0-9\\.]'
order by prod_name;
```
也就是说,^[0-9\\\\.]只在.或任意数字为串中**第一个字符**时才匹配它们。  
**^的第二重用途**:在集合中,用它来否定集合(用[和]定义)。  
## 第十章 创建计算字段
### 1.计算字段
**为什么有计算字段？**  
一般查询返回的结果都不是应用程序想要的,编程人员将拿到的结果进行格式化或者重新组装才能使用。而计算字段创建在**select语句内部**,数据库开发者可以完成这些工作。  
**字段**基本上与列的意思相同,列被称为数据库列,字段一般用在计算字段的连接上。  
客户机与服务器的格式:许多转换和格式化的工作都可以在客户机的应用程序内完成。但是一般来说,在数据库服务器上完成这些操作比在客户机内完成要快的多,因为DBMS是设计来快速有效地完成这种处理的。  
### 2.拼接字段
拼接:将值联结到一起构成单个值,MySQL中是使用concat()函数来拼接两个列。  
下面的例子是要生成一个表,需要在供应商的名字按照name(location)这样的格式列出供应商的位置。  
```mysql
select concat(vend_name,'(',vend_country,')')
from vendors
order by vend_name;
```
concat()拼接串,即把多个串拼接起来形成一个较长的串。  
#### Trim()函数
使用trim()函数可以去掉多余的空格  
```mysql
select concat(RTrim(vend_name),'(',vend_country,')')
from vendors
order by vend_name;
```
#### 使用别名
```mysql
select concat(RTrim(vend_name)...) as vend_title
from vendors
order by vend_name;
```
计算字段之后跟了文本as vend_title，它指示SQL创建一个名为vend_title的计算字段。  
### 3.执行算术计算
计算字段另一种用途是对检索出的结果进行算术计算。  
```mysql
select prod_id,
	quantity,
	item_price,
	quantity*item_price as expand_price
from orderitems
where order_num=20005;
```
## 第十一章 处理函数
### 1.函数
由于实现DBMS各不相同,所以不要使用当前的DBMS的特殊函数,这样做会让SQL的移植性很差。 
### 2.使用函数  
大多数SQL实现支持以下函数  
#### 1.用于处理文本串  
如删除或填充值,转换值为大写或者小写  
转换大写  
```mysql
select vend_name,Upper(vend_name) as vend_name_upcase
from vendors
order by vend_name;
```
##### 常用函数
Left()  
返回最左边字符
Length()  
Locate()  
找出串的一个子串  
Lower()   
LTrim()  
Right()  
RTrim()  
Soundex()   
返回串的Soundex值  
SubString()    
返回子串的字符  
Upper()    
#### 2.用于在数值上数据上进行算术操作
返回绝对值,进行代数运算。  
数值处理函数仅处理数值数据。这些函数一般主要用于代数、几何运算,因此没有串或者日期-时间处理函数的使用那么频繁。  
#### 3.用于处理日期和时间值并从这些值中提取特定成分
例如,两个日期之差,检查日期有效性等。  
一般来说应用程序不使用用来存储日期和时间的格式,因此日期和时间函数总是被用来读取、统计和处理这些值。  
##### 常用函数
AddDate()  
AddTime()  
CurDate()  
CurTime()  
Date()  
返回日期  
DateDiff()  
Date_Add()  
Date_Format()  
Day()  
Hour()  
Minute()  
Month()  
Now()  
Second()  
Time()  
Year()   
不管是插入、删除还是更新表值日期格式都建议用yyyy-mm-dd,这种格式排除了多义性。  
下面有一个例子
```mysql
select cust_id,order_num
from orders
where order_date='2005-09-01';
```  
上面的查询是不可靠的,因为order_date的类型为datetime,这种类型从除了日期以外还包含时分秒(默认为00:00:00)。当你不知道具体时间时查询的结果就是错误的。  
```mysql
select cust_id,order_num
from orders
where Date(order_date)='2005-09-01';
```  
查询时间也能够使用between操作符  
```mysql
select cust_id,order_num
from orders
where Date(order_date) between '2005-09-01' and '2005-09-30'
```
#### 4.返回DBMS正使用的特殊信息
例如返回用户登录信息,检查版本细节。  
## 第十二章 汇总数据
### 1.聚集函数
为什么使用聚集函数?  
我们经常要**汇总数据**而不用把它们实际检索出来,为此MySQL提供了专门的函数。  
**聚集函数**:运行在行组上,计算返回单个值的函数。  
#### 常用函数
avg()   
返回某列的平均值  
count()  
返回某列的行数  
通常的用法是count(*)和count(column)。  
count(*)会统计表中的行数,包括NULL值。  
count(column)对特定列中具有值的行进行计数,忽略NULL值。  
max()  
返回某列的最大值  
max(列名)  
min() 
返回某列的最小值  
sum()  
返回某列值之和  
### 2.聚集不同值
```mysql
select avg(distinct prod_price) as avg_price
from proucts
where vend_id=1003;
```
上面5个函数既可以单独使用(例如sum()),也可以和distinct参数搭配使用。  
### 3.组合聚集函数
```mysql
select count(*) as num_tiems
min(prod_price) as price_min
from products;
```

## 第十三章 分组数据
### 1.数据分组
为什么有数据分组？  
```mysql
select count(*) as num_prods
from products
where vend_id=1003;
```
但是如果要返回每个供应商提供的产品数目怎么办?或者返回提供10个产品以上的供应商怎么办?
这就是数据分组的意义所在。
### 2.创建分组
数据分组通过select语句下的order by子句建立。
```mysql
select vend_id,count(*) as num_prods
from products
group by vend_id;
```
使用group by的规定
group by子句中可以包含任意数量的列。
### 3.过滤分组
MySQL允许过滤分组,规定包括哪些分组,排除哪些分组。
使用的是**having**子句
```mysql
select cust_id,count(*) as orders
from orders
group by cust_id
having count(*)>=2;
```
#### **where和having的区别**
where过滤的是行而不是分组,where没有分组的概念。
过滤价格>=10,超过两个以上的产品的供应商
```mysql
select vend_id,count(*) as num_prods
from products
where prod_price>=10
group by vend_id
having count(*)>=2;
```
### 4.分组和排序
#### order by与group by的区别
1.order by排序,而group分组行之后输出不一定是分组的顺序。  
2.任意列都可以使用order by;而group by只可能使用选择列或表达式列,而且必须使用每个选择列表达式(???)  
3.order by不一定需要;但是group by和聚集函数一起使用列(表达式),则一定需要。  
### 5.select子句顺序
| 子句     | 说明 | 是否必需               |
| -------- | ---- | ---------------------- |
| select   |      | 是                     |
| from     |      | 仅从表中检索数据时需要 |
| where    |      | 否                     |
| group by |      | 按组聚集时使用         |
| having   |      | 否                     |
| order by |      | 否                     |
| limit    |      | 否                     |

## 第十四章 使用子查询
### 1.子查询
什么需要子查询?  
书上的例子两张表中有共有列(订单编号),所以通过两次查询来得到检索结果。  
```mysql
select order_num
from oderitems
where prod_id="TNT2";
```  
```mysql
select cust_id
from orders
where order_num in (20005,20007)
```  
现在可以使用子查询将其进行合并  
```mysql
select cust_id
from orders
where order_num in (select order_num from orderitems
where prod_id='TNT2');
```  
### 2.作为计算字段使用子查询  
```mysql
select cust_name,
	cust_states,
	(select count(*) 
    from orders
    where orders.custid=customers.custid) as orders
    from customers
    order by cust_name;
```  
这条select语句对customers表中每个客户返回3列。  
#### 相关子查询  
涉及外部查询的子查询。
```mysql
where orders.custid=customers.custid
```  
只要有多义性就必须使用这种语法。    
## 第十五章 联结表
### 1.联结
#### 1.关系表
**外键**:某个表中的某一列,它包含另一个表的主键值,定义了两个表之间的关系。  
好处:  
1.供应商信息不重复,从而不浪费时间和空间。  
2.如果供应商信息变动,可以只更新vendors表中的单个记录。  
3.由于数据无重复,显然数据一致。  
#### 2.为什么使用外联结
将数据分为多个表能更有效地存储,更方便的处理,并且具有更大的可伸缩性。  
但是代价就是检索多个列时更加复杂,所以有了联结。  
联结是一种机制,用来在一条select语句中关联表。  
### 2.创建联结
#### 1.where子句的重要性
```mysql
select vend_name,prod_name,prod_price
from vendors,products
where vendors.vend_id=products.vend_id
order by vend_name,prod_name;
```
上面代码中需要检索的列不在同一张表中,所以from后面用,将表隔开,而在where中建立正确的联结。  
我们可以这样理解,实际上就是将第一个表的每一行和第二个表进行匹配。  
**笛卡尔积**没有联结条件的表关系返回的结果为笛卡尔积(第一个表的行数\*第二个表的行数)。
#### 2.内部联结
也被称作**等值联结**,基于两个表的相等测试。  
```mysql
select vend_name,prod_name,prod_price
from vendors inner join products
on vendors.vend_id=products.vend_id;
```
### 3.联结多个表
```mysql
select prod_name,vend_name,prod_price,quantity
from orderitems,products,vendors
where products.vend_id=vendors.vend_id
	and orderitems.prod_id=products.prod_id
	and order_num=20005;
```
联结的表越多,性能下降越快。
## 第十六章 创建高级联结
### 1.使用表别名
```mysql
select ...
from customers as c...
```
### 2.使用不同类型的联结
#### 1.自联结
场景:通过某生产商查询其他铜生产商产品。  

```mysql
select prod_id,prod_name
form products
where vend_id=(select vend_id
from products
where prod_id='DTNTR');
```
#### 2.自然联结
内部联结
#### 3.外部联结
##### 左联结left joijn
检索结果保留左边表的全部数据。  
#### 右联结right join
检索结果保留右边表的全部数据。  
### 3.使用带聚集函数的联结
```mysql
select customers.cust_name
	customers.cust_id,
	count(orders.order_num) as num_ord
	from customers inner join orders on customers.cust_id=orders.cust_id
	group by customers.cust_id;
```
### 4.使用联结和联结条件
1.注意所使用的联结类型。一般我们使用内部联结,但使用外部联结也是有效的。
2.保证正确的联结条件。
3.应该总是提供正确的联结条件。
4.在一个联结中包含多个表,甚至对于每个联结可以采用不同的联结类型。
## 第十七章 组合查询
### 1.组合查询
为什么使用组合查询  
1.在单个查询中从不同的表返回类似的数据。  
2.对单个表执行多个查询,按单个查询返回数据。  
### 2.创建组合查询
#### 1.使用union 
```mysql
select vend_id,pro_id,pro_price
from products
where prod_price<=5
union
select vend_id,prod_ic,prod_price
from products
where vend_id in (1001,1002)
order by vend_id,prod_price;
```
#### 2.union规则
1.必须两条或者**两条以上**的select语句构成
2.每个查询中必须**包括相同的列**
3.每个**查询的列必须兼容**
## 第十八章 全文本搜索
## 第十九章 插入数据
### 1.数据插入
## 第二十章 更新和删除数据
### 更新
### 删除
## 第二十一章 创建和操纵表
### 创建表
### 更新表
### 删除表
### 重命名表
## 第二十二章 使用视图
### 视图
视图是一个**虚拟的表**，只包含动态检索数据的查询。
## 第二十三章 存储过程
简单来说，就是为以后的使用而保存的一条或者多条语句的集合。
## 第二十四章  游标
存储在数据库服务器上的查询，不是select语句，而是**结果集**。
## 第二十五章 触发器
响应更新操作执行的**语句**。
## 第二十六章 管理事务处理
事务处理是一种方案或者说**机制**，用来维护**数据库的完整性**。
### 几个常见概念
#### transcation:一组SQL语句
#### rollback:撤销这一组SQL的提交
#### commit:将未存储的语句执行结果写入数据库表中
#### savepoint:一个事务中的临时占位符,可以针对其发布rollback


