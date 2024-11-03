## 简介
JavaScriptObjectNotation(JavaScript对象表示法)
存储文本和交互文本信息的语法，类似XML。
#### 为什么用？
对于AJAX应用程序来说,JSON比XML更快更易使用
## 语法
#### 规则
1. 数据在key:value中
2. 数据由逗号分隔
3. 大括号{}保存对象
4. 中括号[]保存数组，数组保存多个对象
#### JSON值的类型
1. 数字
2. 字符串
3. 逻辑值
4. 数组
5. 对象
6. null
## JSON对象
#### 访问对象值
x=myObj.name;
## JSON数组
#### 循环数组
```js
for(i=0;i<myObj.sites.length;i++){
    x+=myObj.sites[i]+"<br>";
}
``` 
## JOSN对象常用方法
#### JSON.parse()
将**数据转换为JavaScript对象**
#### JSON.stringify()
将JavaScript对象转换为**字符串**