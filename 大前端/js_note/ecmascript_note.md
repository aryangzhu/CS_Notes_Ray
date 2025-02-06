## 前言

[入门教程](https://es6.ruanyifeng.com/#docs)

### ES6与JavaScript的关系

其实就是js这种浏览器脚本语言先出来，然后将其提交给了ECMA组织，希望称为所有浏览器的标准语言，但是因为商标问题，其他厂商不能叫JavaScript，因为这不只是个语言，还是个商标，所以其他厂商知能将其称之为ECMA。

## 与ES5相比迭代

### let和const命令

在var的基础上添加了let(块级)和const(常量)

所谓的块就是{}包裹起来的作用域，以及for(let i=0;i<100;i++)

### 解构赋值

就是同时对多个变量赋值

### 字符串的扩展

就是对原来的一些api进行扩展，同时兼容更多编码类型。

还有添加了模板字符。

### 字符串新增的方法

新增了一些api

### 正则的扩展

修改了一下es5中的定义，修改了一下函数定义。

## 编程风格

主要就是和JavaScript传统编程作以区分。

#### 块级作用域

##### let取代var

```javascript

'use strict';


if (true) {

letx = 'hello';

    }


for (leti = 0; i < 10; i++) {

console.log(i);

    }

```

##### 全局变量和线程安全

用const代替let来声明全局变量

#### 字符串

使用单引号或者反引号

#### 解构赋值

#### 对象

对象尽量静态化，如果添加属性不可避免，要使用Object.assign方法

#### 数组

使用扩展运算符(...)拷贝数组

```javascript

// bad

constlen = items.length;

constitemsCopy = [];

leti;


for (i = 0; i < len; i++) {

itemsCopy[i] = items[i];

    }


// good

onstitemsCopy = [...items];

```

#### 函数

立即执行的函数可以写成箭头函数的形式

```javascript

(()=>{

console.log('hello world!');

})();

```

#### Map结构

注意区分 Object 和 Map，只有模拟现实世界的实体对象时，才使用 Object。如果只是需要key: value的数据结构，使用 Map 结构。因为 Map 有内建的遍历机制

#### Class

总是用Class关键字来取代需要prototype的操作。因为Class的写法更加简洁，更易读。

```

// bad

function Queue(contents = []) {

  this._queue = [...contents];

}

Queue.prototype.pop = function() {

  const value = this._queue[0];

  this._queue.splice(0, 1);

  return value;

}


// good

class Queue {

  constructor(contents = []) {

    this._queue = [...contents];

  }

  pop() {

    const value = this._queue[0];

    this._queue.splice(0, 1);

    return value;

  }

}

```

**使用extends实现继承**

```javascript

// bad

constinherits = require('inherits');

functionPeekableQueue(contents) {

Queue.apply(this, contents);

}

inherits(PeekableQueue, Queue);

PeekableQueue.prototype.peek = function() {

returnthis._queue[0];

}


// good

classPeekableQueueextendsQueue {

peek() {

returnthis._queue[0];

  }

}

```

#### 模块

虽然我们在nodes.js中学习了CommonJS的语法，但还是要用import的写法

```javascript

// CommonJS 的写法

constmoduleA = require('moduleA');

constfunc1 = moduleA.func1;

constfunc2 = moduleA.func2;


// ES6 的写法

import { func1, func2 } from'moduleA';

```

其次，使用 `export`取代 `module.exports`。

```javascript

// commonJS 的写法

varReact = require('react');


varBreadcrumbs = React.createClass({

render() {

return<nav/>;

  }

});


module.exports = Breadcrumbs;


// ES6 的写法

importReactfrom'react';


classBreadcrumbsextendsReact.Component {

render() {

return<nav/>;

  }

};


exportdefaultBreadcrumbs;

```

#### ESLint的使用

vscode中安装插件即可。
