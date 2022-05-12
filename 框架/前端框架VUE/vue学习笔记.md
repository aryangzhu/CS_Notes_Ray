# 介绍vue 
## 前端都有什么部分(前端的核心)
视图层 HTML+CSS+JS 
功能:
1.显示数据

2.解析后端传回来的数据

vue采用了关注点分离原则进行开发，它只关注视图层。

然而，前端还包含其他部分,所以vue也有一些组件完成这些功能。

网络通信:axios

页面跳转:vue-router

状态管理:vuex

VUE_UI:ICE

这里还得回顾一下MVC模式，即使我们重复过许多遍，但是它还是整个开发中最重要的一环。

MVC->MVVM
M:Data
V:View
VM:双向绑定数据???
集大成者:MVVM+Dom
## 前端发展史
前端有许多UI框架，例如BootStrap、ElementUI等等。
# vue入门
## 快速开始
### 1.独立下载
### 2.\<script>引入
```javascript
<body>
<script src="https://unpkg.com/vue@2.6.14/dist/vue.min.js"></script>
<div id="app">
    <p>{{message}}</p>
</div>
<script>
    var vm=new Vue({
        el:"#app",
        data:{
            message:"Hello Vue"
        }
    });
</script>
```
###  3.着重强调一下npm方法安装
国内直接使用cnpm命令行工具
```
cnpm install vue
```
vue.js提供一个官方命令行工具，用于构建大型单页应用。

全局安装 vue-cli
```
$ cnpm install --global vue-cli
```
创建一个基于 webpack 模板的新项目
```
$ vue init webpack my-project
```
这里需要进行一些配置，默认回车即可

## 语法
如果想要学习这种类JSTL语法，我们只需要以下这四个语法
前提，我们先学习一些简单的语法
```html
<div id="vue_det">
    <h1>site : {{site}}</h1>
    <h1>url : {{url}}</h1>
    <h1>{{details()}}</h1>
</div>
<script type="text/javascript">
    var vm = new Vue({
        el: '#vue_det',
        data: {
            site: "菜鸟教程",
            url: "www.runoob.com",
            alexa: "10000"
        },
        methods: {
            details: function() {
                return  this.site + " - 学的不仅是技术，更是梦想！";
            }
        }
    })
</script>
```
**el**指的是DOM元素节点的id。  
**data**用于定义属性。  
这里需要提一下，vm会将data所有的属性放在对象vm里面。
就是说data.site===vm.site为true。  
**methods**用于定义函数。
{{}}用于输出对象属性和函数返回值，可以和v-on搭配绑定事件 。
```html
<div v-on="details"></div>
```
### 判断循环
#### 判断
```html
<p v-if="type==='A'">A</p>
<p v-else-if="type==='B'">B</p>
<p v-else="type==='C'">C</p>
```
#### 循环
```html
<li v-for="item in items"></li>
```