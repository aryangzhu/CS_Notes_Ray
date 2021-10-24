### 浮动
#### 1.标准文档流

>块级元素:独占一行 h1~h6 p div 列表...
>行内元素:不独占一行 span a img strong

注意：行内元素可以被包含在块级元素之中，反之则不可以

#### 2.display

```
    <!--block块元素
    inline 行元素
    inline-block 是块元素,但是可以内联,在一行
    none-->
    <style>
        div{
            width: 100px;
            height:100px;
            border:1px solid red;
            display: inline-block;
        }
        span{
            width: 100px;
            height: 100px;
            border:1px solid red;
            display: inline-block;
        }
    </style>
```
#### 3.float-常用的块级元素和行内元素排列方案
左右浮动 float
float:left;

#### 4.父级边框塌陷的问题
##### 1.增加父级元素的高度

```
#father{
	border:1px #000 solid;
    height:800px
}
```
##### 2.增加一个空的div标签，清除浮动

```
<div class="clear">
</div>
.clear{
	clear:both;fudong
    margin:0;
    padding:0;
}
```
##### 3.overflow
在父级元素中增加一个overflow:hidden;

##### 4.父类添加一个伪类after

```
#father：after{
	content:'';
    display:block;
    clear:both;
}
```
#### 小结：
》1.空div-简单，代码中应该尽量避免
》2.设置父元素的高度-简单，元素有了固定的高度，就会被限制
》3.overflow-下拉菜单的场景避免使用
》4.父类添加一个伪类after-推荐，写法复杂一点，但是没有副作用
5.对比
display
方向不可以控制
float
浮动起来的话会脱离标准文档流，所以要解决父级边框塌陷问题