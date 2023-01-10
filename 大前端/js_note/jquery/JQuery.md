# jQuery
jQuery库，里面存着大量的Javascript函数
#### 获取
可以去官网进行下载也可以使用cdn在线版本
#### 选择器
$()将括号里的对象包装成一个可以被jquery识别的对象
1.传入一个字符串选择器$('#id')
2.传入一个匿名函数,网页加载完成后执行
**$(function(){})**
3.将JavaScript对象包装为jQuery对象
**this和$(this)的区别**
this只是
了解过基本的html、css和js之后，我们就可以去尝试去看官方文档
css中的选择器它都能用
jQuery官方文档
```
$('p').click();//标签选择器
$('#id1').click(); //id选择器
$('.class').click(); //class选择器
```
#### 事件
鼠标事件，键盘事件，其他事件
```html
<script>
    // $(document).ready(function () {
    //
    // })
    $(function () {
        $("#divMove").mousemove(function (e) {
            $("#mouseMove").text('x:'+e.pageX+'y:'+e.pageY);
        })
    })
</script>
```
#### 操作DOM
**节点文本操作**
```js
$('#test-ul li[name=python]').text(); //获得值
$('#test-ul li[name=python]').text('aaa');//设置值
$('#test-ul').html(); //获得值
$('#test-ul').html(); //设置值
CSS的操作
$('#test-ul li[name=python]').css({"color","red"});
```
#### 元素的显示和隐藏

**本质display:none**
$('#test-ul li[name=python]').show();
$('#test-ul li[name=python]').hide();
**未来**
```js
$('#form').ajax();
$.ajax({url:"test.html",context:document.body,successs:function(){
    $(this).addClass("done");
}
});
```