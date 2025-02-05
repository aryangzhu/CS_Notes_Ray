## 基础部分

### 基本数据类型和变量

#### Number

#### 字符串

#### 布尔值&&比较运算符

#### BigInt

#### null和undefined

#### 数组

#### 对象

#### 变量

#### ECMA规范与strict模式

### 字符串

1. 单行字符串
2. 多行字符串
3. 字符串模板

### 数组

#### 常见属性

#### 常用API

### 对象

#### 属性

#### 方法

#### 来自继承的方法

### 条件判断

if-else

注：要写上{}

### 循环

#### 两种种形式

1. for ifor(i=0;i++;i<100)和变体for in
2. while 包括while和while...do

### Map和Set

1. Map常用的一些API
2. Set常用的一些API

### iterable

for...of

**forof与forin**

for...in遍历的是对象所有的属性

for...of遍历集合本身的元素

**forEach函数**

```javascript
let a = ['A', 'B', 'C'];
a.forEach(function (element, index, array) {
    // element: 指向当前元素的值
    // index: 指向当前索引
    // array: 指向Array对象本身
    console.log(`${element}, index = ${index}`);
});
```

## 函数

### 函数定义和调用

**函数也是一个对象，这是和Java语言让我觉得最大不同的地方**

```javascript
function abs(x) {
    if (x >= 0) {
        return x;
    } else {
        return -x;
    }
}
```

#### 定义函数

1. function指出这是一个函数
2. abs是函数的名称
3. (x)括号内列出函数的参数，多个参数以，分隔；
4. {...}之间的是函数体

#### 调用函数

abs(x)

#### 可变参数arguments

```javascript
function foo(x) {
    console.log('x = ' + x); // 10
    for (let i=0; i<arguments.length; i++) {
        console.log('arg ' + i + ' = ' + arguments[i]); // 10, 20, 30
    }
}
foo(10, 20, 30);
```

可以看到，js中可以不按照函数规定的参数传递(设计太不优雅了)，还提供了一个关键字arguments

#### reset参数

```javascript
function foo(a, b, ...rest) {
    console.log('a = ' + a);
    console.log('b = ' + b);
    console.log(rest);
}

foo(1, 2, 3, 4, 5);
```

如果想多余获取额外的参数并且在内部使用这个关键字就可以发挥作用

#### 匿名函数

很好奇这个知识点经常用，但是却没有讲解

比如说有些第三方库为了避免变量名冲突

```javascript
function(){
	var xxx;
	//do something;
}
```

```javascript
const xxx=function(x){
	//do something;
}

xxx();
```

### 变量作用域与解构值

**var和let**

var针对的函数，let针对的是块

前面提到过，如果一个变量不用var修饰，那么就有可能会和其他的全局变量冲突。

#### 变量提升

```javascript
function foo() {
    var x = 'Hello, ' + y;
    console.log(x);
    var y = 'Bob';
}

foo();

```

意思是这个后面声明的变量在运行扫描时会提升至函数的顶部，所以上面代码不会报错。

注：

1.使用let 声明变量，遵守strict模式

2.一定要将声明的变量放置在函数首部

#### 全局作用域

js中有个默认的全局对象window，下面的代码很有意思，我们可以自己修改alert()方法使其失效

```javascript
window.alert('调用window.alert');
//把alert保存到另一个变量
let old_alert=window.alert();
//给alert赋一个新函数
window.alert=function(){}

alert('无法用alert()显示了！');

//恢复alert
window.alert=old_alert;
alert('又可以使用了');


```

#### 名字空间

就是说将自己所有的变量和函数都绑定到一个全局变量中。

```javascript
// 唯一的全局变量MYAPP:
let MYAPP = {};

// 其他变量:
MYAPP.name = 'myapp';
MYAPP.version = 1.0;

// 其他函数:
MYAPP.foo = function () {
    return 'foo';
};
```

#### 局部作用域

说的就是var和let的事儿

#### 常量

```javascript
let PI = 3.14;
```

#### 传统赋值

```javascript
let array = ['hello', 'JavaScript', 'ES6'];
let x = array[0];
let y = array[1];
let z = array[2];
```

使用解构赋值之后形式如下

```javascript
let [x,[y,z]]=['hello',['JavaScript','ES6']];
x;
y;
z;
```

### 方法

对象的函数就是方法

#### this关键字

### 高阶函数

### 闭包

### 箭头函数&标签函数&生成器

## 标准对象

### Date

### RegExp

### JSON

## 面向对象编程

### 创建对象

### 原型继承

### class继承

## 浏览器

### 浏览器对象

### 操作DOM

### 操作表单

### 操作文件

### AjAX(重点)

### Promise函数&async函数

## 异常处理

### 错误传播

当前函数如果处理的话那么就不用

### 异步异常处理

回调函数内部处理

## JQuery

### 选择器

#### 层级过滤

#### 查找和过滤

### 操作DOM

#### 修改DOM结构

## underscore

#### Colletions

#### Arrays

#### Functions

#### Objects

#### Chaining

## Nodejs

Node.js 是一个基于 Chrome V8 引擎的 JavaScript 运行时环境，类似于Java的jre

### 环境搭建

比较重要的事就是npm，也就是类似于Java项目中maven，用来管理你所需要的依赖

### 使用模块

#### 导出

写在js文件文件后面就可以

##### 两种方式

```
function hello() {
     console.log('Hello, world!'); 
}
```

1. module.export

```javascript
module.exports={
     hello:hello
}
```

2. exports

<!-- mindmap-ignore -->

```
exports.hello=hello;
```

#### 导入

require

```javascript
'use strict';

// 引入hello模块:
const greet = require('./hello');

let s = 'Michael';

greet(s); // Hello, Michael!
```

### 使用ESM(es modules)模块

Javascript在遵循ECMAScript标准之后，推出的模块化支持

#### 导出

```javascript

let s='Hello';

function out(prompt,name){
  console.log(`${prompt},${name}`);
}

export function greet(name){
  out(s,name);
}

export function hi(name){
  out('Hi',name);
}
```

注意：文件要保存为.mjs

#### 导入

```javascript
import {greet,hi} from './hello.mjs';
let name = 'Bob';
greet(name);
hi(name);
```

### Node.js基本模板

Node.js内置的常用模块就是为了实现基本的服务器功能

#### 一些常见对象

1. global
   和Javascript中的window一样
2. process
   代表当前Node.js进程
3. 判断执行环境typeof(xxx)

```javascript
  if(typeof(window)==='undefined'){
     console.log('node.js');
  }else{
     console.log('browser');
  }
```

#### 文件处理fs

#### 流处理stream

#### 网络编程http

#### 加密crypto

### Web开发
