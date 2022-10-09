---
html:
    toc: true
    # number_sections: true
    toc_depth: 6
    toc_float: true
        collapsed: true
        smooth_scroll: true
--- 
<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->
# 介绍
Jaspersoft：这是基于Eclipse软件开发的图形化报表设计工具。
# 快速上手
## 创建项目与模板
1. 创建项目  
![](https://raw.githubusercontent.com/aryangzhu/blogImage/master/%E5%88%9B%E5%BB%BA%E9%A1%B9%E7%9B%AE.png)
2. 选择项目类型  
![](https://raw.githubusercontent.com/aryangzhu/blogImage/master/%E9%80%89%E6%8B%A9%E9%A1%B9%E7%9B%AE%E7%B1%BB%E5%9E%8B.png)
3. 输入项目名称  
![](https://raw.githubusercontent.com/aryangzhu/blogImage/master/%E8%BE%93%E5%85%A5%E9%A1%B9%E7%9B%AE%E5%90%8D%E7%A7%B0.png)
4. 创建模板  
![](https://raw.githubusercontent.com/aryangzhu/blogImage/master/%E5%88%9B%E5%BB%BA%E6%A8%A1%E6%9D%BF.png)
5. 选择模型  
![](https://raw.githubusercontent.com/aryangzhu/blogImage/master/%E9%80%89%E6%8B%A9%E6%A8%A1%E5%9E%8B.png)
6. 模板名称  
![](https://raw.githubusercontent.com/aryangzhu/blogImage/master/%E6%A8%A1%E6%9D%BF%E5%90%8D%E7%A7%B0.png)

## 项目目录结构展示
![](https://raw.githubusercontent.com/aryangzhu/blogImage/master/%E7%9B%AE%E5%BD%95%E7%BB%93%E6%9E%84.png)
## OutLine元素列表
![](https://raw.githubusercontent.com/aryangzhu/blogImage/master/OutLine%E5%85%83%E7%B4%A0%E5%88%97%E8%A1%A8.png)
## 基本元素
![](https://raw.githubusercontent.com/aryangzhu/blogImage/master/%E5%9F%BA%E6%9C%AC%E5%85%83%E7%B4%A0.png)
## <font color="red">模板参数(常用)</font>
![](https://raw.githubusercontent.com/aryangzhu/blogImage/master/%E6%A8%A1%E6%9D%BF%E5%8F%82%E6%95%B0.png)
Report Name : 模板名称，注意，如果你复制了一份模板文件，这个地方是没有修改的。  
Description : 模板描述，这个模板文件是干什么的，起注释作用。  
Language : 有三种 Java | groovy | javascript, 这里指定报表表达式使用的语言。  
Imports : 引入其他包，自定义，或者第三方  
Format Factory Class : 翻译 (指定实现要与此报表一起使用的接口的类的名称。如果省略，将创建的实例)  
When No Data Type: (当打印的报表数据源中没有数据的情况下，也就是数据源为空的情况下)  
​null: 默认，不选择。    
​No Pages: 不打印数据。  
​Blank Pages:返回一个空白的页面。 
​All Sections No Detail: 打印除了Detail 之外的所有页面。
​No Data Section: 把No Data的Band 的也打印出来。  
Report 属性 描述  
Title On A New Page 表示 Tilte Band 单独一页打印。    
Summary On A New Page 表示 Summary 单独一页打印。  
Summary With Page Header And Footer 表示在Sumnmary最后一页，也显示Header头 和 Footer脚  
Float Column Footer 在最后一页,Column Foot(列脚)是否紧挨着最后一个Details
Ignore Pagination 忽略分页  
Create bookmarks 创建书签  
Dataset 参数  
When Resource Missing Type:(当资源的属性错误时)  
​Null: 默认，为Null。  
​Empty: 为空。  
​Key: 输出key  
​Error:报错，异常。  
Scriptlet Class: (网上百度)自定义scriptlet，可在报表生成时自定义一些行为。  
Resource Bundle: 资源绑定，报表所用资源文件。  
Default Data Adapter: 默认数据源，在这里，可以选择数据源配置在哪里  
___
数据源配置
![](https://raw.githubusercontent.com/aryangzhu/blogImage/master/%E6%95%B0%E6%8D%AE%E6%BA%90%E9%85%8D%E7%BD%AE.png)
### Page Format:报表格式化
![](https://raw.githubusercontent.com/aryangzhu/blogImage/master/%E6%8A%A5%E8%A1%A8%E6%A0%BC%E5%BC%8F%E5%8C%96.png)
# 简单报表
## 新建模板 
1. 保留Title和Detail1  
2. 配置主表中的数据源和查询集    
3. 如图   ![](https://raw.githubusercontent.com/aryangzhu/blogImage/master/%E7%AE%80%E5%8D%95%E6%8A%A5%E8%A1%A8%E6%A0%B7%E5%BC%8F.png)
4. 可以通过vairiable变量表达式进行聚集函数运算的作用
# <font color="red">主报表与子报表相互传参</font>
## 主报表配置
**为什么使用主报表和子报表？**  
因为Detail1代表一条数据,所以需要展示一对多的样式(要求居中垂直)时只凭一张报表无法胜任,这个时候使用subreport组件就是解决方案之一,但是编译的时候会将子报表也进行编译。
1. 样式配置  
![](https://raw.githubusercontent.com/aryangzhu/blogImage/master/%E4%B8%BB%E8%A1%A8%E6%A0%B7%E5%BC%8F.png)
2. 配置数据源
## 子报表配置
1. 样式配置  
只需要保留Detail即可  
![](https://raw.githubusercontent.com/aryangzhu/blogImage/master/%E5%AD%90%E6%8A%A5%E8%A1%A8%E6%A0%B7%E5%BC%8F.png)
根据报表的格式进行设置,**将Detail的宽度和高度和里面的元素大小保持一致**
2. 创建参数  
需要和主报表传过来的参数保持一致,主报表中的参数后面会讲到如何设置。  
将子报表配置放在前面是因为在拖拽subreport时需要选择已存在模板。  
3. 配置数据源和查询条件
右键OutLine中当前报表,选择 DataSet and Query...
选项
![](https://raw.githubusercontent.com/aryangzhu/blogImage/master/%E9%85%8D%E7%BD%AE%E6%95%B0%E6%8D%AE%E6%BA%90%E5%92%8C%E8%AE%BE%E7%BD%AE%E6%9F%A5%E8%AF%A2%E8%AF%AD%E5%8F%A5.png)
4. 预览
## 在主报表中配置SubReport组件
1. 拖拽SubReport组件至主报表中  
   ![](https://raw.githubusercontent.com/aryangzhu/blogImage/master/SubReport.png)
2. 选择文件    
   ![](https://raw.githubusercontent.com/aryangzhu/blogImage/master/%E9%80%89%E6%8B%A9%E6%96%87%E4%BB%B6.png)

   ![](https://raw.githubusercontent.com/aryangzhu/blogImage/master/%E9%80%89%E6%8B%A9%E7%9B%B8%E5%90%8C%E8%BF%9E%E6%8E%A5.png)
3. 选择相同连接  
   ![](https://raw.githubusercontent.com/aryangzhu/blogImage/master/%E8%AE%BE%E7%BD%AE%E5%8F%82%E6%95%B0.png)
4. 设置参数  
![](https://raw.githubusercontent.com/aryangzhu/blogImage/master/%E9%85%8D%E7%BD%AE%E5%AD%90%E6%8A%A5%E8%A1%A8%E5%8F%82%E6%95%B0.png)
## <font color="red">细节调整</font>
1. 如果主表中的表格高度无法匹配子报表中的数据,那么需要手动设置主表单元格的参数  
1.1 更改Position Type  
1.2 更改Stretch Type  
如图  
![](https://raw.githubusercontent.com/aryangzhu/blogImage/master/%E4%B8%BB%E8%A1%A8%E5%8D%95%E5%85%83%E6%A0%BC%E5%8F%82%E6%95%B0.png)
## 主报表的表格动态高度设置
# <font color="red">合并行内容的第二种方案:使用CrossTable组件</font>
使用crosstable的需要提前设计好表格的样式(需要展示多少个属性,展示在什么地方)  
**CrossTab需要放在Summry的Band中**
## 使用sql语句查询的时候多放置一个字段
```mysql
select uname,itemname,insure,insurepor,opermoney,extramoney,opermorate,1 a  from insure where   opermoney >$P{condition}
order by uname
```
## 设置横列
用多余的就可以,注意是否需要total
![](https://raw.githubusercontent.com/aryangzhu/blogImage/master/%E8%AE%BE%E7%BD%AE%E6%A8%AA%E5%88%97.png)
## 设置竖列
展示多少个属性
![](https://raw.githubusercontent.com/aryangzhu/blogImage/master/%E8%AE%BE%E7%BD%AE%E7%AB%96%E5%88%97.png)
## 设置交叉值
用竖列的其中一个属性就可以
![](https://raw.githubusercontent.com/aryangzhu/blogImage/master/%E8%AE%BE%E7%BD%AE%E4%BA%A4%E5%8F%89%E5%80%BC.png)
## 设置Cloumn group高度
设置高度为0px
![](https://raw.githubusercontent.com/aryangzhu/blogImage/master/%E8%AE%BE%E7%BD%AEColumnGroup%E9%AB%98%E5%BA%A6.png)
## 设置水平居中、垂直居中
# 合并行的第三种方案:取消属性Print Repeated Values(不推荐)
这种方案只是提供一种思路,建议还是通过subreport或者crosstable组件来实现行的合并
## 报表样式配置
![](https://raw.githubusercontent.com/aryangzhu/blogImage/master/%E5%8F%96%E6%B6%88%E9%87%8D%E5%A4%8D%E5%86%85%E5%AE%B9.png)
一般来说需要合并的都会展示在最左边
## 设置属性
![](https://raw.githubusercontent.com/aryangzhu/blogImage/master/%E6%88%AA%E5%B1%8F2022-09-16%20%E4%B8%8B%E5%8D%882.30.45.png)
将Print Pepeated Values这一项设置取消勾选
## 加入一个Line组件
由于左侧会取消打印重复的行,所以这个时候的左侧除第一行外会空出来一部分,所以需要加入一个Line组件并设置location和size为float和ContainerBottom。
# <font color="red">使用Table组件</font>
1. 拖拽Table组件  
2. 配置数据源
右键DataSet1和主报表中设置数据源的方式一致。
3. 合并单元格
## 细节调整
1. table组件的detail不能超过主报表中的高度,否则会报错
2. 设置variable变量来进行聚合运算的时候将表达式转换为数值型,否则可能不会生效
# 柱状图和饼图
## 柱状图
1. 使用Chart组件,选择BarChart  
2. 设置四维参数 Series、Value、Category和Label  
与常见统计的highchart和echart类似  
假设我要统计1991年到2021年各产业的GDP，Value就是只GDP的具体数值，Category就是指年份[1991~ 2021]，而Series就是指第一产业到第三产业。
注:label一般来说不需要关注,如果只显示最后一个值的话那么就将Lable和Category设置成一样的。
![](https://raw.githubusercontent.com/aryangzhu/blogImage/master/%E6%9F%B1%E7%8A%B6%E5%9B%BE%E8%AE%BE%E7%BD%AE.png)
注意:value为数值类型如Long、Double等,否则预览时会出错。
3. 创建DataSet并传递参数
![](https://raw.githubusercontent.com/aryangzhu/blogImage/master/SubDataSet.png) 
点击上一步的DataSet中的parameters就可以设置参数  
需要向当前的SubDataSet中传入参数,可以是Main DataSet中的Fields中的其中一个属性。  
4. 如果是单柱状图,则不需要Series,将Properties中的Show Legend设置为false。
## 饼图
1. 使用Chart组件,选择PieChart
2. 设置Series、Key、Lable、Value
![](https://raw.githubusercontent.com/aryangzhu/blogImage/master/%E9%A5%BC%E5%9B%BE%E8%AE%BE%E7%BD%AE.png)
3. 设置DataSet
和柱状图一样根据需要传递参数
# 报表平台上传模板文件
1. 检查是否有对应数据源,如果没有需要新建
2. 报表管理中上传
3. 实例化
4. 预览
# SpringBoot中集成Jaspersoft
## 新建SpringBoot项目
## 导入依赖
## 添加模板的静态资源
在生成pdf时可能会有乱码的问题出现,所以需要添加字体的静态资源
### 在使用Jaspersoft软件的过程中也需要设置字体
1. 将字体文件添加到字体库中
2. 修改模板格式
3. 调用export方法生成html或者pdf










