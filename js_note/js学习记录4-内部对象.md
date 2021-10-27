**标准对象**
复习一下typeof关键字
typeof 123
number
typeof '123'
string
typeof true
boolean
typeof NaN
number
typeof []
object
typeof {}
object
typeof Math.abs
function
typeof undefined 
undefined

### 1.Date
基本使用

```
  var now=new Date(); //Mon Mar 08 2021 14:51:20 GMT+0800 (中国标准时间)
        console.log(now);
        now.getFullYear();//年
        now.getMonth();//月 0-11代表月
        now.getDate();//日
        now.getDay();//星期几
        now.getHours();//时
        now.getMinutes();//分
        now.getSeconds();//秒

        now.getTime();//时间戳 1615186280742
        console.log(new Date(1615186280742)); //时间戳转换为时间格式
```
转换
```
now.toLocaleString()
"2021/3/8 下午2:56:22"
now.toGMTString()
"Mon, 08 Mar 2021 06:56:22 GMT"
now.toLocaleDateString()
"2021/3/8"
```
### 2.JSON
json是什么
轻量级数据交换格式
特点：
1.层次结构
2.易于解析和编写
格式：
对象都用{}
数组都用[]
所有键值对都是使用key:value

```
var user={
            name:"leiliu",
            age:3,
            sex:'男'
        }
        //对象转化为json字符串格式
        var jsonUser=JSON.stringify(user);

        //json字符串转化为对象
        JSON.parse({"name":"leiliu","age":3,"sex":"男"});
```
注意：上面主要的方法是stringify() 转化为josn格式
parse()将json字符串转化为对象
js对象和json的区别
var obj={a:'hello',b:'hellob'};
var json='{"a":"hello","b":"hellob"}'
### 3.Ajax
原生的js写法 xhr异步请求
jQuery封装好的方法$()
axios 请求