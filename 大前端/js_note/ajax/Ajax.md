# 介绍
Ajax asyn javasript and xml
异步发送请求的一种**技术**。
## 作用
不重新加载网页的情况下动态地修改网页内容
## 遵从的标准
1.XmlHttpRequest(与服务器异步交换数据)
2.js/DOM(信息显示/交互)
3.css
4.xml
# 创建XHR对象
XMLHttpRequest是Ajax的基础
xmlhttp=new XMLHttpRequest();
# XHR发送请求
## open(method：请求的类型:GET 或 POST,url：文件在服务器上的位置,async：true（异步）或 false（同步）)
## send(string(仅用于post请求))
## GET实例
```js
xmlhttp.open("GET","/try/ajax/demo_get.php?t=" + Math.random(),true);
xmlhttp.send();
```
## POST实例
```js
xmlhttp.open("POST","/try/ajax/demo_post2.php",true);
// 之前http学习过，请求可以设置首部
xmlhttp.setRequestHeader("Content-type","application/x-www-form-urlencoded");
xmlhttp.send("fname=Henry&lname=Ford");
```
# XHR服务器响应
有两种响应
reponseText和responseXML
## responseText
```js
document.getElementById("myDiv").innerHTML=xmlhttp.responseText;
```
## responseXML
```js
xmlDoc=xmlhttp.responseXML;
txt="";
// 可以看出xml文件以DOM方式进行解析
x=xmlDoc.getElementsByTagName("ARTIST");
for (i=0;i<x.length;i++)
{
    txt=txt + x[i].childNodes[0].nodeValue + "<br>";
}
document.getElementById("myDiv").innerHTML=txt;
```
# XHR readyState(事件)
**readyState**属性改变时就会出发onreadystatechange事件
XHR对象的3个重要属性
## onreadystatechange事件
函数
## readyState
0-4发生变化
## status
对应http响应状态
## 使用回调函数
回调函数是以参数形式传递给另一个函数的函数
```js
var xmlhttp;
function loadXMLDoc(url,cfunc)
{
if (window.XMLHttpRequest)
  {// IE7+, Firefox, Chrome, Opera, Safari 代码
  xmlhttp=new XMLHttpRequest();
  }
else
  {// IE6, IE5 代码
  xmlhttp=new ActiveXObject("Microsoft.XMLHTTP");
  }
xmlhttp.onreadystatechange=cfunc;
xmlhttp.open("GET",url,true);
xmlhttp.send();
}
// 自定义函数
function myFunction()
{
	loadXMLDoc("/try/ajax/ajax_info.txt",function()
	{
		if (xmlhttp.readyState==4 && xmlhttp.status==200)
		{
			document.getElementById("myDiv").innerHTML=xmlhttp.responseText;
		}
	});
}
```
# Ajax实例
根据需求直接去菜鸟教程上找就可以
# Ajax JSON
使用JSON.parse()方法将数据转换为Javascript对象
```js
function loadXMLDoc()
{
  var xmlhttp;
  if (window.XMLHttpRequest)
  {
    // IE7+, Firefox, Chrome, Opera, Safari 浏览器执行代码
    xmlhttp=new XMLHttpRequest();
  }
  else
  {
    // IE6, IE5 浏览器执行代码
    xmlhttp=new ActiveXObject("Microsoft.XMLHTTP");
  }
  xmlhttp.onreadystatechange=function()
  {
    if (xmlhttp.readyState==4 && xmlhttp.status==200)
    {
        // 这里是代码的重点部分
      var myArr = JSON.parse(this.responseText);
      myFunction(myArr)
    }
  }
  xmlhttp.open("GET","/try/ajax/json_ajax.json",true);
  xmlhttp.setRequestHeader("Content-Type", "application/json;charset=UTF-8");
  xmlhttp.send();
}
function myFunction(arr) {
  var out = "";
  var i;
  for(i = 0; i < arr.length; i++) {
    out += '<a href="' + arr[i].url + '">' + 
    arr[i].title + '</a><br>';
  }
 document.getElementById("myDiv").innerHTML=out;
}
```