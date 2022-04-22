### 美化网页
#### 1.为什么
​	1.有效的传递页面信息
​    2.美化网页，页面漂亮，才能吸引用户
​    3.凸显页面的主题
​    4.提高用户的体验
​    

	span标签：重点要突出的字，使用span套起来

#### 2.字体样式
​	 <!--
​    font-family：字体
​    font-size:字体大小
​    font-wieght:字体粗细
​    line-height:行高
​    text-indent:缩进-->

```html
    <style>
        body {
            font-family: Vemana2000;
        }
		h1 {
            font-size: 50px;
    	}
    	.p1{
        	font-weight:700;
    	}
	</style>
```
#### 3.文本样式
​	1.颜色
​    2.文本对齐的方式
​    3.首行锁紧
​    4.行高
​    5.装饰
#### 4.超链接伪类和阴影
超链接伪类
a:link--链接
a:hover--鼠标悬浮
a:active--鼠标按住未释放
a;visited--已经访问过的链接

```html
  /*鼠标悬浮的颜色*/
        a:hover{
            color:orange;
            font-size: 50px;
        }

        /*鼠标按住未释放的状态*/
        a:active{
            color: green;
        }
```
阴影
text-shadow:阴影颜色，水平位移，垂直偏移，阴影半径

#### 5.列表

```
/*
list-style:
    none:去掉原点
    circle:空心圆
    decimal:数字
    square:正方形
 */
 子选择器+列表样式
 ul li{
    height:30px;
    list-style: none;
    text-indent: 1em;
}

```
#### 6.背景 

```
	第一种
    background:red url("../img/选区_118.png") 265px 0px no-repeat;
    第二种
    background-image: url("../img/选区_118.png");
    background-repeat: no-repeat;
    background-position: 236px 2px;
    
```
#### 7.渐变