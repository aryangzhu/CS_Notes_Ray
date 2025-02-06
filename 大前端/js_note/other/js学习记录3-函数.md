## 1.函数定义及变量作用域
#### Java定义方式
Java定义函数
public 返回值类型 方法名(){
	return 返回值；
}
#### JS定义方式一
绝对值函数

```javascript
function abs(x){
	if(x>=0){
    return x;
    }else{
    return -x;
    }
}
```
可以看出JS与Java相比,没有修饰符,没有返回类型,没有参数类型
#### JS定义方式二
var abs=function(x){
	
}
function(x){....}是一个匿名函数，但是**可以把结果赋值给abs**,通过abs就可以调用函数！
方式一和方式二等价

###### abs(x)调用方式\
一旦执行到return代表函数结束，返回结果
如果没有执行return，函数执行完也会返回结果，结果就是undefined
参数问题：JavaScript可以穿任意个参数也可以不传参数
参数进来是否存在的问题？
假设不存在参数，如何规避？
```js
 var abs=function (x) {
            //手动抛出异常
            if (typeof x!=='number'){
                throw 'Not a Number';
            }
            if (x>=0) {
                return x;
            }else{
                return -x;
            }
        }
```
###### arguments
arguments是一个JS免费赠送的一个关键字；
代表，传递进来的所有参数，都是一个数组！
问题：arguments包含所有的参数，我们有时候想使用多余的参数来进行附加操作，需要排除已有的参数- 

###### 使用rest
以前：
	if(arguments.length>2){
    	for(var i=2;i<arguments.length;i++){
        //...}
    }
**ES6引入的新特性，或去除了已经定义的参数之外所有参数**
function aaa(a,b,....rest){
	console.log("a=>"+a);
    console.log("b=>"+b);
    console.log(rest);
}

####  事件
为什么使用事件,当发生某个动作时需要做什么事情(做什么事情就由事件来指定)
例如,onmouseover(on当什么时候mouse鼠标over悬浮)
event:当前发生的事件;
event.srcElement:事件源
## 2.变量的作用域
'use strict'
在Javascript中，var定义变量实际是有作用域的
假设在函数体中，则在函数体外不可以使用～（闭包）
```js
 function aaa(){
            var x=1;
            x=x+1;
        }
        x = x+2;
```
**报错**：Uncaught ReferenceError: x is not defined
**如果两个函数使用了相同的变量名，在内部的话不影响使用，但是内部的函数不起作用**
```js
 function qj() {
            var x=1;

            function qj() {
                var x='A';
                console.log('inner'+x);
            }
            console.log('outer'+x);
            qj();
        }
        qj();
```
**内部函数可以访问外部函数的成员，反之则不行**
**假设在JavaScript中函数查找变量从自身函数开始,由"内"向"外"查找，假设外部存在这个同名的函数变量，则内部函数屏蔽外部函数**

#### 提升变量的作用域

```js
function qj(){	
	var x="x"+y;
    console.log(x);
    var y='y';
}
结果：x undefined 
```
说明：js执行引擎，自动提升了y的声明，但是不会提升y的赋值。
**这个是在JavaScript建立之初就存在的特性。养成规范：所有的变量定义都放在函数的头部，不要乱放，便于代码维护；**

#### 全局函数

```js
//全局变量
x=1;
function f(){
	console.log(x);
}
f();
console.log(x);
```
## 全局对象 window

```js
var x='xxx';
alert(x);
window.alert(window.x);//默认所有的全局变量，都会自动绑定在window下
```

alert()函数本身也是一个window的变量；
```js
var x='xxx';
window.alert(x);

var old_alert=window.alert(x);

wondow.alert=function(){

};
//发现alert()失效了
window.alert(123);

//恢复
window.alert=old_alert;
window.alert(456);
```
JavaScript实际上只有一个全局作用域，任何变量(函数也可以视为变量),假设没在函数作用域范围内找到，就会向外查找，如果在全局作用域内都没有找到，报错ReferenceError

#### 规范
由于我们所有的全局变量都会绑定到我们的window上，如果不同的js文件，使用了相同的全局变量，冲突->如何能减少冲突？
```js
//唯一全局变量
var KuangApp={};

//定义全局变量
KuangApp.name='kuangshen';
KuangApp.add=function(a,b){
	return a+b;
}
```
**把自己的代码全部放入自己定义的唯一命名空间中，降低全局命名冲突的问题**
jQuery

#### 局部作用域 let
```js
function aaa(){
	for(var i=0;i<100;i++){
    	console.log(i);
    }
    console.log(i+1);//问题？i出了这个作用域还可以使用
}
```
ES6 let关键字，解决局部作用域解决冲突问题
```js
	for(let i=0;i<100;i++){
	使用了let关键字，作用域外使用的话就会报错
```
#### 常量const
使用var定义的话可以对这个变量的值进行修改，而使用了const关键字之后无法对这个值进行修改
## 3.方法
#### 定义方法
方法就是把函数放在对象里，对象只有两个东西，属性和方法
```js
 var kuangshen={
            name:"leiliu",
            birth:2000,
            age:function () {
                var now=new Date().getFullYear();
                return now-this.birth;
            }
        }
```
注意对age进行调用的时候必须添加(),即kuangshen.age()才能成功返回结果
this代表什么？
this是无法指向的，是默认指向调用它的那个对象
```js
var kuangshen={
            name:"leiliu",
            birth:2000,
            age:getAge
        }
var getAge=function () {
                var now=new Date().getFullYear();
                return now-this.birth;
        }
```
kuangshen.age() //OK
但是 getAge() //NaN 默认指向window，但是window下没有age这个属性

#### apply
用来控制this指向
```js
   console.log(getAge.apply(kuangshen,[]));
```