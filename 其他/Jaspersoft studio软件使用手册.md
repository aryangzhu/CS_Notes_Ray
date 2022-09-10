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
因为Detail1代表一条数据,所以需要展示一对多的样式时只凭一张报表无法胜任
1. 样式配置  
![](https://raw.githubusercontent.com/aryangzhu/blogImage/master/%E4%B8%BB%E8%A1%A8%E6%A0%B7%E5%BC%8F.png)
2. 配置数据源
## 子报表配置
1. 样式配置  
![](https://raw.githubusercontent.com/aryangzhu/blogImage/master/%E5%AD%90%E6%8A%A5%E8%A1%A8%E6%A0%B7%E5%BC%8F.png)
2. 创建参数
需要和主报表传过来的参数保持一致
3. 配置数据源和查询条件
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
# <font color="red">使用Table组件</font>
1. 拖拽Table组件  
2. 配置数据源
3. 合并单元格
## 细节调整
1. table组件的detail不能超过主报表中的高度,否则会报错




