# 介绍vue 
## 前端都有什么部分(前端的核心)
视图层 HTML+CSS+JS 
功能:
1.显示数据  
2.解析后端传回来的数据  
Vue采用了关注点分离原则进行开发，它只关注视图层。  
然而，前端还包含其他部分,所以vue也有一些组件完成这些功能。  
网络通信:axios  
页面跳转:vue-router  
状态管理:vuex 
VUE_UI:ICE
这里还得回顾一下MVC模式，即使我们重复过许多遍，但是它还是整个开发中最重要的一环。
MVC->MVVM  
M:Data  
V:View  
VM:ViewModel 双向绑定数据???  
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
## 打包
npm run build打包之后会生成一个dist目录,里面就是生成的静态资源
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
# Vue.js目录结构
## 导入组件
```js
import Hello from './components/Hello'

export default{
    name:'app',
    components:{
        Hello
    }
}
```
# Vue.js起步
每个Vue应用都需要通过实例化Vue来实现
```js
var vm=new Vue({
    //选项
})
```
## 基本语法
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
v-on命令用来监听DOM事件
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
## 模板语法
也就是对HTML元素节点进行一些操作
{{}}文本值
### v-html输出HTML代码
```html
<div id="app">
    <div v-html="message"></div>
</div>

<script>
new Vue({
    el:'#app',
    data:{
        message:'<h1>菜鸟教程</h1>'
    }
})
</script>
```
### v-bind设置标签属性
```html
<div v-bind:class="{'class1':use}"></div>
```
### 指令
带有v-前缀的特殊属性
## 计算属性
关键词 computed
```html
<script>
var vm=new Vue({
    el:'#app',
    data:{
        message:'Runoob!'
    },
    computed:{
        reversedMessage:function(){
            return this.message.split('').reverse().join('');
        }
    }
})
</script>
```
上面声明了一个计算属性reversedMessage
提供的函数将用作的vm.reverseMessage的getter,vm.reversedMessage依赖于vm.message，在vm.message发生改变时,vm.reversedMessage也会更新。
computed 和mehods
computed是基于它的依赖缓存，只有相关依赖发发生改变才会重新获取值。
## 样式绑定
v-bind:class="{''active':isActive}"
为v-bind:class属性绑定一个对象，从而动态切换class
### 数组语法
```html
<div v-bind:class="[activeClass, errorClass]"></div>
```
### Vue.js.style(内联样式)
```html
<div id="app">
    <div v-bind:style="{ color: activeColor, fontSize: fontSize + 'px' }">菜鸟教程</div>
</div>
```
## 事件处理器
v-on指令来绑定事件
## Vue.js表单
### 双向绑定
v-model会根据控件类型自动选取正确方法来更新元素
```html
  <input v-model="message" placeholder="编辑我……">
```
单选框绑定
```html
  <input type="radio" id="google" value="Google" v-model="picked">
```
复选框绑定
```html
<input type="checkbox" id="runoob" value="Runoob" v-model="checkedNames">
   <input type="checkbox" id="runoob" value="Runoob" v-model="checkedNames">
```
下拉框列表
```html
<select v-model="selected" name="fruit">
```
##  Vue.js组件
组件可以扩展HTML元素，封装可重用的代码
```html
<div id="app">
    <runoob></runoob>
</div>
 
<script>
// 注册
Vue.component('runoob', {
  template: '<h1>自定义组件!</h1>'
})
// 创建根实例
new Vue({
  el: '#app'
})
</script>
```
## 自定义指令
## Vue.js路由
安装vue-router
```shell
cnpm install vue-router
```
### 引入router相关的js
### 编写代码
html中使用
```html
<!-- <router-link> 默认会被渲染成一个 `<a>` 标签 -->
 <router-link to="/foo">Go to Foo</router-link>
    <router-link to="/bar">Go to Bar</router-link>
    <!-- 路由出口-->
    <!-- 路由匹配到的组件将渲染在这里 -->
    <router-view></router-view>
```
js中使用
```js
<!--1.自定义组件，可以从其他地方导入进来 -->
const Foo={template:'<div>foo</div>'}
<!-- 2.定义路由 -->
const routes={
    {path:'/foo',component:Foo},
}
<!-- 3.创建router实例 -->
const router=new VueRouter({
    routes
})
    
<!-- 4.创建和挂载实例 -->
const app=new Vue({
    router
}).$mount('#app')
```
### 简单实例
vue-js+vue-router可以很简单的实现单页应用。
使用/<router-link>设置一个导航链接，切换不同的HTML内容。
# Vue.js Ajax(axios)
## GET请求
``` js
new Vue({
  el: '#app',
  data () {
    return {
      info: null
    }
  },
  mounted () {
    axios
      .get('https://www.runoob.com/try/ajax/json_demo.json')
      .then(response => (this.info = response))
      .catch(function (error) { // 请求失败处理
        console.log(error);
      });
  }
})
```
上面的方法很容易看出来then(response=>(this.info=response))将response的内容赋值给info
## 使用response.data读取JSON数据
```js
new Vue({
  el: '#app',
  data () {
    return {
      info: null
    }
  },
  mounted () {
    axios
      .get('https://www.runoob.com/try/ajax/json_demo.json')
      .then(response => (this.info = response.data.sites))
      .catch(function (error) { // 请求失败处理
        console.log(error);
      });
  }
})
```
# Vue.js实例
直接参照菜鸟教程即可
https://www.runoob.com/vue2/vue-examples.html
# 组合式API
