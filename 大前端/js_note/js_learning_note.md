## 基础部分

### 基本数据类型和变量

### 字符串

### 数组

### 对象

### 条件判断

### 循环

### Map和Set

### iterable

## 函数

### 函数定义和调用

### 变量作用域与解构值

### 方法

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

1. import
2. require

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
3. 判断执行环境

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
