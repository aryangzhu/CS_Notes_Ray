## 介绍
ECMAScript6.0 是JavaScript的下一个版本标准
## 环境搭建
### Node.js环境中运行ES6
node.js对ES6支持度更高
### webpack
webpack是一个现代的JavaScript应用程序的静态模块打包器(module bundler)。
当webpack处理应用程序的时候，它会生成一个依赖关系图(dependency graph)，将应用程序打包一个或者多个bundle
#### webpack的四个概念
##### 入口(entry)
告诉webpack使用哪个模块作为**构建依赖关系图**的起点。
单个(简写)语法
```js
const config = {
  entry: "./src/main.js"
}
```
##### 输出(output)
output会告诉webpack在哪里存放它的bundle
```js
const config={
    entry:"./src/main.js",
    output:{
        filename:"bundle.js",
        path:path.reslove(xxx)
    }
}
```
##### loader
webpack只能处理js文件，所以需要loader将非js文件转换为webpack能处理的模块
##### 插件(plugin)
压缩、优化以及配置环境等
## let和const
### let
#### let声明的只在代码块内有效
通过例子来看更加直观
```js
{
    let a=1;
    var b=2;
}
a //undefined
b //2
```
#### 不能重复声明
let a=1;
let a=2;
a //'a'already exists
#### let适合在循环中使用
console.log(a);  //ReferenceError: a is not defined
let a = "apple";
console.log(b);  //undefined
var b = "banana";
变量 b 用 var 声明存在变量提升，所以当脚本开始运行的时候，b 已经存在了，但是还没有赋值，所以会输出 undefined  
变量 a 用 let 声明不存在变量提升，在声明变量 a 之前，a 不存在，所以会报错  
### const
const定义常量，常量的值不能再修改  
const PI = "3.1415926";  
PI  // 3.1415926  
const MY_AGE;  // SyntaxError: Missing initializer in const declaration     
## 解构赋值
对数组或者对象进行赋值  
### 数组
### 字符串
### Object对象
## Symbol对象
在js的Number、String、boolean、null和undefined基础上增加的一种数据类型  
用来标识唯一属性  
Symbol("OK")//不能new  
## Map和Set
### Map
#### key是字符串
#### key是对象
#### key是函数
#### key是NaN
#### 遍历
##### of
```js
for (var [key, value] of myMap) {
  console.log(key + " = " + value);
}
for (var [key, value] of myMap.entries()) {
  console.log(key + " = " + value);
}
/* 这个 entries 方法返回一个新的 Iterator 对象，它按插入顺序包含了 Map 对象中每个元素的 [key, value] 数组。 */
 
// 将会显示两个log。 一个是 "0" 另一个是 "1"
for (var key of myMap.keys()) {
  console.log(key);
}
/* 这个 keys 方法返回一个新的 Iterator 对象， 它按插入顺序包含了 Map 对象中每个元素的键。 */
 
// 将会显示两个log。 一个是 "zero" 另一个是 "one"
for (var value of myMap.values()) {
  console.log(value);
}
/* 这个 values 方法返回一个新的 Iterator 对象，它按插入顺序包含了 Map 对象中每个元素的值。 */
```
##### foreach
```js
var myMap = new Map();
myMap.set(0, "zero");
myMap.set(1, "one");
// 将会显示两个 logs。 一个是 "0 = zero" 另一个是 "1 = one"
myMap.forEach(function(value, key) {
  console.log(key + " = " + value);
}, myMap)
```
#### Map对象常见操作
##### Map与数组转换
```js
var kvArray = [["key1", "value1"], ["key2", "value2"]];  
// Map 构造函数可以将一个 二维 键值对数组转换成一个 Map 对象
var myMap = new Map(kvArray);  
// 使用 Array.from 函数可以将一个 Map 对象转换成一个二维键值对数组
var outArray = Array.from(myMap);  
```
##### Map合并
### Set
#### Set中几个特殊值
1.-0与+0在判断唯一性时不重复
2.undefined和undefined
3.NaN和NaN是不恒等的，只能存一个，不重复
#### Set与Array
```js
// Array 转 Set
var mySet = new Set(["value1", "value2", "value3"]);
// 用...操作符，将 Set 转 Array
var myArray = [...mySet];
String
// String 转 Set
var mySet = new Set('hello');  // Set(4) {"h", "e", "l", "o"}
// 注：Set 中 toString 方法是不能将 Set 转换成 String
```
#### Set的作用
交集、去重、并集
## Reflect和Proxy
### Proxy使用了代理模式，对方法增强
```js
let target = {
    name: 'Tom',
    age: 24
}
let handler = {
    get: function(target, key) {
        console.log('getting '+key);
        return target[key]; // 不是target.key
    },
    set: function(target, key, value) {
        console.log('setting '+key);
        target[key] = value;
    }
}
let proxy = new Proxy(target, handler)
proxy.name     // 实际执行 handler.get
proxy.age = 25 // 实际执行 handler.set
// getting name
// setting age
// 25
```
### Reflect与Object一样，更加优雅
```js
// 当 target 对象中存在 name 属性的 getter 方法， getter 方法的 this 会绑定 // receiver
let receiver = {
    name: "Jerry",
    age: 20
}
Reflect.get(exam, 'info', receiver); // Jerry20  
// 当 name 为不存在于 target 对象的属性时，返回 undefined
Reflect.get(exam, 'birth'); // undefined  
```
## ES6字符串
### 字串识别
includes()：返回布尔值，判断是否找到参数字符串  
startsWith()：返回布尔值，判断参数字符串是否在原字符串的头部  
endsWith()：返回布尔值，判断参数字符串是否在原字符串的尾部  
### 字符串重复
repeat()
### 字符串补全
```js
console.log("h".padStart(5,"o"));  // "ooooh"
console.log("h".padEnd(5,"o"));    // "hoooo"
console.log("h".padStart(5));      // "    h"
```
### 模板字符串
可以定义多行字符串,``来定义  
```js
let string1 =  `Hey,
can you stop angry now?`;
```
### 标签模板
## ES6数值
感兴趣直接可以看网上教程  
## ES6数组
### 数组创建
#### Array.of()
#### Array.form()
将类数组对象或可迭代对象转化为数组  
### 类数组对象
必须得有length  
### 转换
### 扩展
#### 查找
## ES6对象
### 对象字面量
#### 属性简写
```js
let name="aa";
let age=12
let person={name,age}
// 等同于
let person={name:"aa",age:12}
```
#### 方法简写
```js
const person={
    sayHi(){
        console.log("xxx");
    }
}
// 等同于
const person={
    sayHi:function(){
        console.log("xxx");
    }
}
```
#### 属性名表达式
```js
const obj = {
 ["he"+"llo"](){
   return "Hi";
  }
}
```
### 对象运算符扩展
(...)取出参数所有可遍历的属性拷贝到当前对象
#### 基本用法
```js
let person = {name: "Amy", age: 15};
let someone = { ...person };
someone;  //{name: "Amy", age: 15}
```
## ES6函数
### 默认参数
### 不定参数
...
### 箭头函数
```js
var f = v => v;
//等价于
var f = function(a){
 return a;
}
f(1);  //1
```
## ES6Class类
在ES6中，class (类)作为对象的模板被引入，可以通过 class 关键字定义类。
class 的本质是 function。
它可以看作一个语法糖，让对象原型的写法更加清晰、更像面向对象编程的语法。
## ES6模块
ES6 引入了模块化，其设计思想是在编译时就能确定模块的依赖关系，以及输入和输出的变量。
### import和export
#### 基本用法
模块导入导出各种类型的变量，如字符串，数值，函数，类。
1. 导出的函数声明与类声明必须要有名称（export default 命令另外考虑）。 
2. 不仅能导出声明还能导出引用（例如函数）。
3. export 命令可以出现在模块的任何位置，但必需处于模块顶层。
4. import 命令会提升到整个模块的头部，首先执行。
```java
/*-----export [test.js]-----*/
let myName = "Tom";
let myAge = 20;
let myfn = function(){
    return "My name is" + myName + "! I'm '" + myAge + "years old."
}
let myClass =  class myClass {
    static a = "yeah!";
}
export { myName, myAge, myfn, myClass }
 
/*-----import [xxx.js]-----*/
import { myName, myAge, myfn, myClass } from "./test.js";
console.log(myfn());// My name is Tom! I'm 20 years old.
console.log(myAge);// 20
console.log(myName);// Tom
console.log(myClass.a );// yeah!
```
#### as的用法
export 命令导出的接口名称，须和模块内部的变量有一一对应关系。
导入的变量名，须和导出的接口名称相同，即顺序可以不一致。
```js
/*-----export [test.js]-----*/
let myName = "Tom";
export { myName as exportName }
```
#### import 的特点
##### 只读
##### 单例
#### export default命令
1. 在一个文件或模块中，export、import 可以有多个，**export default 仅有一个**。
2. export default 中的 **default 是对应的导出接口变量**。
3. 通过 export 方式导出，在导入时要加{ }，export default 则不需要。
4. export default 向外暴露的成员，可以使用任意变量来接收。
```js
var a = "My name is Tom!";
export default a; // 仅有一个
export default var c = "error"; 
// error，default 已经是对应的导出变量，不能跟着变量声明语句
 
import b from "./xxx.js"; // 不需要加{}， 使用任意变量接收
```
### 复合使用
export 与 import 可以在同一模块使用，使用特点： 
可以将导出接口改名，包括 default  
复合使用 export 与 import ，也可以导出全部，当前模块导出的接口会覆盖继承导出的  
## Promise对象
异步编程的解决方案  
从语法上说，Promise 是一个对象，从它可以获取**异步操作**的消息  
### 状态
pending（进行中）、fulfilled（已成功）和 rejected（已失败）  
#### resolved()定型方法
可以定型为success和reject  
### then方法(重点)
then 方法接收两个函数作为参数，第一个参数是 Promise 执行成功时的回调，第二个参数是 Promise 执行失败时的回调，两个函数只会有一个被调用  
```js
const p = new Promise(function(resolve,reject){
  resolve(1);
}).then(function(value){ // 第一个then // 1
  console.log(value);
  return value * 2;
}).then(function(value){ // 第二个then // 2
  console.log(value);
}).then(function(value){ // 第三个then // undefined
  console.log(value);
  return Promise.resolve('resolve'); 
}).then(function(value){ // 第四个then // resolve
  console.log(value);
  return Promise.reject('reject'); 
}).then(function(value){ // 第五个then //reject:reject
  console.log('resolve:' + value);
}, function(err) {
  console.log('reject:' + err);
});
```
## Generator函数
ES6 新引入了 Generator 函数，可以通过 yield 关键字，把函数的执行流挂起，为改变执行流程提供了可能，从而为异步编程提供解决方案  
### 函数组成
function后面有个*  
内部有 yield  
### 执行机制
```js
f.next();
// one
// {value: "1", done: false}
 
f.next();
// two
// {value: "2", done: false}
 
f.next();
// three
// {value: "3", done: true}
 
f.next();
// {value: undefined, done: true}
```
 Generator 函数不会像普通函数一样立即执行，而是**返回一个指向内部状态对象的指针**，所以要调用遍历器对象Iterator 的 next 方法，**指针就会从函数头部或者上一次停下来的地方开始执行**  
1. 第一次调用 next 方法时，从 Generator 函数的头部开始执行，先是打印了 one ,执行到 yield 就停下来，并将yield 后边表达式的值 '1'，作为返回对象的 value 属性值，此时函数还没有执行完， 返回对象的 done 属性值是 false  
2. 第二次调用 next 方法时，同上步  
3.第三次调用 next 方法时，先是打印了 three ，然后执行了函数的返回操作，并将 return 后面的表达式的值，作为返回对象的 value 属性值，此时函数已经结束，多以 done 属性值为true   
4. 第四次调用 next 方法时， 此时函数已经执行完了，所以返回 value 属性值是 undefined ，done 属性值是 true 。如果执行第三步时，没有 return 语句的话，就直接返回 {value: undefined, done: true}  
## async 函数
ES7 异步操作关键字
### 语法
name: 函数名称。
param: 要传递给函数的参数的名称。
statements: 函数体语句。
### 返回值
async 函数返回一个 Promise 对象，可以使用 then 方法添加回调函数。
### await
await 操作符用于等待一个 Promise 对象, 它只能在异步函数 async function 内部使用。
async 函数中可能会有 await 表达式，async 函数执行时，如果遇到 await 就会先暂停执行 ，等到触发的异步操作完成后，恢复 async 函数的执行并返回解析值。
await 关键字仅在 async function 中有效。如果在 async function 函数体外使用 await ，你只会得到一个语法错误。
```js
function testAwait(){
   return new Promise((resolve) => {
       setTimeout(function(){
          console.log("testAwait");
          resolve();
       }, 1000);
   });
}
 
async function helloAsync(){
   await testAwait();
   console.log("helloAsync");
 }
helloAsync();
// testAwait
// helloAsync
```
#### 返回值
