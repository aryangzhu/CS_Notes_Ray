#### 1.选择器
作用：选择页面上的某一个或者某一类元素
​1.基本选择器:选择一类标签 标签{}
​2.类选择器 class：选择所有class属性一致的标签，跨标签 类名.{}
3.id选择器 全局唯一！#id{}

#### 2.层次选择器
##### 1.后代选择器：

在某个元素的后面 祖爷爷 爷爷 爸爸。。。

​    	 /*后代选择器*/
​        body p {
​            background: red;
​        }

##### 2.子选择器
​    	body>p {
​            background: chartreuse;
​        }

##### 3.相邻兄弟选择器
/*相邻兄弟选择器:注意：只有一个相邻,向下*/
​    	.active+p{
​            background: chocolate;
​        }

##### 4.通用选择器
​     `/*`通用兄弟选择器,当前选中元素的向下的所有兄弟元素*/`
​        `

```html
.active~p{
            `background: cyan;`
        }
<p>p0</p>
<p class="leiliu">p1</p>
<p>p2</p>
<p>p3</p>
<ul>
    <li>
        <p>p4</p>
    </li>
    <li>
        <p>p5</p>
    </li>
    <li>
        <p>p6</p>
    </li>
</ul>
<p>p7</p>
<p>p8</p>
```
#### 3.结构伪类选择器
​	 /*选中p1：定位到父元素,选择当前的第一个元素
​        选择当前p元素的父级元素,选中父元素的第一个,并且是当前元素才会生效 顺序*/
​        p:nth-child(2){
​            background: chocolate;
​        }

```css
/*选中父元素,下的p元素第二个,类型*/
    p:nth-of-type(2){
        background: coral;
    }

/*鼠标移动到元素位置会有阴影效果*/
    a:hover{
        background: cornflowerblue;
    }
```
```html
<h1>h1</h1>
<p>p1</p>
<p>p2</p>
<p>p3</p>
<ul>
    <li>li1</li>
    <li>li2</li>
    <li>li3</li>
</ul>
```
#### 4.属性选择器
将id和class进行结合 
1.=匹配
2.*=包含
3.^= 以等号后面的为开头进行匹配
4.$= 以等号后面的为结尾进行匹配

```css
 .demo a{

        }
        /*属性名,属性名=属性值(正则)*/

        /*id为firstt的元素*/
        a[id=first]{
            background: cornflowerblue;
        }

        /*class中有links的元素*/
        a[class="links"]{
            background: yellow;
        }
```