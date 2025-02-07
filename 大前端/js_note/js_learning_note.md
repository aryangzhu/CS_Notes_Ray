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

#### 传统赋值&解构赋值

解构赋值就是对一组变量进行赋值

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

还有对于对象的操作

```javascript
let person = {
    name: '小明',
    age: 20,
    gender: 'male',
    passport: 'G-12345678'
};

// 如果person对象没有single属性，默认赋值为true:
let {name, single=true} = person;
name; // '小明'
single; // true
```

#### 解构赋值使用场景

```javascript
//交换变量
let x=1,y=2;
[x,y]=[y,x];

//快速获取当前页面的域名和路径
let {hostname:domain,pathname:path}=location;

//将传入参数绑定到对象的属性中
function buildDate({year,month,day,hour=0,minute=0,second=0}){
       return new Date(`${year}-${month}-${day} ${hour}:${minute}:${second}`);
}
```

### 方法

对象中绑定的函数就是方法

```javascript
let xiaoming = {
    name: '小明',
    birth: 1990,
    age: function () {
        let y = new Date().getFullYear();
        return y - this.birth;
    }
};

xiaoming.age; // function xiaoming.age()
xiaoming.age(); // 今年调用是25,明年调用就变成26了
```

#### this关键字

xiaoming.age是一个函数对象

xiaoming.age()调用方法

```javascript
function getAge() {
    let y = new Date().getFullYear();
    return y - this.birth;
}

let xiaoming = {
    name: '小明',
    birth: 1990,
    age: getAge
};

xiaoming.age(); // 25, 正常结果
getAge(); // NaN
```

要想成功执行getAge()方法则必须要指明当前对象是xiaoming,否则this指向的就是window。

ECMA决定，在strict模式下让函数的 `this`指向 `undefined。`

所以进行年龄计算就会直接报错了，下面的代码可以作为使用的参考

```javascript
'use strict';

let xiaoming = {
    name: '小明',
    birth: 1990,
    age: function () {
        let that = this; // 在方法内部一开始就捕获this
        function getAgeFromBirth() {
            let y = new Date().getFullYear();
            return y - that.birth; // 用that而不是this
        }
        return getAgeFromBirth();
    }
};

xiaoming.age(); // 25
```

#### apply

由于函数是一个对象，它也有来自继承的方法可以使用

```javascript
xiaoming.age(); // 25
getAge.apply(xiaoming, []); // 25, this指向xiaoming, 参数为空
```

#### apply&call

apply()把参数打包成Array再传入

call()把参数按顺序传入

```javascript
Math.max.apply(null,[3,5,4]);
Math.max.call(null, 3, 5, 4); // 5
```

对于普通函数把this绑定为null

#### 装饰器

利用apply()，我们可以动态地改变函数的行为。

**JavaScript的所有对象都是动态的**，即使内置的函数，我们也可以重新指向新的函数。

```javascript
'use strict';

let count = 0;
let oldParseInt = parseInt; // 保存原函数

window.parseInt = function () {
    count += 1;
    return oldParseInt.apply(null, arguments); // 调用原函数
};

// 测试:
parseInt('10');
parseInt('20');
parseInt('30');
console.log('count = ' + count); // 3
```

### 高阶函数

一个函数就可以接收另一个函数作为参数，这种函数就称之为高阶函数。

#### map&reduce

map对原始输入做映射

reduce一般是累积操作，返回一个值

#### filter

#### sort

#### Array

### 闭包

闭包让函数可以"记住"它创建时的变量

```javascript
function lazy_sum(arr) {
    let sum = function () {
        return arr.reduce(function (x, y) {
            return x + y;
        });
    }
    return sum;
}
```

我复制了一下廖大的原文

```javascript
在这个例子中，我们在函数lazy_sum中又定义了函数sum，并且，内部函数sum可以引用外部函数lazy_sum的参数和局部变量，当lazy_sum返回函数sum时，相关参数和变量都保存在返回的函数中，这种称为“闭包（Closure）
```

### 箭头函数&标签函数&生成器

## 标准对象

### Date

### RegExp

### JSON

## 面向对象编程

js通过原型(prototype)来实现面向对象编程。

```javascript
let Student = {
    name: 'Robot',
    height: 1.2,
    run: function () {
        console.log(this.name + ' is running...');
    }
};

let xiaoming = {
    name: '小明'
};

xiaoming.__proto__ = Student;

xiaoming.name; // '小明'
xiaoming.run(); // 小明 is running...
```

![1738725252056](image/js_learning_note/1738725252056.png)

javascript的原型链和Java的clss就在于，它没有class的概念，要知道class是由jvm统一管理的，而js中的原型链上都是对象

### 创建对象

### 原型继承

### class继承

ES6之后有了class关键字

## 浏览器

### 浏览器对象

### 操作DOM

### 操作表单

### 操作文件

### AjAX(重点)

Asynchronous JavaScript and XML，意思就是用JavaScript执行异步网络请求

在现代浏览器上写AJAX主要依靠 `XMLHttpRequest`对象，如果不考虑早期浏览器的兼容性问题，现代浏览器还提供了原生支持的Fetch API，以[Promise](https://liaoxuefeng.com/books/javascript/browser/promise/index.html)方式提供。使用Fetch API发送HTTP请求代码如下：

```javascript
async function get(url) {
    let resp = await fetch(url);
    let result = await resp.text();
    return result;
}

// 发送异步请求:
get('./content.html').then(data => {
    let textarea = document.getElementById('fetch-response-text');
    textarea.value = data;
});
```

#### 跨域CORS

CORS全称Cross-Origin Resource Sharing，是HTML5规范定义的如何跨域访问资源

```
         GET /res/abc.data
         Host: sina.com
┌──────┐ Origin: http://my.com                      ┌────────┐
│my.com│────────────────────▶│sina.com│
│      │◀────────────────────│        │
└──────┘ HTTP/1.1 200 OK                            └────────┘
         Access-Control-Allow-Origin: http://my.com
         Content-Type: text/xml

         <xml data...>
```

Origin表示本域，也就是浏览器当前页面的域。当JavaScript向外域（如sina.com）发起请求后，浏览器收到响应后，首先检查 `Access-Control-Allow-Origin`是否包含本域，如果是，则此次跨域请求成功，如果不是，则请求失败，JavaScript将无法获取到响应的任何数据。

对于PUT、DELETE以及其他类型如 `application/json`的POST请求，在发送AJAX请求之前，浏览器会先发送一个 `OPTIONS`请求（称为preflighted请求）到这个URL上，询问目标服务器是否接受：

```plain
OPTIONS /path/to/resource HTTP/1.1
Host: bar.com
Origin: http://my.com
Access-Control-Request-Method: POST
```

服务器必须响应并明确指出允许的Method：

```plain
HTTP/1.1 200 OK
Access-Control-Allow-Origin: http://my.com
Access-Control-Allow-Methods: POST, GET, PUT, OPTIONS
Access-Control-Max-Age: 86400
```

浏览器确认服务器响应的 `Access-Control-Allow-Methods`头确实包含将要发送的AJAX请求的Method，才会继续发送AJAX，否则，抛出一个错误。

由于以 `POST`、`PUT`方式传送JSON格式的数据在REST中很常见，所以要跨域正确处理 `POST`和 `PUT`请求，服务器端必须正确响应 `OPTIONS`请求。

### Promise函数&async函数(重点)

```javascript
function callback() {
    console.log('Done');
}
console.log('before setTimeout()');
setTimeout(callback, 1000); // 1秒钟后调用callback函数
console.log('after setTimeout()');
```

#### Promise

Promise 是一个表示异**步操作最终完成或失败的对象**，它允许你**注册回调函数来处理操作的结果**

```javascript
let p1 = new Promise(test);
let p2 = p1.then(function (result) {
    console.log('成功：' + result);
});
let p3 = p2.catch(function (reason) {
    console.log('失败：' + reason);
});
```

Promise最大的好处是在异步执行的流程中，把执行代码和处理结果的代码清晰地分离了：

![1738726632955](image/js_learning_note/1738726632955.png)

#### async配合await调用Promise

异步函数和Promise可以看作是等价的，在async function内部，用 **`await`调用另一个异步函数**，写起来和同步代码没啥区别，但执行起来是异步的

```javascript
async function get(url) {
    let resp = await fetch(url);
    let result = await resp.json();
    return result;
}
```

注：普通函数调用异步函数报错。

在普通function中调用async function，不能使用await，但可以直接调用async function拿到Promise对象，后面加上 `then()`和 `catch()`就可以拿到结果或错误了：

```javascript
async function get(url) {
    let resp = await fetch(url);
    let result = await resp.text();
    return result;
}

function doGet() {
    let promise = get('./content.html');
    promise.then(data => {
        // 拿到data
        document.getElementById('test-response-text').value = JSON.stringify(data);
```

## 异常处理

```javascript
function main(s) {
    console.log('BEGIN main()');
    try {
        foo(s);
    } catch (e) {
        console.log('出错了：' + e);
    }
    console.log('END main()');
}

function foo(s) {
    console.log('BEGIN foo()');
    bar(s);
    console.log('END foo()');
}

function bar(s) {
    console.log('BEGIN bar()');
    console.log('length = ' + s.length);
    console.log('END bar()');
}

main(null);
```

### 错误传播

当前函数如果处理的话那么就不用

### 异步异常处理

回调函数内部处理

## JQuery

js的一个第三方库

`$`是著名的jQuery符号。实际上，jQuery把所有功能全部封装在一个全局变量 `jQuery`中，而 `$`也是一个合法的变量名，它是变量 `jQuery`的别名：

```javascript
window.jQuery; // jQuery(selector, context)
window.$; // jQuery(selector, context)
$ === jQuery; // true
typeof($); // 'function'
```

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

mac下推荐使用Homebrew进行安装，并使用版本管理工具进行管理

比较重要的事就是npm，也就是类似于Java项目中maven，用来管理项目中你所需要的依赖

开发的IDE推荐使用VSCode，可以安装插件

### 使用模块

#### 导出

写在js文件文件后面就可以

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

```
exports.hello=hello;
```

#### 导入

1. require

```javascript
'use strict';

// 引入hello模块:
const greet = require('./hello');

let s = 'Michael';

greet(s); // Hello, Michael!
```

2. import

```javascript
import { createReadStream } from 'node:fs';
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

##### 异步读文件

```javascript
// read-text-file-async.mjs
import { readFile } from 'node:fs';

console.log('BEGIN');

readFile('sample.txt', 'utf-8', function (err, data) {
    if (err) {
        console.log(err);
    } else {
        console.log(data);
    }
});

console.log('END');
```

##### 异步写文件

```javascript
// write-file-async.mjs
import { writeFile } from 'node:fs';

let data = 'Hello, Node.js';
writeFile('output.txt', data, function (err) {
    if (err) {
        console.log(err);
    }
});
```

##### 文件信息对象stat

```javascript
// read-stat-async.mjs
import { stat } from 'node:fs';

stat('sample.png', function (err, st) {
    if (err) {
        console.log(err);
    } else {
        // 是否是文件:
        console.log('isFile: ' + st.isFile());
        // 是否是目录:
        console.log('isDirectory: ' + st.isDirectory());
        if (st.isFile()) {
            // 文件大小:
            console.log('size: ' + st.size);
            // 创建时间, Date对象:
            console.log('birth time: ' + st.birthtime);
            // 修改时间, Date对象:
            console.log('modified time: ' + st.mtime);
        }
    }
```

注：无特殊需求的话都是要异步读写文件

#### 流处理stream

和Java中的stream一样，是一种抽象的数据结构

```javascript
import { createReadStream } from 'node:fs';

// 打开流:
let rs = createReadStream('sample.txt', 'utf-8');

// 读取到数据:
rs.on('data', (chunk) => {
    console.log('---- chunk ----');
    console.log(chunk);
});

// 结束读取:
rs.on('end', () => {
    console.log('---- end ----');
});

// 出错:
rs.on('error', err => {
    console.log(err);
});
```

#### 网络编程http(重点)

```javascript
// 导入http模块:
import http from 'node:http';

// 创建http server，并传入回调函数:
const server = http.createServer((request, response) => {
    // 回调函数接收request和response对象,
    // 获得HTTP请求的method和url:
    console.log(request.method + ': ' + request.url);
    // 将HTTP响应200写入response, 同时设置Content-Type: text/html:
    response.writeHead(200, {'Content-Type': 'text/html'});
    // 将HTTP响应的HTML内容写入response:
    response.end('<h1>Hello world!</h1>');
});

// 出错时返回400:
server.on('clientError', (err, socket) => {
  socket.end('HTTP/1.1 400 Bad Request\r\n\r\n');
});

// 让服务器监听8080端口:
server.listen(8080);
console.log('Server is running at http://127.0.0.1:8080/');
```

#### 加密crypto

### Web开发

#### koa
