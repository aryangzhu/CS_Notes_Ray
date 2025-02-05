**网页查看元素时，f12,对值进行修改，查看效果**
![选区_120.png](https://i.loli.net/2021/02/23/xKMAP3wUrHjkFuE.png)

### 1.什么是盒子模型

![选区_119.png](https://i.loli.net/2021/02/23/cXHmESvKBxRgjtr.png)
margin:外边距

padding:内边距-可以填上下左右四个方向

border:边框

两个盒子模型在一起嵌套时,子盒子以父盒子为基准。

注意：在谷歌浏览器中

实际长度=width+左右border+padding

### 2.边框

1.边框的粗细
2.边框的样式
3.边框的颜色
/*border:粗细,样式,颜色*/

### 3.内外边距

```css
#box{
            width: 300px;
            border: 1px solid red;
            选择auto
            margin: auto;
}
form{
            background-color: darkcyan;
}
input{
            border:1px solid black;
}
div:nth-of-type(1){
        	padding:上下边距 左右边距
            padding: 10px 2px;
}
```

### 4.圆角边框

```html
  /*border-radius 顺时针 左上 右上...*/
        /*圆角=半径+边框宽度*/
        div{
            width: 100px;
            height:50px;
            /*border:10px solid red;*/
            background: red;
            border-radius:50px 50px 0 0;
			border-radius:50%;/*也可以使用百分之多少来确定*/
        }
```

### 5.阴影

```
  img{
            border-radius: 500px;
            /*box-shadow:右边阴影边距 下边阴影边距 阴影大小 颜色*/
            box-shadow: 10px 10px 100px yellow;
        }
```
