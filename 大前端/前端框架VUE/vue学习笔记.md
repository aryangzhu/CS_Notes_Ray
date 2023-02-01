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
## npm介绍
Node包管理器
https://www.freecodecamp.org/chinese/news/what-is-npm-a-node-package-manager-tutorial-for-beginners/
这篇文章叙述得很清楚,就和maven的作用一样
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
vue.js提供一个官方命令行工具，用于构建大型单页应用  
全局安装 vue-cli  
```
$ cnpm install --global vue-cli
```
创建一个基于 webpack 模板的新项目  
webpack是一个打包工具,将模块的依赖关系进行分析,最后打包成为静态资源。
```
$ vue init webpack my-project
```
这里需要进行一些配置，默认回车即可  
# Vue.js目录结构
## 目录解析
### src
1. assets: 放置一些图片
2. components: 目录里面放入了一个组文件,可以不用
3. App.vue: 项目入口文件,上面的文件可以不用而将组件放在这里
4. main.js: 项目的核心文件
5. index.css: 样式文件
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
## 打包项目
npm run build打包之后会生成一个dist目录,里面就是生成的静态资源
## 创建项目
npm init 
npm ui命令
# Vue.js起步
每个Vue应用都需要通过实例化Vue来实现???
```js
var vm=new Vue({
    //选项
})
```
通过一段代码来看是最直观的,Vue2创建Vue实例时都是new Vue();而Vue3的时候是通过Vue.createApp()方法来进行创建。  
new Vue()方案中都是在实例中通过el属性来挂载DOM元素,而createApp()方案则是通过mount()方法来挂载DOM实例。  
```html
<div id="hello-vue" class="demo">
  {{message}}
</div>
<script>
  const HelloVueApp={
    data(){
      return{
        message: 'Hello Vue!!'
      }
    },
    methods:{
      increment(){
        // 'this'指向该组件实例
        this.count++
      }
    }
  }

  Vue.createApp(HelloVueApp).mount('#hello-vue');
</script>
```
上面的代码中有3个关键点
createApp()来创建应用
mount()将应用挂载到DOM节点
data()将数据包裹在组件实例
再进一步,可以在组件中添加方法
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
这里需要提一下，**vm会将data所有的属性放在对象vm里面**。
就是说data.site===vm.site为true。  
**methods**用于定义函数。
{{}}用于输出对象属性和函数返回值，可以和v-on搭配绑定事件 。
v-on命令用来监听DOM事件
```html
<div v-on="details"></div>
```
### 判断循环
#### 判断
v-if
```html
<p v-if="type==='A'">A</p>
<p v-else-if="type==='B'">B</p>
<p v-else="type==='C'">C</p>
```
#### 循环
v-for
```html
<div id="app">
  <ol>
    <li v-for="site in sites">
       {{ site.text }}
    </li>
  </ol>
</div>
<script>
  const app={
    data(){
      return{
        sites:[
          {text:'Google'},
          {text:'Runoob'},
          {text:'TaoBao'}
        }
      ]
    }
  }
Vue.createApp(app).mount('#app');
</script>
```
## 模板语法
Vue使用了基于HTML的模板语法,允许开发者声明式地将DOM绑定至底层Vue实例的数据  
### 文本
也就是对HTML元素节点进行一些操作
{{......}}
```html
<div>
  <p>{{message}}</p>
</div>
```
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
### 表达式
{{5+5}}
{{ok?'YES':'NO'}}
### 指令
带有v-前缀的特殊属性
例如,<p v-if="seen"></p>
v-if就是一个特殊属性
### 参数
v-bind:参数
### 修饰符
### 用户输入
v-model
## 组件
组件是Vue.js最重要的功能之一  
### 注册全局组件语法
```js
//创建应用
const app=Vue.createApp({....})

// 定义组件名
app.component('my-component-name',{
  /*...*/
})
```
参考示例有现成的代码
### 注册局部组件
```js
const ComponentA={
  /*...*/
}

const ComponentB={
  /*...*/
}
```
将局部组件保存在一个js文件中,前端直接引入进来(说是这么说,咱也不知道真实应用是什么情况)
接下来就是在创建应用时中使用自己定义的局部组件
```html
const app=Vue.createApp({
  components:{
    'component-a':ComponentA,
    'component-b':ComponentB
  }
})
```
### Prop
官方文档说的都是父组件向子组件传递值,我的理解是DOM与应用实例之间的的数据传递,如下示例:
```html
<div id="app">
  <!-- 使用自定义组件 -->
  <site-name title="Google"></site-name>
  <site-name title="Runoob"></site-name>
  <site-name title="Taobao"></site-name>
</div>
 
<script>
  // 创建应用实例
const app = Vue.createApp({})
 
 // 注册组件
app.component('site-name', {
  props: ['title'],
  template: `<h4>{{ title }}</h4>`
})
 
// 挂载
app.mount('#app')
</script>
```
#### 动态Prop
和静态的相比起来,
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
**提供的函数将用作的vm.reverseMessage的getter**,vm.reversedMessage依赖于vm.message，在vm.message发生改变时,vm.reversedMessage也会更新。
computed 和mehods
computed是基于它的依赖缓存，只有相关依赖发发生改变才会重新获取值。
## 监听属性
```html
<div id = "app">
    <p style = "font-size:25px;">计数器: {{ counter }}</p>
    <button @click = "counter++" style = "font-size:25px;">点我</button>
</div>
   
<script>
const app = {
  data() {
    return {
      counter: 1
    }
  }
}
vm = Vue.createApp(app).mount('#app')

vm.$watch('counter',function(nval,oval){
  alert('计数器的值从'+oval+'变为'+nal+'!');
})
</script>
```
从上面的代码中可以看出来,vm.$watch()方法来进行监听属性
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
v-on指令来来监听DOM事件,从而执行JavaScript代码。
v-on指令可以缩写为@符号
```
v-on:click="methodName"
@click="methodName"
```
## Vue表单
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
# Vue.js路由vue-router
为了方便构建单页面应用？？？
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
// 1. 定义路由组件.
// 也可以从其他文件导入
const Home = { template: '<div>Home</div>' }
const About = { template: '<div>About</div>' }
 
// 2. 定义一些路由
// 每个路由都需要映射到一个组件。
// 我们后面再讨论嵌套路由。
const routes = [
  { path: '/', component: Home },
  { path: '/about', component: About },
]
 
// 3. 创建路由实例并传递 `routes` 配置
// 你可以在这里输入更多的配置，但我们在这里
// 暂时保持简单
const router = VueRouter.createRouter({
  // 4. 内部提供了 history 模式的实现。为了简单起见，我们在这里使用 hash 模式。
  history: VueRouter.createWebHashHistory(),
  routes, // `routes: routes` 的缩写
})
 
// 5. 创建并挂载根实例
const app = Vue.createApp({})
//确保 _use_ 路由实例使
//整个应用支持路由。
app.use(router)
 
app.mount('#app')
```
### 简单实例
vue-js+vue-router可以很简单的实现单页应用。
使用/<router-link>设置一个导航链接，切换不同的HTML内容。
**个人理解:router就是在一个页面中如果通过链接来切换组件的话那么就可以用这玩意儿**
# Vue.js Ajax(axios)
因为Vue版本更加推荐使用axios来完成ajax请求,所以才会有这么个玩意儿诞生。
Axios是一个基于Promise的Http库,可以用在浏览器和node.js中。
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
# js-cookie
一个专门用来处理cookie的js函数库
## 基础用法
### 存储
保存cookie  
Cookies.set('name','value')
设置过期时间  
Cookies.set('name','value',{expire:7})
设置页面路径  
github上的说明是这个属性指示的是cookie可见的路径,所以我的理解是这个cookie只能在指定的页面使用???
Cookes.set('name','value',{expire:7,path:''})
### 读取
Cookies.get('name')
Cookies.get() 获取所有可见的cookie
### 删除
Cookies.remove('name')
Cookies.remove('name',{path:''})
Cookies.remove('name',{path:'',domain:'.yourdomain.com'})
### 属性说明
1. expires
2. path
3. domain
用来指示子域Cookie可见
# Element-UI
## 安装
npm i element-ui -S
基于Vue2的组件  
## 简单使用
```js
var Main={
  data: ()=>{
    show:true;
  }
}

var Hello=Vue.extend(Main);

Hello.mount("#app"); //从这里能够看出Vue2中也有mount()用于绑定DOM元素
```
# nprogress
一款基于JavaScript的进度条UI组件
## 安装
```html
<script src='nprogress.js'></script>
<link rel='stylesheet' href='nprogress.css'/>
```
## 基础使用
Nprogress.start();
Nprogress.done();
github上提到了说使用不同的xxx(搜了一下Turbolinks是一个typescript的项目,单页面跳转另一个页面)就有不同的写法
## 进阶使用
```js
Nprogress.set(0.0); //和start()方法一样
Nprogress.set(0.4);
Nprogress.set(1.0); //和done()方法一样

Nprogress.incr();  //随机增加进度

Nprogress.incr(0.1); //增加指定进度

Nprogress.done(true);
```
## 配置
用法都是NProgress.configure({...})
1. minimum最小值
NProgress.configure({minimum:0.1});
2. template模板
NProgress.configure({
  template:"<div class=''></div>"
});
3. easing and spped缓和速度
需要参数easing的css格式和speed
NProgress.configure({
  easing:'ease',
  speed:500
})
4. parent 父容器
NProgress.configure({parent:'#container'});
# path-to-regexp
将路径字符串(如/user/:name)转换为正则表达式的工具库。
## 安装
npm install path-to-regexp --save
## 基础使用
首先我们需要理解的是vue前端的路径是/foo/:bar这样式儿的(教程是这么说明的),然后再来看它提供的Api  
使用之前先要进行引入  
```js
var pathToRegexp=require('path-to-regexp')
```
pathToRegexp()  
这个方法会根据参数生成一个标准的正则表达式
```js
var re=pathToRegexp('/foo/:bar')
// /^\/foo\/((?:[^\/]+?))(?:\/(?=$))?$/i
```
exec()  
用来验证url与验证规则是否相符  
```js
var re=pathToRegexp('foo/:bar'); //匹配规则
var match1=re.exec('test/route'); //url路径
var match2=re.exec('foo/route');

//match1肯定是匹配不到的
```
parse()  
解析url中的参数部分,当然前提是得按匹配规则来写  
```js
var url='/user/:id';
console.log(pathToRegexp.parse(url));
```
complile()
快速填充url字符串的参数值
```js
var url='/user/:id/:name';
var data={id:10001,name:'bob'}
console.log(pathToRegexp.complile(url)(data));
```
