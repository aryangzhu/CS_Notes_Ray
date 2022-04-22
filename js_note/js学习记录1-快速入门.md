## 1.概述

流行的脚本语言,js是一中弱类型语言(变量的类型是根据值来确定的,使用typeof关键字可以来判断类型)。

## 2.快速入门

### 1.引入方式

####　1.内部引入

```html
<script>
	alert('hello world');
</script>
```
#### 2.外部引入

```html
<script src="js/qj.js">
</script>

qj.js
alert('hello world');
```
### 2.基本语法入门

console.log("...");
浏览器
![选区_125.png](https://i.loli.net/2021/03/02/PSKtH56Eubf3Mxy.png)

### 3.数据类型

数值、文本、图形、音频、视频

#### Number数值类型

js不区分小数和整数

> Number 123 //整数
>
> 123.1 //浮点数
>
> 1.123e3 //科学计数法
>
> -99 //复数
>
> NaN //not a number
>
> Infinity //表示无限大

#### 字符串类型

 'abc' "abc"

#### 布尔类型

布尔值 true false

true:非零的数值、非空字符串、非空对象

false:零、空字符串、null、undefined

#### 运算符

逻辑运算符

>  && 短路与
>
> || 短路或
>
> ！ 比较运算符

== 等于(类型不一样，值一样，也会判断为true) 

=== 绝对等于(类型一样，结果为true) 这是一个js的缺陷，坚持不要用==比较

**须知：**
NaN===NaN，这个与所有的数值都不相等，包括自己
只能通过isNaN(NaN)来判断这个数是否为NaN
浮点数问题：
console.log((1/3)==(1-2/3))
尽量避免使用浮点数进行运算，存在精度问题
Math.abs(1/3-(1-2/3))<0.0000000000001;

******
##### null和undefined的区别
null 空
undefined 未定义

#### 引用类型

##### 所有new出来的对象

##### 数组
Java的数值必须是相同类型的对象，JS中不需要这样
//保证代码的可读性，尽量使用[]
var arr=[1,2,3,4,5,'hello',null,true];
new Array(1,12,3,4,4,5,'hello');
取数组下标：如果越界了，就会 undefined

##### 对象
var person={
"name":"leiliu"
}
取对象的值
person.name
### 严格检查模式
‘use strict’