# 字符串
## 1.正常字符串我们使用单引号或者双引号包裹
## 2.注意转义字符\
\'
\n
\t
\u4e2d \u### unicode字符
\x41 ascll字符
## 3.多行字符串编写
var msg=`
hello world 
fafa
`
## 4.模板字符串
例如
let name="qinjiang";
let age=3;
let msg=`你好啊,${name}`;
## 5.字符串长度
str.length
## 6.字符串的可变性，不可变
![选区_127.png](https://i.loli.net/2021/03/03/wCt4PurOneDIlLb.png)
## 7.大小写转换
student.toUpperCase()
student.toLowerCase()
## 8.student.indexOf('t');
## 9.substring()
student.substring(0) //从第一个字符串截取到最后一个字符串
student.substring(1,3) //[1,3)

# 数组
Array可以包含**任意的数据类型**
var arr=[1,2,3,4,5,6]; //通过数组下标取值和赋值
## 1.长度
arr.length
注意：加入给arr.length赋值，数组大小就会发生变化
## 2.indexOf 通过元素获取数组下标索引
arr.indexOf(2)
注意：字符串的"1"和数字1是不同的
## 3.slice()
截取Array的一部分，返回一个新数组，类似于String中的subString  但是[)
## 4.push和pop
push:压入到尾部
pop：弹出尾部的一个元素
## 5.unshift()、shift()
unshift:压入到头部
shift:弹出头部的一个元素
## 6.排序sort()
["B","C","A"]
arr.sort()
["A","B","C"]
## 7.元素反转reverse()
["A","B","C"]
arr.reverse()
["C","B","A"]

## 8.concat()拼接数组并返回一个新的数组

```javascript
arr=["B","C","A"]
(3) ["B", "C", "A"]
arr.sort()
(3) ["A", "B", "C"]
arr.reverse()
(3) ["C", "B", "A"]
arr.concat([1,2,3])
(6) ["C", "B", "A", 1, 2, 3]
arr
(3) ["C", "B", "A"]
```
注意:concat()并没有修改数组，只是会返回一个新的数组
## 9.join()
打印拼接数组，使用特定的字符串连接
arr.join("-")
"C-B-A"
## 10.多维数组
```javascript
arr=[[1],[2,true],[null,undefined,"a"]]
(3) [Array(1), Array(2), Array(3)]
console.log(arr[1][2])
VM2058:1 undefined
undefined
console.log(arr[2][2]);
VM2139:1 a
undefined
arr[0][1]
undefined
arr[0][0]
1
arr[2][1]
undefined
```
数组：存储数据(如何存，如何取)
# 对象
若干个键值对
var 对象名={
	属性名:属性值，
    属性名:属性值，
    属性名:属性值
}
JavaScript中所有的键都是字符串，值是任意对象！
## 1.对象赋值
## 2.使用一个不存在的对象属性，不会报错！undefined
## 3.动态的删减属性，通过delete删除对象的属性
## 4.动态的添加，直接给新的属性添加值即可
peson.haha="haha"
"haha"
person  
## 5.判断属性值是否在这个对象中！
'age' in person
true
//继承
## 6.判断一个属性是否是这个对象自身拥有的hasOwnProperty
person.hasOwnProperty('toString')
false
person.hasOwnProperty('age')
true  
# 流程控制
## if 判断
var age=3;
if(age>3){
	alert("haha");
}
## 循坏
while循环，避免死循环
while(age<3){
	age=age+1;
    console.log(age);
}
## for循环
for(let i=0;i<100;i++){
	console.log(i);
}
## 数组循环
**age.forEach();**语言糖
for(var index in object){}
for(var num in age){
	if(age.hasOwnProperty(num)){
    console.log("存在");
    console.log(age[num]);
    }
}
# Map和Set
var map=new Map([['tom',100],['jack',90],['haha',80]]);
语法 new Map(['xxx',ob]);  //和Java一样通过Key可以获得value
var set=new Set([3,1,1])； //和Java一样set可以去重
set.add(2); //添加
set.delete(); //删除
console.log(set.has(3)); //是否包含某个元素
## iterator 迭代器
和Java的用法类似
Map

```js
 var map=new Map([["tom",100],["zhangsan",80]]);
        for (var x of map){
            console.log(x);
        }
```
set类似
注意：这里set和map是of，但是数组中既能用下标var x in arr,也能用var x of arr